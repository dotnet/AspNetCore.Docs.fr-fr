---
title: Déployer une application sur App Service-DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Déployez une application ASP.NET Core sur Azure App Service, la première étape pour DevOps avec ASP.NET Core et Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-azurecli
ms.date: 10/24/2018
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
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: f1c7acba0b7fb7dc07da576b188e580328ff4b89
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96901156"
---
# <a name="deploy-an-app-to-app-service"></a>Déployer une application sur App Service

[Azure App service](/azure/app-service/) est la plateforme d’hébergement Web d’Azure. Le déploiement d’une application Web sur Azure App Service peut être effectué manuellement ou à l’aide d’un processus automatisé. Cette section du guide décrit les méthodes de déploiement qui peuvent être déclenchées manuellement ou par script à l’aide de la ligne de commande, ou déclenchées manuellement à l’aide de Visual Studio.

Dans cette section, vous allez effectuer les tâches suivantes :

* Téléchargez et générez l’exemple d’application.
* Créez une application Web Azure App Service à l’aide de l’Azure Cloud Shell.
* Déployez l’exemple d’application sur Azure à l’aide de git.
* Déployer une modification apportée à l’application à l’aide de Visual Studio.
* Ajoutez un emplacement intermédiaire à l’application Web.
* Déployez une mise à jour sur l’emplacement intermédiaire.
* Procédez à l’échange des emplacements de préproduction et de production.

## <a name="download-and-test-the-app"></a>Télécharger et tester l’application

L’application utilisée dans ce guide est une application ASP.NET Core pré-générée, un [lecteur de flux simple](https://github.com/Azure-Samples/simple-feed-reader/). Il s’agit d’une Razor application pages qui utilise l' `Microsoft.SyndicationFeed.ReaderWriter` API pour récupérer un flux RSS/Atom et afficher les éléments d’actualité dans une liste.

N’hésitez pas à examiner le code, mais il est important de comprendre qu’il n’y a rien de spécial sur cette application. Il s’agit simplement d’une simple ASP.NET Core application à des fins d’illustration.

À partir d’une interface de commande, téléchargez le code, générez le projet et exécutez-le comme suit.

> *Remarque : les utilisateurs Linux/macOS doivent apporter les modifications appropriées pour les chemins d’accès, par exemple, à l’aide de la barre oblique ( `/` ) au lieu de la barre oblique inverse ( `\` ).*

1. Clonez le code dans un dossier sur votre ordinateur local.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Remplacez votre dossier de travail par le dossier *simple-Feed-Reader* qui a été créé.

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. Restaurez les packages, puis générez la solution.

    ```dotnetcli
    dotnet build
    ```

4. Exécutez l'application.

    ```dotnetcli
    dotnet run
    ```

    ![La commande dotnet Run est réussie](./media/deploying-to-app-service/dotnet-run.png)

5. Ouvrez un navigateur et accédez à `http://localhost:5000`. L’application vous permet de taper ou de coller une URL de flux de syndication et d’afficher une liste d’éléments d’actualité.

     ![Application affichant le contenu d’un flux RSS](./media/deploying-to-app-service/app-in-browser.png)

6. Une fois que vous êtes satisfait du bon fonctionnement de l’application, arrêtez-la en appuyant sur **CTRL** + **C** dans l’interface de commande.

## <a name="create-the-azure-app-service-web-app"></a>Créer l’application Web Azure App Service

Pour déployer l’application, vous devez créer une [application Web](/azure/app-service/app-service-web-overview)App service. Après la création de l’application Web, vous allez le déployer à partir de votre ordinateur local à l’aide de git.

1. Connectez-vous au [Azure Cloud Shell](https://shell.azure.com/bash). Remarque : quand vous vous connectez pour la première fois, Cloud Shell vous invite à créer un compte de stockage pour les fichiers de configuration. Acceptez les valeurs par défaut ou fournissez un nom unique.

2. Utilisez l’Cloud Shell pour les étapes suivantes.

    a. Déclarez une variable pour stocker le nom de votre application Web. Le nom doit être unique pour être utilisé dans l’URL par défaut. L’utilisation de la `$RANDOM` fonction bash pour construire le nom garantit l’unicité et les résultats au format `webappname99999` .

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Créez un groupe de ressources. Les groupes de ressources fournissent un moyen d’agréger des ressources Azure à gérer en tant que groupe.

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    La `az` commande appelle la [Azure CLI](/cli/azure/). L’interface CLI peut être exécutée localement, mais son utilisation dans le Cloud Shell permet de gagner du temps et de la configuration.

    c. Créez un plan de App Service dans le niveau S1. Un plan de App Service est un regroupement d’applications Web qui partagent le même niveau tarifaire. Le niveau S1 n’est pas gratuit, mais il est requis pour la fonctionnalité d’emplacements intermédiaires.

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Créez la ressource d’application Web à l’aide du plan de App Service dans le même groupe de ressources.

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Définissez les informations d’identification de déploiement. Ces informations d’identification de déploiement s’appliquent à toutes les applications Web de votre abonnement. N’utilisez pas de caractères spéciaux dans le nom d’utilisateur.

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Configurez l’application Web pour accepter les déploiements à partir du Git local et afficher l' *URL de déploiement git*. **Notez cette URL pour référence ultérieure**.

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. Affichez l’URL de l' *application Web*. Accédez à cette URL pour voir l’application Web vide. **Notez cette URL pour référence ultérieure**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. À l’aide d’un interpréteur de commandes sur votre ordinateur local, accédez au dossier du projet de l’application Web (par exemple, `.\simple-feed-reader\SimpleFeedReader` ). Exécutez les commandes suivantes pour configurer Git pour l’envoi vers l’URL de déploiement :

    a. Ajoutez l’URL distante au référentiel local.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Poussez la branche par défaut locale (*Master*) vers la branche par défaut (*Master*) d' *Azure-prod* Remote.

    ```console
    git push azure-prod master
    ```

    Vous serez invité à entrer les informations d’identification de déploiement que vous avez créées précédemment. Observez la sortie dans l’interface de commande. Azure génère l’application ASP.NET Core à distance.

4. Dans un navigateur, accédez à l' *URL de l’application Web* et notez que l’application a été générée et déployée. Des modifications supplémentaires peuvent être validées dans le référentiel Git local avec `git commit` . Ces modifications sont transmises à Azure avec la `git push` commande précédente.

## <a name="deployment-with-visual-studio"></a>Déploiement avec Visual Studio

> *Remarque : cette section s’applique uniquement à Windows. Les utilisateurs de Linux et macOS doivent apporter la modification décrite à l’étape 2 ci-dessous. Enregistrez le fichier et validez la modification dans le référentiel local avec `git commit` . Enfin, envoyez la modification avec `git push` , comme dans la première section.*

L’application a déjà été déployée à partir de l’interface de commande. Nous allons utiliser les outils intégrés de Visual Studio pour déployer une mise à jour de l’application. En arrière-plan, Visual Studio remplit la même fonction que les outils de ligne de commande, mais dans l’interface utilisateur familière de Visual Studio.

1. Ouvrez *SimpleFeedReader. sln* dans Visual Studio.
2. Dans Explorateur de solutions, ouvrez *Pages\Index.cshtml*. Remplacez `<h2>Simple Feed Reader</h2>` par `<h2>Simple Feed Reader - V2</h2>`.
3. Appuyez sur **CTRL** + **MAJ** + **B** pour générer l’application.
4. Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet et cliquez sur **publier**.

    ![Capture d’écran montrant un clic droit, publier](./media/deploying-to-app-service/publish.png)
5. Visual Studio peut créer une ressource de App Service, mais cette mise à jour sera publiée sur le déploiement existant. Dans la boîte de dialogue **choisir une cible de publication** , sélectionnez **app service** dans la liste à gauche, puis sélectionnez **Sélectionner existant**. Cliquez sur **Publier**.
6. Dans la boîte de dialogue **app service** , vérifiez que le compte Microsoft ou professionnel utilisé pour créer votre abonnement Azure s’affiche dans l’angle supérieur droit. Si ce n’est pas le cas, cliquez sur la liste déroulante et ajoutez-la.
7. Confirmez que l' **abonnement** Azure correct est sélectionné. Pour **Afficher**, sélectionnez **groupe de ressources**. Développez le groupe de ressources **AzureTutorial** , puis sélectionnez l’application Web existante. Cliquez sur **OK**.

    ![Capture d’écran montrant la boîte de dialogue publier App Service](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio génère et déploie l’application sur Azure. Accédez à l’URL de l’application Web. Vérifiez que la `<h2>` modification de l’élément est active.

![Application avec le titre modifié](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Emplacements de déploiement

Les emplacements de déploiement prennent en charge la mise en lots des modifications sans affecter l’exécution de l’application en production. Une fois que la version intermédiaire de l’application est validée par une équipe d’assurance qualité, les emplacements de production et intermédiaires peuvent être permutés. L’application dans l’environnement intermédiaire est promue en production de cette manière. Les étapes suivantes créent un emplacement intermédiaire, y déploient des modifications et échangent l’emplacement intermédiaire avec la production après vérification.

1. Connectez-vous au [Azure Cloud Shell](https://shell.azure.com/bash), s’il n’est pas déjà connecté.
2. Créez l’emplacement intermédiaire.

    a. Créez un emplacement de déploiement avec le nom *intermédiaire*.

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Configurez l’emplacement intermédiaire pour utiliser le déploiement à partir de Git local et récupérez l’URL de déploiement **intermédiaire** . **Notez cette URL pour référence ultérieure**.

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Affichez l’URL de l’emplacement intermédiaire. Accédez à l’URL pour voir l’emplacement de mise en lots vide. **Notez cette URL pour référence ultérieure**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. Dans un éditeur de texte ou Visual Studio, modifiez *pages/index. cshtml* à nouveau afin que l' `<h2>` élément Lise `<h2>Simple Feed Reader - V3</h2>` et enregistre le fichier.

4. Validez le fichier dans le référentiel Git local, à l’aide de la page **modifications** dans l’onglet *Team Explorer* de Visual Studio, ou en entrant la commande suivante à l’aide de l’interface de commande de l’ordinateur local :

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. À l’aide de l’interface de commande de l’ordinateur local, ajoutez l’URL de déploiement intermédiaire en tant que git distant et poussez les modifications validées :

    a. Ajoutez l’URL distante pour la mise en lots dans le référentiel Git local.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Pousser la branche locale par défaut (*Master*) vers la branche par défaut (*Master*) de la succursale *Azure intermédiaire* .

    ```console
    git push azure-staging master
    ```

    Patientez pendant que Azure génère et déploie l’application.

6. Pour vérifier que v3 a été déployé dans l’emplacement intermédiaire, ouvrez deux fenêtres de navigateur. Dans une fenêtre, accédez à l’URL de l’application Web d’origine. Dans l’autre fenêtre, accédez à l’URL de l’application Web intermédiaire. L’URL de production dessert v2 de l’application. L’URL intermédiaire sert v3 à l’application.

    ![Capture d’écran comparaison des fenêtres de navigateur](./media/deploying-to-app-service/ready-to-swap.png)

7. Dans le Cloud Shell, échangez l’emplacement intermédiaire vérifié/chaud en production.

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Vérifiez que l’échange s’est produit en actualisant les deux fenêtres du navigateur.

    ![Comparaison des fenêtres de navigateur après l’échange](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Résumé

Dans cette section, les tâches suivantes ont été effectuées :

* Téléchargez et construisez l’exemple d’application.
* Création d’une application Web Azure App Service à l’aide du Azure Cloud Shell.
* Déploiement de l’exemple d’application sur Azure à l’aide de git.
* Déploiement d’une modification apportée à l’application à l’aide de Visual Studio.
* Ajout d’un emplacement intermédiaire à l’application Web.
* Une mise à jour a été déployée sur l’emplacement intermédiaire.
* Échange des emplacements intermédiaires et de production.

Dans la section suivante, vous apprendrez à créer un pipeline DevOps avec Azure Pipelines.

## <a name="additional-reading"></a>Documentation supplémentaire

* [Vue d’ensemble des applications web](/azure/app-service/app-service-web-overview)
* [Créer une application Web .NET Core et SQL Database dans Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Configurer les informations d’identification de déploiement pour Azure App Service](/azure/app-service/app-service-deployment-credentials)
* [Configurer des environnements intermédiaires dans Azure App Service](/azure/app-service/web-sites-staged-publishing)
