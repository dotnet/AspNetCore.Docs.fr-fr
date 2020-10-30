---
title: Publier une application ASP.NET Core sur Azure avec Visual Studio Code
author: rick-anderson
description: Découvrez comment publier une application ASP.NET Core sur Azure App Service à l’aide de Visual Studio Code.
ms.author: riserrad
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
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: a5a863682655517e27f6540af76342f95d4ecae0
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061260"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Publier une application ASP.NET Core sur Azure avec Visual Studio Code

Par [Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Pour résoudre un problème de déploiement App Service, consultez <xref:test/troubleshoot-azure-iis>.

## <a name="intro"></a>Introduction

Dans ce tutoriel, vous allez apprendre à créer une application ASP.Net Core MVC et à la déployer dans Visual Studio Code.

## <a name="set-up"></a>Configurer

- Ouvrez un [compte Azure gratuit](https://azure.microsoft.com/free/dotnet/) si vous n’en avez pas.
- Installer [Kit SDK .net Core](https://dotnet.microsoft.com/download)
- Installer [Visual Studio Code](https://code.visualstudio.com/Download)
  - Installez l’[extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) dans Visual Studio Code.
  - Installer l' [extension de Azure App service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) pour la Visual Studio code et la configurer avant de continuer

## <a name="create-an-aspnet-core-mvc-project"></a>Créer un projet ASP.NET Core MVC

À l’aide d’un terminal, accédez au dossier dans lequel vous souhaitez créer le projet, puis exécutez la commande suivante :

```dotnetcli
dotnet new mvc
```

Vous aurez une structure de dossier similaire à celle-ci :

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a>Ouvrir le projet avec Visual Studio Code

Une fois le projet créé, vous pouvez l’ouvrir avec Visual Studio Code en utilisant l’une des options ci-dessous :

### <a name="through-the-command-line"></a>Via la ligne de commande

Exécutez la commande suivante dans le dossier où vous avez créé le projet :

```cmd
> code .
```

Si la commande ci-dessous ne fonctionne pas, vérifiez que votre installation est configurée correctement en référençant [ce lien](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).

### <a name="through-visual-studio-code-interface"></a>Via l’interface Visual Studio Code

- Ouvrez Visual Studio Code.
- Dans le menu, sélectionnez `File > Open Folder`.
- Sélectionnez la racine du dossier dans lequel vous avez créé le projet MVC.

Lorsque vous ouvrez le dossier du projet, un message s’affiche, indiquant que les composants nécessaires à la génération et au débogage sont manquants. Acceptez l’aide qui vous est fournie pour les ajouter.

![Interface Visual Studio Code avec le projet chargé](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

Un dossier `.vscode` sera créé sous la structure de projets. Il contiendra les fichiers suivants :

```cmd
launch.json
tasks.json
```

Il s’agit des fichiers d’utilitaire qui vous aident à générer et à déboguer votre application web .NET Core.

## <a name="run-the-app"></a>Exécuter l’application

Avant de déployer l’application sur Azure, vérifiez qu’elle s’exécute correctement sur votre ordinateur local.

- Appuyez sur F5 pour exécuter le projet

Votre application web commence à s’exécuter sous un nouvel onglet de votre navigateur par défaut. Dès le démarrage de l’application, un avertissement lié à la confidentialité peut s’afficher. Cela est dû au fait que votre application démarre soit avec HTTP, soit avec HTTPS, et qu’elle navigue vers le point de terminaison HTTPS par défaut.

![Avertissement de confidentialité pendant le débogage local de l’application](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

Pour conserver la session de débogage, cliquez sur `Advanced`, puis sur `Continue to localhost (unsafe)`.

## <a name="generate-the-deployment-package-locally"></a>Générer le package de déploiement localement

- Ouvrez le terminal Visual Studio Code.
- Utilisez la commande suivante pour générer un package `Release` dans un sous-dossier appelé `publish` :
  - `dotnet publish -c Release -o ./publish`
- Un dossier `publish` sera créé sous la structure de projets.

![Publier la structure du dossier de publication](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Publier sur Azure App Service

Grâce à l’extension Azure App Service pour Visual Studio Code, suivez les étapes ci-dessous pour publier le site web directement sur Azure App Service.

### <a name="if-youre-creating-a-new-web-app"></a>Si vous créez une nouvelle application web

- Cliquez avec le bouton droit sur le dossier `publish` et sélectionnez `Deploy to Web App...`.
- Sélectionnez l’abonnement dans lequel vous souhaitez créer l’application web.
- Sélectionnez `Create New Web App`
- Entrez un nom pour l’application web.

L’extension crée la nouvelle application web et démarre automatiquement le déploiement du package dans l’application. Une fois le déploiement terminé, cliquez sur `Browse Website` pour valider le déploiement.

![Message de déploiement réussi](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

Une fois que vous cliquez sur `Browse Website`, vous y reviendrez à l’aide de votre navigateur par défaut :

![Déploiement réussi de l’application web](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>Si vous effectuez un déploiement dans une application web existante

- Cliquez avec le bouton droit sur le dossier `publish` et sélectionnez `Deploy to Web App...`.
- Sélectionnez l’abonnement où se trouve l’application web existante.
- Sélectionnez l’application web dans la liste.
- Visual Studio Code vous demande si vous souhaitez remplacer le contenu existant. Cliquez sur `Deploy` pour confirmer.

L’extension déploiera le contenu mis à jour dans l’application web. Une fois le déploiement terminé, cliquez sur `Browse Website` pour valider le déploiement.

![Déploiement réussi de l’application web existante](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>Étapes suivantes

- [Créer votre premier pipeline Azure DevOps](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Azure App Service](/azure/app-service/app-service-web-overview)
- [Groupes de ressources Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
