---
title: Déploiement continu sur Azure avec Visual Studio et Git avec ASP.NET Core
author: rick-anderson
description: Découvrez comment créer une application web ASP.NET Core à l’aide de Visual Studio et comment la déployer sur Azure App Service en utilisant Git pour le déploiement continu.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 12/06/2018
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 1485e1a97e51bb4d12d0d74ca1550e5cf18258d8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053785"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a>Déploiement continu sur Azure avec Visual Studio et Git avec ASP.NET Core

Par [Erik Reitan](https://github.com/Erikre)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

Ce tutoriel montre comment créer une application web ASP.NET Core à l’aide de Visual Studio et comment la déployer à partir de Visual Studio sur Azure App Service en utilisant le déploiement continu.

Voir aussi [Créer votre premier pipeline avec Azure Pipelines](/azure/devops/pipelines/get-started-yaml), qui indique comment configurer un flux de travail de livraison continue pour [Azure App Service](/azure/app-service/app-service-web-overview) à l’aide d’Azure DevOps Services. Azure Pipelines (un service Azure DevOps Services) simplifie la configuration d’un pipeline de déploiement fiable pour publier les mises à jour des applications hébergées dans Azure App Service. Le pipeline peut être configuré à partir du portail Azure pour générer, exécuter des tests, déployer sur un emplacement de préproduction, puis déployer en production.

> [!NOTE]
> Pour la réalisation de ce tutoriel, un compte Microsoft Azure est nécessaire. Pour obtenir un compte, [activez les avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) ou [inscrivez-vous pour un essai gratuit](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prérequis

Ce tutoriel suppose que les logiciels suivants sont installés :

* [Visual Studio](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* [Git](https://git-scm.com/downloads) pour Windows

## <a name="create-an-aspnet-core-web-app"></a>Créez une application web ASP.NET Core

1. Démarrez Visual Studio.

1. Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.

1. Sélectionnez le modèle de projet **Application web ASP.NET Core**. Il apparaît sous   >  **modèles** installés  >  **Visual C#**  >  **.net Core**. Nommez le projet `SampleWebAppDemo`. Sélectionnez l’option **Créer un dépôt Git**, puis cliquez sur **OK**.

   ![Boîte de dialogue Nouveau projet](azure-continuous-deployment/_static/01-new-project.png)

1. Dans la boîte de dialogue **Nouveau projet ASP.NET Core**, sélectionnez le modèle ASP.NET Core **Vide**, puis cliquez sur **OK**.

   ![Boîte de dialogue Nouveau projet ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> La dernière version de .NET Core est 2.0.

### <a name="running-the-web-app-locally"></a>Exécution de l’application web localement

1. Une fois que Visual Studio a terminé la création de l’application, exécutez l’application en sélectionnant **Déboguer**  >  **Démarrer le débogage**. Vous pouvez aussi appuyer sur **F5**.

   L’initialisation de Visual Studio et de la nouvelle application peut prendre un certain temps. Une fois qu’elle est terminée, le navigateur affiche l’application en cours d’exécution.

   ![Fenêtre de navigateur montrant l’application en cours d’exécution qui affiche « Hello World! »](azure-continuous-deployment/_static/04-browser-runapp.png)

1. Après avoir examiné l’application web en cours d’exécution, fermez le navigateur et sélectionnez l’icône « Arrêter le débogage » dans la barre d’outils de Visual Studio pour arrêter l’application.

## <a name="create-a-web-app-in-the-azure-portal"></a>Créer une application web dans le portail Azure

Les étapes suivantes permettent de créer une application web dans le portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **NOUVEAU** en haut à gauche de l’interface du portail.

1. Sélectionnez **Web + mobile**  >  **application Web**.

   ![Portail Microsoft Azure : Nouveau bouton Web + Mobile sous Place de marché - Bouton Application Web sous Applications à la une](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. Dans le panneau **Application web**, entrez une valeur unique pour le **Nom App Service**.

   ![Panneau Application web](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > Le nom dans **Nom App Service** doit être unique. Le portail applique cette règle quand le nom est fourni. Si vous indiquez une valeur différente, substituez cette valeur pour chaque occurrence de **SampleWebAppDemo** dans ce tutoriel.

   Dans le panneau **Application web**, sélectionnez un **Plan/emplacement App Service** existant ou créez-en un. Si vous créez un plan, sélectionnez le niveau tarifaire, l’emplacement et les autres options. Pour plus d’informations sur les plans App Service, consultez [Présentation détaillée des plans Azure App Service](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

1. Sélectionnez **Create** (Créer). Azure provisionne et démarre l’application web.

   ![Portail Azure : Panneau Exemple d’application web Demo 01 Essentials](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Activer la publication Git pour la nouvelle application Web

GIT est un système de gestion de versions distribué qui permet de déployer une application web Azure App Service. Le code de l’application web est stocké dans un dépôt Git local, et est déployé sur Azure par le biais d’un envoi (push) vers un dépôt distant.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **App Services** pour afficher la liste des services App Services associées à l’abonnement Azure.

1. Sélectionnez l’application web créée dans la section précédente de ce tutoriel.

1. Dans le panneau **Déploiement**, sélectionnez **Options de déploiement** > **Choisir une source** > **Référentiel Git local**.

   ![Panneau Paramètres : Panneau Source de déploiement : Panneau Choisir une source](azure-continuous-deployment/_static/deployment-options.png)

1. Sélectionnez **OK**.

1. Si les informations d’identification de déploiement pour la publication d’une application web ou d’une autre application App Service n’ont pas été configurées, configurez-les maintenant :

   * Sélectionnez **paramètres**  >  **informations d’identification de déploiement**. Le panneau **Définir les informations d’identification de déploiement** s’affiche.
   * Créez un nom d'utilisateur et un mot de passe. Enregistrez le mot de passe en vue de l’utiliser au moment de la configuration de Git.
   * Sélectionnez **Enregistrer**.

1. Dans le panneau **Application web**, sélectionnez **Paramètres** > **Propriétés**. L’URL du dépôt Git distant destinataire du déploiement est affiché sous **URL Git**.

1. Copiez la valeur **URL GIT** pour pouvoir l’utiliser plus tard dans le didacticiel.

   ![Portail Azure : Panneau Propriétés de l’application](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Publication de l’application web dans Azure App Service

Dans cette section, créez un dépôt Git local à l’aide de Visual Studio, et envoyez (push) de ce dépôt vers Azure pour déployer l’application web. Les étapes impliquées sont les suivantes :

* Ajoutez le paramètre de dépôt distant en utilisant la valeur de l’URL Git, afin que le dépôt local puisse être déployé sur Azure.
* Validez les modifications du projet.
* Envoyez (push) les modifications du projet depuis le dépôt local vers le dépôt distant sur Azure.

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Solution 'SampleWebAppDemo'** et sélectionnez **Valider**. **Team Explorer** s’affiche.

   ![Onglet Connexion de Team Explorer](azure-continuous-deployment/_static/10-team-explorer.png)

1. Dans **Team Explorer**, sélectionnez **Accueil** (icône de maison) > **Paramètres** > **Paramètres du dépôt**.

1. Dans la section **Distants** des **Paramètres du référentiel**, sélectionnez **Ajouter**. La boîte de dialogue **Ajouter un élément distant** s’affiche.

1. Définissez le **Nom** de l’élément distant sur **Azure-SampleApp**.

1. Définissez la valeur de **Récupérer** sur **l’URL Git** précédemment copiée depuis Azure dans ce tutoriel. Notez qu’il s’agit de l’URL qui se termine par **.git**.

   ![Boîte de dialogue Modifier un élément distant](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > Vous pouvez aussi spécifier le dépôt distant à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande**, en accédant au répertoire du projet et en entrant la commande. Exemple :
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. Sélectionnez **Accueil** (icône de maison) > **Paramètres** > **Paramètres globaux**. Vérifiez que le nom et l’adresse e-mail sont définis. Sélectionnez **Mettre à jour** si nécessaire.

1. Sélectionnez   >  **modifications** familiales pour revenir à l’affichage des **modifications** .

1. Entrez un message de validation, comme **Initial Push #1**, et sélectionnez **Valider**. Cette action crée une *validation* localement.

   ![Onglet Connexion de Team Explorer](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > Vous pouvez aussi valider les modifications à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande**, en accédant au répertoire du projet et en entrant les commandes Git. Exemple :
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. Sélectionnez **démarrage** de la  >  **synchronisation**  >  **actions**  >  **ouvrir l’invite de commandes**. L’invite de commandes ouvre le répertoire du projet.

1. Entrez la commande suivante dans la fenêtre Commande :

   `git push -u Azure-SampleApp master`

1. Entrez le mot de passe des **informations d’identification de déploiement** Azure créé précédemment dans Azure.

   Cette commande démarre le processus d’envoi (push) des fichiers de projet locaux vers Azure. La sortie de la commande ci-dessus se termine par un message indiquant que le déploiement a réussi.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > Si une collaboration sur le projet est requise, envisagez un envoi (push) vers [GitHub](https://github.com) avant un envoi (push) vers Azure.
 
### <a name="verify-the-active-deployment"></a>Vérifier le déploiement actif

Vérifiez que le transfert de l’application web à partir de l’environnement local vers Azure a réussi.

Dans le [portail Azure](https://portal.azure.com), sélectionnez l’application web. Sélectionnez   >  **options de déploiement** de déploiement.

![Portail Azure : Panneau Paramètres : Panneau Déploiements montrant la réussite du déploiement](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Exécuter l’application dans Microsoft Azure

L’application web étant déployée sur Azure, exécutez l’application.

Cette opération peut se faire de deux façons :

* Dans le portail Azure, recherchez le panneau lié à l’application web. Sélectionnez **Parcourir** pour afficher l’application dans le navigateur par défaut.
* Ouvrez un navigateur et entrez l’URL de l’application web. Exemple : `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>Mettre à jour l’application web et la republier

Après avoir apporté des modifications au code local, effectuez une republication :

1. Dans **l’Explorateur de solutions** de Visual Studio, ouvrez le fichier *Startup.cs*.

1. Dans la méthode `Configure`, modifiez la méthode `Response.WriteAsync` comme suit :

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. Enregistrez les modifications de *Startup.cs*.

1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur **solution’SampleWebAppDemo'** et sélectionnez **valider**. **Team Explorer** s’affiche.

1. Entrez un message de validation, comme `Update #2`.

1. Appuyez sur le bouton **Valider** pour valider les modifications du projet.

1. Sélectionnez **démarrage**  >    >  **actions** Sync  >  .

> [!NOTE]
> Vous pouvez aussi envoyer (push) les modifications à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande**, en accédant au répertoire du projet et en entrant une commande Git. Exemple :
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Afficher l’application web mise à jour dans Azure

Affichez l’application web mise à jour en sélectionnant **Parcourir** dans le panneau Application web du portail Azure, ou en ouvrant un navigateur et en entrant l’URL de l’application web. Exemple : `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Ressources supplémentaires

* [Créer son premier pipeline avec Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
