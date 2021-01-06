---
title: Profils de publication Visual Studio (. pubxml) pour le déploiement d’applications ASP.NET Core
author: rick-anderson
description: Découvrez comment créer des profils de publication dans Visual Studio et les utiliser pour gérer les déploiements d’applications ASP.NET Core sur différentes cibles.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
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
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: eae4a19042efded03f10e9ebd17122232f0323eb
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854637"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>Profils de publication Visual Studio (. pubxml) pour le déploiement d’applications ASP.NET Core

De [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) et [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce document traite de l’utilisation de Visual Studio 2019 ou supérieur pour créer et utiliser des profils de publication. Les profils de publication créés avec Visual Studio peuvent être utilisés avec MSBuild et Visual Studio. Pour obtenir des instructions sur la publication sur Azure, consultez <xref:tutorials/publish-to-azure-webapp-using-vs>.

La `dotnet new mvc` commande génère un fichier projet contenant l' [ \<Project> élément](/visualstudio/msbuild/project-element-msbuild)de niveau racine suivant :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

L’attribut `Sdk` de l’élément `<Project>` précédent importe les [propriétés](/visualstudio/msbuild/msbuild-properties) et [cibles](/visualstudio/msbuild/msbuild-targets) de MSBuild à partir de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* et *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectivement. L’emplacement par défaut de `$(MSBuildSDKsPath)` (avec Visual Studio 2019 Enterprise) est le dossier *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks*.

`Microsoft.NET.Sdk.Web`([Kit de développement logiciel (SDK) Web](xref:razor-pages/web-sdk)) dépend d’autres kits de développement logiciel (SDK), y compris `Microsoft.NET.Sdk` ([Kit SDK .net Core](/dotnet/core/project-sdk/msbuild-props)) et `Microsoft.NET.Sdk.Razor` ([ Razor SDK](xref:razor-pages/sdk)). Les propriétés et cibles MSBuild associées à chaque kit de développement logiciel sont importées. Les cibles de publication importent le bon ensemble de cibles en fonction de la méthode de publication utilisée.

Quand MSBuild ou Visual Studio charge un projet, les actions principales suivantes se produisent :

* Génération du projet
* Calcul des fichiers à publier
* Publication des fichiers sur la destination

## <a name="compute-project-items"></a>Calcul des éléments du projet

Quand le projet est chargé, les [éléments du projet MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (fichiers) sont calculés. Le type d’élément détermine la façon dont le fichier est traité. Par défaut, les fichiers *.cs* sont inclus dans la liste d’éléments `Compile`. Les fichiers dans la liste d’éléments `Compile` sont compilés.

La liste d’éléments `Content` contient des fichiers qui sont publiés en plus des sorties de génération. Par défaut, les fichiers qui correspondent aux modèles `wwwroot\**`, `**\*.config` et `**\*.json` sont inclus dans l’élément `Content`. Par exemple, le  [modèle d’utilisation des caractères génériques](https://gruntjs.com/configuring-tasks#globbing-patterns)`wwwroot\**` correspond à tous les fichiers dans le dossier *wwwroot* et ses sous-dossiers.

::: moniker range=">= aspnetcore-3.0"

Le [Kit de développement logiciel (SDK) Web](xref:razor-pages/web-sdk) importe le [ Razor SDK](xref:razor-pages/sdk). Par conséquent, les fichiers correspondant aux modèles `**\*.cshtml` et `**\*.razor` sont également inclus dans la liste d’éléments `Content`.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Le [Kit de développement logiciel (SDK) Web](xref:razor-pages/web-sdk) importe le [ Razor SDK](xref:razor-pages/sdk). Par conséquent, les fichiers correspondant au modèle `**\*.cshtml` sont également inclus dans la liste d’éléments `Content`.

::: moniker-end

Pour ajouter explicitement un fichier à la liste de publication, ajoutez-le directement au fichier *.csproj* comme indiqué dans la section [Inclure des fichiers](#include-files).

Quand vous sélectionnez le bouton **Publier** dans Visual Studio ou quand vous publiez à partir de la ligne de commande :

* Les éléments/propriétés sont calculés (il s’agit des fichiers nécessaires à la génération).
* **Visual Studio uniquement**: les packages NuGet sont restaurés. (La restauration doit être explicite par l’utilisateur sur l’interface CLI.)
* Le projet est généré.
* Les éléments de publication sont calculés (il s’agit des fichiers nécessaires à la publication).
* Le projet est publié (les fichiers calculés sont copiés sur la destination de publication).

Quand un projet ASP.NET Core référence `Microsoft.NET.Sdk.Web` dans le fichier projet, un fichier *app_offline.htm* est placé à la racine du répertoire des applications web. Quand le fichier est présent, le module ASP.NET Core ferme l’application normalement et sert le fichier *app_offline.htm* pendant le déploiement. Pour plus d’informations, consultez les [Informations de référence sur la configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).

## <a name="basic-command-line-publishing"></a>Publication de base à partir d’une ligne de commande

La publication à partir d’une ligne de commande fonctionne sur toutes les plateformes .NET Core prises en charge et ne nécessite pas Visual Studio. Dans les exemples suivants, la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) de l’interface CLI .NET Core est exécutée à partir du répertoire de projet (qui contient le fichier *.csproj*). Si le dossier du projet n’est pas le répertoire de travail actuel, passez explicitement le chemin du fichier projet. Par exemple :

```dotnetcli
dotnet publish C:\Webs\Web1
```

Exécutez les commandes suivantes pour créer et publier une application web :

```dotnetcli
dotnet new mvc
dotnet publish
```

La commande `dotnet publish` produit une variante de la sortie suivante :

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

Le format par défaut du dossier de publication est *bin\Debug\\{MONIKER DU FRAMEWORK CIBLE}\publish\\*. Par exemple, *bin\Debug\netcoreapp2.2\publish\\*.

La commande suivante spécifie une build `Release` et le répertoire de publication :

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

La commande `dotnet publish` appelle MSBuild, qui appelle la cible de `Publish`. Les paramètres passés à `dotnet publish` sont passés à MSBuild. Les paramètres `-c` et `-o` correspondent aux propriétés `Configuration` et `OutputPath` de MSBuild, respectivement.

Les propriétés MSBuild peuvent être passées à l’aide de l’un des formats suivants :

* `-p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Par exemple, la commande suivante publie une build `Release` sur un partage réseau. Le partage réseau est spécifié avec des barres obliques (*//r8/*) et fonctionne sur toutes les plateformes .NET Core prises en charge.

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

Vérifiez que l’application publiée pour le déploiement n’est pas en cours d’exécution. Les fichiers dans le dossier *publish* sont verrouillés quand l’application est en cours d’exécution. Le déploiement ne peut pas se produire car les fichiers verrouillés ne peuvent pas être copiés.

## <a name="publish-profiles"></a>Profils de publication

Cette section utilise Visual Studio 2019 ou supérieur pour créer un profil de publication. Une fois le profil créé, la publication à partir de Visual Studio ou de la ligne de commande est disponible. Les profils de publication peuvent simplifier le processus de publication, et n’importe quel nombre de profils peut exister.

Créez un profil de publication dans Visual Studio en choisissant l’une des méthodes suivantes :

* Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.
* Sélectionner **Publier {NOM DU PROJET}** dans le menu **Générer**.

L’onglet **Publier** de la page de fonctionnalités de l’application s’affiche. Si le projet n’a pas de profil de publication, la page **Choisir une cible de publication** s’affiche. Vous êtes invité à sélectionner une des cibles de publication suivantes :

* Azure App Service
* Azure App Service sur Linux
* Machines virtuelles Azure
* Dossier
* IIS, FTP, Web Deploy (pour n’importe quel serveur web)
* Profil d’importation

Pour déterminer la cible de publication la plus appropriée, consultez [Quelles sont les meilleures options de publication pour moi](/visualstudio/ide/not-in-toc/web-publish-options).

Quand la cible de publication **Dossier** est sélectionné, spécifiez un chemin de dossier pour stocker les ressources publiées. Le chemin du dossier par défaut est *bin\\{CONFIGURATION DU PROJET}\\{MONIKER DU FRAMEWORK CIBLE}\publish\\*. Par exemple, *bin\Release\netcoreapp2.2\publish\\*. Sélectionnez le bouton **Créer un profil** pour terminer.

Une fois créé le profil de publication, le contenu de l’onglet **Publier** change. Le profil créé apparaît dans une liste déroulante. Dans la liste déroulante, sélectionnez **Créer un profil** pour créer un autre profil.

L’outil de publication de Visual Studio produit un fichier MSBuild *Properties/PublishProfiles/{NOM DU PROFIL}.pubxml* décrivant le profil de publication. Le fichier *.pubxml* :

* Contient les paramètres de configuration de publication et est utilisé par le processus de publication.
* Peut être modifié pour personnaliser le processus de génération et de publication.

Dans le cas d’une publication sur une cible Azure, le fichier *.pubxml* contient l’identificateur de votre abonnement Azure. Avec ce type de cible, nous vous déconseillons d’ajouter ce fichier au contrôle de code source. Dans le cas d’une publication sur une cible non-Azure, nous vous recommandons d’archiver le fichier *.pubxml*.

Les informations sensibles (telles que le mot de passe de publication) sont chiffrées par utilisateur/machine. Elles sont stockées dans le fichier *Properties/PublishProfiles/{NOM DU PROFIL}.pubxml.user*. Ce fichier pouvant stocker des informations sensibles, il ne doit pas être archivé dans le contrôle de code source.

Pour obtenir une vue d’ensemble expliquant comment publier une application web ASP.NET Core, consultez <xref:host-and-deploy/index>. Les tâches et les cibles MSBuild nécessaires pour publier une ASP.NET Core application Web sont Open source dans le [référentiel dotnet/WebSDK](https://github.com/dotnet/websdk).

Les commandes suivantes peuvent utiliser les profils de publication de dossier, MSDeploy et [Kudu](https://github.com/projectkudu/kudu/wiki) . Comme MSDeploy ne prend pas en charge plusieurs plateformes, les options MSDeploy suivantes sont prises en charge uniquement sur Windows.

**Dossier (fonctionne sur plusieurs plateformes) :**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**MSDeploy :**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

**Package MSDeploy :**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

Dans les exemples précédents :

* `dotnet publish` et `dotnet build` prennent en charge les API Kudu pour publier sur Azure à partir de n’importe quelle plateforme. La publication Visual Studio prend en charge les API Kudu, mais avec la prise en charge par WebSDK pour la publication multiplateforme sur Azure.
* Ne pas passer `DeployOnBuild` à la `dotnet publish` commande.

Pour plus d’informations, consultez [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).

Ajoutez un profil de publication au dossier *Properties/PublishProfiles* avec le contenu suivant :

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a>Exemple de publication de dossier

Lors de la publication avec un profil nommé *FolderProfile*, utilisez l’une des commandes suivantes :

```dotnetcli
dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
```

```dotnetcli
dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

```bash
msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

La commande [dotnet build](/dotnet/core/tools/dotnet-build) de l’interface CLI .NET Core appelle `msbuild` pour exécuter les processus de génération et de publication. Les commandes `dotnet build` et `msbuild` sont équivalentes quand vous passez un profil de dossier. Quand vous appelez `msbuild` directement sur Windows, la version MSBuild du .NET Framework est utilisée. L’appel de `dotnet build` sur un profil autre qu’un dossier :

* Appelle `msbuild`, qui utilise MSDeploy.
* Entraîne un échec (même en cas d’exécution sur Windows). Pour publier avec un profil autre qu’un dossier, appelez `msbuild` directement.

Le profil de publication de dossier suivant a été créé avec Visual Studio et publie sur un partage réseau :

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

Dans l’exemple précédent :

* La propriété `<ExcludeApp_Data>` est présente simplement pour satisfaire une exigence de schéma XML. La propriété `<ExcludeApp_Data>` n’a aucun effet sur le processus de publication, même s’il y a un dossier *App_Data* à la racine du projet. Le dossier *App_Data* ne reçoit pas de traitement spécial comme c’est le cas dans les projets ASP.NET 4.x.
* La propriété `<LastUsedBuildConfiguration>` est définie sur `Release`. En cas de publication à partir de Visual Studio, la valeur de `<LastUsedBuildConfiguration>` est définie sur la valeur existante au démarrage du processus de publication. La propriété `<LastUsedBuildConfiguration>` est spéciale et ne doit pas être remplacée dans un fichier MSBuild importé. Cette propriété peut, toutefois, être remplacée à partir de la ligne de commande avec l’une des approches suivantes.
  * À l’aide de CLI .NET Core :

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * À l’aide de MSBuild :

    ```bash
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  Pour plus d’informations, consultez [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) (Comment définir la propriété de configuration).

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Publier sur un point de terminaison MSDeploy à partir de la ligne de commande

L’exemple suivant utilise une application web ASP.NET Core créée par Visual Studio et nommée *AzureWebApp*. Un profil de publication Azure Apps est ajouté avec Visual Studio. Pour plus d’informations sur la création d’un profil, consultez la section [profils de publication](#publish-profiles).

Pour déployer l’application à l’aide d’un profil de publication, exécutez la `msbuild` commande à partir d’une **invite de commandes développeur** Visual Studio. L’invite de commandes est disponible dans le dossier *Visual Studio* du menu **Démarrer** sur la barre des tâches Windows. Pour en faciliter accès, vous pouvez ajouter à l’invite de commandes au menu **Outils** dans Visual Studio. Pour plus d’informations, consultez [invite de commandes développeur pour Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

MSBuild utilise la syntaxe de commande suivante :

```bash
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* `{PATH}`: Chemin d’accès au fichier projet de l’application.
* `{PROFILE}`: Nom du profil de publication.
* `{USERNAME}`: MSDeploy nom d’utilisateur. Le se `{USERNAME}` trouve dans le profil de publication.
* `{PASSWORD}`: Mot de passe MSDeploy. Obtenez le `{PASSWORD}` à partir du *{Profile}. Fichier PublishSettings* . Téléchargez le fichier *.PublishSettings* à partir d’un des emplacements suivants :
  * **Explorateur de solutions**: sélectionnez **Afficher** les  >  **Cloud Explorer**. Connectez-vous avec votre abonnement Azure. Ouvrez **App Services**. Faites un clic droit sur l’application. Sélectionnez **Télecharger un profil de publication**.
  * Portail Azure : sélectionnez **accéder au profil de publication** dans le panneau **vue d’ensemble** de l’application Web.

L’exemple suivant utilise un profil de publication nommé *AzureWebApp - Web Deploy*:

```bash
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Un profil de publication peut également être utilisé avec la commande [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) de l’interface CLI .NET Core à partir d’un interpréteur de commandes Windows :

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> La commande `dotnet msbuild` est une commande multiplateforme pouvant compiler des applications ASP.NET Core sur macOS et Linux. Toutefois, MSBuild sur macOS et Linux n’est pas capable de déployer une application sur Azure ou d’autres points de terminaison MSDeploy.

## <a name="set-the-environment"></a>Définir l’environnement

Incluez la propriété `<EnvironmentName>` dans le profil de facturation (*.pubxml*) ou le fichier projet pour définir [l’environnement](xref:fundamentals/environments) de l’application :

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

Si vous avez besoin de transformations de *web.config* (par exemple, définir les variables d’environnement basées sur la configuration, le profil ou l’environnement), consultez <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="exclude-files"></a>Exclure des fichiers

Lors de la publication d’applications web ASP.NET Core, les ressources suivantes sont incluses :

* Artefacts de build
* Les dossiers et fichiers correspondant aux modèles d’utilisation des caractères génériques suivants :
  * `**\*.config` (par exemple *web.config*)
  * `**\*.json` (par exemple, *appsettings.json* )
  * `wwwroot\**`

MSBuild prend en charge les [modèles d’utilisation des caractères génériques](https://gruntjs.com/configuring-tasks#globbing-patterns). Par exemple, l’élément `<Content>` suivant supprime la copie des fichiers texte (*.txt*) dans le dossier *wwwroot/content* et ses sous-dossiers :

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Vous pouvez ajouter le balisage précédent à un profil de publication ou au fichier *.csproj*. En cas d’ajout au fichier *.csproj*, la règle est ajoutée à tous les profils de publication dans le projet.

L' `<MsDeploySkipRules>` élément suivant exclut tous les fichiers du dossier *wwwroot\content* :

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>` ne supprime pas les cibles *skip* du site de déploiement. Les dossiers et fichiers ciblés par `<Content>` sont supprimés du site de déploiement. Par exemple, supposez qu’une application web déployée avait les fichiers suivants :

* *Views/Home/About1.cshtml*
* *Views/Home/About2.cshtml*
* *Views/Home/About3.cshtml*

Si les éléments `<MsDeploySkipRules>` suivants sont ajoutés, ces fichiers ne sont pas supprimés sur le site de déploiement.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

Les éléments `<MsDeploySkipRules>` précédents empêchent le déploiement des fichiers *skipped*. Ces fichiers ne sont pas supprimés une fois déployés.

L’élément `<Content>` suivant supprime les fichiers ciblés sur le site de déploiement :

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

L’utilisation du déploiement à partir de la ligne de commande avec l’élément `<Content>` précédent génère une variante de la sortie suivante :

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a>Fichiers Include

Les sections suivantes décrivent différentes approches pour l’inclusion de fichier au moment de la publication. La section [général d’inclusion de fichier](#general-file-inclusion) utilise l' `DotNetPublishFiles` élément, qui est fourni par un fichier de cibles de publication dans le [Kit de développement logiciel (SDK) Web](xref:razor-pages/web-sdk). La section d' [inclusion sélective des fichiers](#selective-file-inclusion) utilise l' `ResolvedFileToPublish` élément, qui est fourni par un fichier de cibles de publication dans le [Kit SDK .net Core](/dotnet/core/project-sdk/msbuild-props). Comme le SDK Web dépend du SDK .NET Core, l’élément peut être utilisé dans un projet ASP.NET Core.

### <a name="general-file-inclusion"></a>Inclusion de fichier générale

L’élément `<ItemGroup>` de l’exemple suivant illustre la copie d’un dossier situé en dehors du répertoire de projet dans un dossier du site publié. Les fichiers ajoutés à l’élément `<ItemGroup>` du balisage suivant sont inclus par défaut.

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

Le balisage précédent :

* Vous pouvez l’ajouter au fichier *.csproj* ou au profil de publication. Si vous l’ajoutez au fichier *csproj*, il est inclus dans chaque profil de publication du projet.
* Déclare un élément `_CustomFiles` pour stocker les fichiers qui correspondent au modèle d’utilisation des caractères génériques `Include` de l’attribut. Le dossier d’*images* référencé dans le modèle se trouve en dehors du répertoire de projet. Une [propriété réservée](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), nommée `$(MSBuildProjectDirectory)`, se traduit par le chemin absolu du fichier projet.
* Fournit une liste de fichiers à l’élément `DotNetPublishFiles`. Par défaut, l’élément `<DestinationRelativePath>` de l’élément est vide. La valeur par défaut est remplacée dans le balisage et utilise des [métadonnées d’éléments connus](/visualstudio/msbuild/msbuild-well-known-item-metadata) comme `%(RecursiveDir)`. Le texte interne représente le dossier *wwwroot/images* du site publié.

### <a name="selective-file-inclusion"></a>Inclusion de fichier sélective

Dans l’exemple suivant, le balisage en surbrillance illustre ce qui suit :

* La copie d’un fichier situé en dehors du projet dans le dossier *wwwroot* du site publié. Le nom de fichier *ReadMe2.md* est conservé.
* L’exclusion du dossier *wwwroot\Content*.
* L’exclusion de *Views\Home\About2.cshtml*.

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

L’exemple précédent utilise l’élément `ResolvedFileToPublish`, dont le comportement par défaut consiste à toujours copier les fichiers fournis dans l’attribut `Include` sur le site publié. Remplacez le comportement par défaut en incluant un élément enfant `<CopyToPublishDirectory>` avec le texte interne `Never` ou `PreserveNewest`. Par exemple :

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

Pour obtenir d’autres exemples de déploiement, consultez le [fichier Lisez-moi du SDK Web](https://github.com/dotnet/sdk/tree/master/src/WebSdk).

## <a name="run-a-target-before-or-after-publishing"></a>Exécuter une cible avant ou après la publication

Les cibles intégrées `BeforePublish` et `AfterPublish` exécutent une cible avant ou après la cible de publication. Ajoutez les éléments suivants au profil de publication pour journaliser les messages de console avant et après la publication :

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Publier sur un serveur à l’aide d’un certificat non approuvé

Ajoutez la propriété `<AllowUntrustedCertificate>` avec la valeur `True` au profil de publication :

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Le service Kudu

Pour afficher les fichiers dans un déploiement d’application web Azure App Service, utilisez le [service Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). Ajoutez le jeton `scm` au nom de l’application web. Par exemple :

| URL                                    | Résultats       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Application web      |
| `http://mysite.scm.azurewebsites.net/` | Service Kudu |

Sélectionnez l’élément de menu [Console de débogage](https://github.com/projectkudu/kudu/wiki/Kudu-console) pour afficher, modifier, supprimer ou ajouter des fichiers.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifie le déploiement des applications web et des sites web sur les serveurs IIS.
* [Référentiel github du kit de développement logiciel (SDK) Web](https://github.com/dotnet/websdk/issues): problèmes de fichier et demande de déploiement.
* [Publier une application web ASP.NET sur une machine virtuelle Azure à partir de Visual Studio](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
