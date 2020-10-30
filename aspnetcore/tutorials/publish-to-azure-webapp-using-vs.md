---
title: Publier une application ASP.NET Core sur Azure avec Visual Studio
author: rick-anderson
description: Découvrez comment publier une application ASP.NET Core sur Azure App Service à l’aide de Visual Studio.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 817169503a80a771354e32123d65ba2bf388aa2d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060220"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publier une application ASP.NET Core sur Azure avec Visual Studio

Par [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Consultez [publier une application Web sur Azure App service à l’aide de Visual Studio pour Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) si vous travaillez sur MacOS.

Pour résoudre un problème de déploiement App Service, consultez <xref:test/troubleshoot-azure-iis>.

## <a name="set-up"></a>Configurer

* Ouvrez un [compte Azure gratuit](https://azure.microsoft.com/free/dotnet/) si vous n’en avez pas. 

## <a name="create-a-web-app"></a>Créer une application web

Dans la page de démarrage de Visual Studio, sélectionnez **Fichier > Nouveau > Projet...**

![Menu Fichier](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Renseignez la boîte de dialogue **Nouveau projet** :

* Sélectionnez **Application web ASP.NET Core** .
* Sélectionnez **Suivant** .

![Boîte de dialogue Nouveau projet](publish-to-azure-webapp-using-vs/_static/new_prj.png)

Dans la boîte de dialogue **Nouvelle application web ASP.NET Core** :

* Sélectionnez **application Web** .
* Sélectionnez **modifier** sous authentification.

![Boîte de dialogue Nouveau ASP.NET Core Web](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

La boîte de dialogue **Modifier l’authentification** s’affiche. 

* Sélectionnez **Comptes d’utilisateur individuels** .
* Sélectionnez **OK** pour revenir à la **nouvelle application Web ASP.net Core** , puis sélectionnez **créer** .

![Boîte de dialogue Nouvelle application web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio crée la solution.

## <a name="run-the-app"></a>Exécuter l’application

* Appuyez sur CTRL+F5 pour exécuter le projet.
* Testez le lien de **confidentialité** .

![Application web ouverte dans Microsoft Edge sur localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Inscrire un utilisateur

* Sélectionnez **S’inscrire** , puis inscrivez un nouvel utilisateur. Vous pouvez utiliser une adresse e-mail fictive. Quand vous effectuez l’envoi, la page affiche l’erreur suivante :

    *«Une opération de base de données a échoué lors du traitement de la demande. L’application de migrations existantes pour le contexte de la base de la base de la base de de l’application peut résoudre ce problème*
* Sélectionnez **Appliquer les migrations** , puis, une fois la page mise à jour, actualisez-la.

![Une opération de base de données a échoué lors du traitement de la requête. Appliquer des migrations existantes pour le contexte de base de données d’application peut résoudre ce problème.](publish-to-azure-webapp-using-vs/_static/mig.png)

L’application affiche l’e-mail utilisé pour inscrire le nouvel utilisateur et un lien de **déconnexion** .

![Application web ouverte dans Microsoft Edge. Le lien S’inscrire est remplacé par le texte Hello user1@example.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Déploiement de l’application dans Azure

Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **publier...** .

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

Dans la boîte de dialogue **Publier** :

* Sélectionnez **Azure** .
* Sélectionnez **Suivant** .

![Boîte de dialogue Publier](publish-to-azure-webapp-using-vs/_static/maas1.png)

Dans la boîte de dialogue **Publier** :

* Sélectionnez **Azure App service (Linux)** .
* Sélectionnez **Suivant** .

![Boîte de dialogue publier : sélectionner un service Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

Dans la boîte de dialogue **publier** , sélectionnez **créer une Azure App service...**

![Boîte de dialogue publier : sélectionner une instance de service Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

La boîte de dialogue **créer un app service** s’affiche :

* Les champs d’entrée **Nom de l’application** , **Groupe de ressources** et **Plan App Service** sont renseignés. Vous pouvez conserver ces noms ou les changer.
* Sélectionnez **Create** (Créer).

![Boîte de dialogue Créer App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

Une fois la création terminée, la boîte de dialogue est fermée automatiquement et la boîte de dialogue **publier** se met à nouveau en place :

* La nouvelle instance qui vient d’être créée est automatiquement sélectionnée.
* Sélectionnez **Terminer** .

![Boîte de dialogue publier : sélectionner une instance de App Service](publish-to-azure-webapp-using-vs/_static/select_as.png)

Vous voyez ensuite la page **Résumé du profil de publication** . Visual Studio a détecté que cette application nécessite une base de données SQL Server et qu’elle vous demande de la configurer. Sélectionnez **Configurer** .

![Page Résumé du profil de publication : configurer la dépendance SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

La boîte de dialogue **configurer la dépendance** s’affiche :

* Sélectionnez **Azure SQL Database** .
* Sélectionnez **Suivant** .

![Boîte de dialogue configurer SQL Server dépendance](publish-to-azure-webapp-using-vs/_static/sql1.png)

Dans la boîte **de dialogue Configurer la base de données SQL Azure** , sélectionnez **créer un SQL Database**

![Boîte de dialogue Configurer Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql2.png)

L' **Azure SQL Database créer** s’affiche :

* Les champs **nom de la base de données** , **groupe de ressources** , **serveur de base de données** et entrée du **plan de App service** sont remplis. Vous pouvez conserver ces valeurs ou les modifier.
* Entrez le **nom d’utilisateur** et le **mot de passe** de l’administrateur de base de données pour le **serveur de base de données** sélectionné (Notez que le compte que vous utilisez doit disposer des autorisations nécessaires pour créer la nouvelle base de données SQL Azure)
* Sélectionnez **Create** (Créer).

![Boîte de dialogue Nouveau Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_create.png)

Une fois la création terminée, la boîte de dialogue est fermée automatiquement et la boîte de dialogue **configurer Azure SQL Database** est à nouveau activée :

* La nouvelle instance qui vient d’être créée est automatiquement sélectionnée.
* Sélectionnez **Suivant** .

![Boîte de dialogue Configurer Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_select.png)

À l’étape suivante de la boîte de dialogue **configurer Azure SQL Database** :

* Entrez les champs **nom d’utilisateur** et **mot de passe de** connexion à la base de données. Il s’agit des détails que votre application utilisera pour se connecter à la base de données au moment de l’exécution. La meilleure pratique consiste à éviter d’utiliser les mêmes détails que le nom d’utilisateur administrateur & mot de passe utilisé à l’étape précédente.
* Sélectionnez **Terminer** .

![Boîte de dialogue Configurer Azure SQL Database, détails de la chaîne de connexion](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

Dans la page **Résumé du profil de publication** , sélectionnez **paramètres** :

![Page Résumé du profil de publication : modifier les paramètres](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

Dans la page **Paramètres** de la boîte de dialogue **Publier** :

* Développez **bases de données** et cochez **la case utiliser cette chaîne de connexion au moment de l’exécution** .
* Développez **Entity Framework migrations** et cochez **appliquer cette migration lors de la publication** .

* Sélectionnez **Enregistrer** . Visual Studio retourne à la boîte de dialogue **Publier** . 

![Boîte de dialogue Publier : panneau Paramètres](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

Cliquez sur **Publier** . Visual Studio publie votre application sur Azure. Quand le déploiement est terminé, l’application est ouverte dans un navigateur.

![Boîte de dialogue Publier : panneau Paramètres](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a>Mettre à jour l’application

* Modifiez la page *pages/index. cshtml* Razor et modifiez son contenu. Par exemple, vous pouvez modifier le paragraphe pour indiquer « Hello ASP.NET Core! » :

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* Sélectionnez à nouveau **publier** dans la page **Résumé du profil de publication** .

![Page Résumé du profil de publication](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* Une fois l’application publiée, vérifiez que les changements apportés sont disponibles sur Azure.

![Vérifiez que la tâche est terminée](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Nettoyage

Après avoir testé l’application, accédez au [portail Azure](https://portal.azure.com/), puis supprimez l’application.

* Sélectionnez **Groupes de ressources** , puis sélectionnez le groupe de ressources que vous avez créé.

![Portail Azure : Groupes de ressources dans le menu de la barre latérale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Dans la page **Groupes de ressources** , sélectionnez **Supprimer** .

![Portail Azure : page Groupes de ressources](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Entrez le nom du groupe de ressources et sélectionnez **supprimer** . Votre application et toutes les autres ressources créées dans ce didacticiel sont désormais supprimées d’Azure.

### <a name="next-steps"></a>Étapes suivantes

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Ressources supplémentaires

* Pour Visual Studio Code, consultez [Profils de publication](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Groupes de ressources Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>