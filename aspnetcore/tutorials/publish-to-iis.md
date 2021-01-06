---
title: Publier une application ASP.NET Core sur IIS
author: rick-anderson
description: Découvrez comment héberger une application ASP.NET Core sur un serveur IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: 0f70b5f12b9097f8710c9641404b3e085968fc3f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753151"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>Publier une application ASP.NET Core sur IIS

Ce tutoriel explique comment héberger une application ASP.NET Core sur un serveur IIS.

Ce tutoriel couvre les étapes suivantes :

> [!div class="checklist"]
> * Installer le bundle d’hébergement .NET Core sur Windows Server
> * Créer un site IIS dans le gestionnaire IIS
> * Déployer une application ASP.NET Core

## <a name="prerequisites"></a>Conditions préalables requises

* Installation du [SDK .NET Core](/dotnet/core/sdk) sur l’ordinateur de développement
* Configuration de Windows Server avec le rôle serveur **Serveur Web (IIS)**. Si votre serveur n’est pas configuré pour héberger des sites web avec IIS, suivez les instructions qui se trouvent dans la section *Configuration IIS* de l’article <xref:host-and-deploy/iis/index#iis-configuration>, puis revenez à ce tutoriel.

> [!WARNING]
> **La configuration d’IIS et la sécurité des sites web impliquent des concepts qui ne sont pas abordés dans ce tutoriel.** Avant d’héberger des applications de production sur IIS, consultez la [documentation IIS de Microsoft](https://www.iis.net/) et cet [article ASP.NET Core concernant l’hébergement sur IIS](xref:host-and-deploy/iis/index).
>
> Voici certains scénarios importants concernant l’hébergement sur IIS qui ne sont pas abordés dans ce tutoriel :
>
> * [Création d’une ruche de Registre pour la protection des données ASP.NET Core](xref:host-and-deploy/iis/advanced#data-protection)
> * [Configuration de la liste de contrôle d’accès (ACL) du pool d’applications](xref:host-and-deploy/iis/advanced#application-pool-identity)
> * Pour aborder les concepts de déploiement IIS, ce tutoriel déploie une application pour laquelle aucune sécurité HTTPS n’a été configurée dans IIS. Pour plus d’informations sur l’hébergement d’une application dans laquelle est activé le protocole HTTPS, consultez les rubriques relatives à la sécurité dans la section [Ressources supplémentaires](#additional-resources) de cet article. Vous trouverez des informations supplémentaires sur l’hébergement d’applications ASP.NET Core dans l’article <xref:host-and-deploy/iis/index>.

## <a name="install-the-net-core-hosting-bundle"></a>Installer le bundle d’hébergement .NET Core

Installez le *bundle d’hébergement .NET Core* sur le serveur IIS. L’offre groupée installe le Runtime .NET Core, la bibliothèque .NET Core et le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module). Le module permet aux applications ASP.NET Core de s’exécuter derrière IIS.

Téléchargez le programme d’installation à l’aide du lien suivant :

[Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. Exécutez le programme d’installation sur le serveur IIS.

1. Redémarrez le serveur ou exécutez `net stop was /y` suivi `net start w3svc` de dans un interpréteur de commandes.

## <a name="create-the-iis-site"></a>Créer le site IIS

1. Sur le serveur IIS, créez un dossier pour contenir les fichiers et dossiers publiés de l’application. À l’étape suivante, le chemin du dossier est fourni à IIS en tant que chemin d’accès physique à l’application. Pour plus d’informations sur le dossier de déploiement et la disposition d’un fichier d’une application, consultez <xref:host-and-deploy/directory-structure>.

1. Dans le gestionnaire des services Internet, ouvrez le nœud du serveur dans le panneau **connexions** . Cliquez avec le bouton de droite sur le dossier **Sites**. Sélectionnez **Ajouter un site Web** dans le menu contextuel.

1. Spécifiez le **Nom du site** et définissez le **Chemin physique** sur le dossier de déploiement de l’application que vous avez créé. Spécifiez la configuration **Liaison** et créez le site web en sélectionnant **OK**.

   > [!WARNING]
   > Les liaisons génériques de niveau supérieur (`http://*:80/` et `http://+:80`) ne doivent **pas** être utilisées. Les liaisons génériques de niveau supérieur peuvent exposer votre application à des failles de sécurité. Cela s’applique aux caractères génériques forts et faibles. Utilisez des noms d’hôte explicites plutôt que des caractères génériques. Une liaison générique de sous-domaine (par exemple, `*.mysub.com`) ne présente pas ce risque de sécurité si vous contrôlez le domaine parent en entier (par opposition à `*.com`, qui est vulnérable). Consultez la [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pour plus d’informations.

1. Vérifiez que l’identité de modèle de processus dispose des autorisations appropriées.

   Si l’identité par défaut du pool d’applications (**modèle de processus**  >  **Identity** ) est remplacée par `ApplicationPoolIdentity` une autre identité, vérifiez que la nouvelle identité dispose des autorisations nécessaires pour accéder au dossier de l’application, à la base de données et à d’autres ressources requises. Par exemple, le pool d’applications nécessite l’accès en lecture et en écriture aux dossiers dans lesquels l’application lit et écrit des fichiers.

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a>Créer une Razor application ASP.net Core pages

Suivez le <xref:getting-started> didacticiel pour créer une Razor application pages.

## <a name="publish-and-deploy-the-app"></a>Publier et déployer l’application

*Publier une application* signifie que vous créez une application compilée qui peut être hébergée par un serveur. *Déployer une application* signifie que vous déplacez l’application publiée vers un système d’hébergement. L’étape de publication est gérée par le [SDK .NET Core](/dotnet/core/sdk), alors que l’étape de déploiement peut être gérée à l’aide de différentes méthodes. Ce tutoriel utilise la méthode de déploiement par *dossier*, dans laquelle :
 
* L’application est publiée dans un dossier.
* Le contenu du dossier est déplacé vers le dossier du site IIS (le **chemin physique** du site dans le gestionnaire IIS).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis sélectionnez **Publier**.
1. Dans la boîte de dialogue **Choisir une cible de publication**, sélectionnez l’option de publication **Dossier**.
1. Configurez un chemin pour **Dossier ou partage de fichiers**.
   * Si vous avez créé un dossier pour le site IIS qui est disponible sur l’ordinateur de développement en tant que partage réseau, indiquez le chemin de ce partage. L’utilisateur actuel doit disposer d’un accès en écriture pour publier des données sur le partage.
   * Si vous ne parvenez pas à effectuer le déploiement directement dans le dossier site IIS sur le serveur IIS, publiez-le dans un dossier sur un support amovible et déplacez physiquement l’application publiée dans le dossier du site IIS sur le serveur, qui est le **chemin d’accès physique** du site dans le gestionnaire des services Internet. Déplacez le contenu du `bin/Release/{TARGET FRAMEWORK}/publish` dossier vers le dossier site IIS sur le serveur, qui est le **chemin d’accès physique** du site dans le gestionnaire des services Internet.
1. Cliquez sur le bouton **Publier**.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

1. Dans une invite de commande, publiez l’application en configuration Release avec la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) :

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. Déplacez le contenu du `bin/Release/{TARGET FRAMEWORK}/publish` dossier vers le dossier site IIS sur le serveur, qui est le **chemin d’accès physique** du site dans le gestionnaire des services Internet.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Cliquez avec le bouton droit sur le projet dans **Solution**, puis sélectionnez **Publier** > **Publier sur un dossier**.
1. Configurez le chemin **Choisir un dossier**.
   * Si vous avez créé un dossier pour le site IIS qui est disponible sur l’ordinateur de développement en tant que partage réseau, indiquez le chemin de ce partage. L’utilisateur actuel doit disposer d’un accès en écriture pour publier des données sur le partage.
   * Si vous ne parvenez pas à effectuer le déploiement directement dans le dossier du site IIS sur le serveur IIS, publiez l’application dans un dossier situé sur un support amovible et déplacez physiquement l’application publiée vers le dossier du site IIS sur le serveur, qui correspond au **chemin physique** du site dans le gestionnaire IIS. Déplacez le contenu du `bin/Release/{TARGET FRAMEWORK}/publish` dossier vers le dossier site IIS sur le serveur, qui est le **chemin d’accès physique** du site dans le gestionnaire des services Internet.
1. Cliquez sur le bouton **Publier**.

---

## <a name="browse-the-website"></a>Parcourir le site Web

L’application est accessible dans un navigateur après avoir reçu la première requête. Envoyez une requête à l’application au niveau de la liaison de point de terminaison que vous avez établie dans le gestionnaire IIS pour le site.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Installer le bundle d’hébergement .NET Core sur Windows Server
> * Créer un site IIS dans le gestionnaire IIS
> * Déployer une application ASP.NET Core

Pour plus d’informations sur l’hébergement des applications ASP.NET Core sur IIS, consultez l’article de présentation d’IIS :

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>Ressources supplémentaires

### <a name="articles-in-the-aspnet-core-documentation-set"></a>Articles de la documentation ASP.NET Core

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>Articles relatifs au déploiement d’applications ASP.NET Core

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Publier une application web sur un dossier à l’aide de Visual Studio pour Mac](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>Articles sur la configuration HTTPS IIS

* [Configuration du protocole SSL dans le gestionnaire IIS](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [Configuration du protocole SSL sur IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>Articles sur IIS et Windows Server

* [Site officiel de Microsoft IIS](https://www.iis.net/)
* [Bibliothèque de contenu technique Windows Server](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a>Déploiement de ressources pour les administrateurs IIS

* [Documentation IIS](/iis)
* [Bien démarrer avec le Gestionnaire IIS dans IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Déploiement d’applications .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

