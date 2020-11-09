---
title: Transformer web.config
author: rick-anderson
description: Découvrez comment transformer le fichier web.config lors de la publication d’une application ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: 259b5bf9bf2a6de987494b5771897355e3ea67db
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057310"
---
# <a name="transform-webconfig"></a><span data-ttu-id="911d8-103">Transformer web.config</span><span class="sxs-lookup"><span data-stu-id="911d8-103">Transform web.config</span></span>

<span data-ttu-id="911d8-104">Par [Vijay Ramakrishnan](https://github.com/vijayrkn)</span><span class="sxs-lookup"><span data-stu-id="911d8-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn)</span></span>

<span data-ttu-id="911d8-105">Les transformations du fichier *web.config* peuvent être appliquées automatiquement lorsqu’une application est publiée en fonction de :</span><span class="sxs-lookup"><span data-stu-id="911d8-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="911d8-106">Configuration de build</span><span class="sxs-lookup"><span data-stu-id="911d8-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="911d8-107">Profil</span><span class="sxs-lookup"><span data-stu-id="911d8-107">Profile</span></span>](#profile)
* [<span data-ttu-id="911d8-108">Environment</span><span class="sxs-lookup"><span data-stu-id="911d8-108">Environment</span></span>](#environment)
* [<span data-ttu-id="911d8-109">Personnalisée</span><span class="sxs-lookup"><span data-stu-id="911d8-109">Custom</span></span>](#custom)

<span data-ttu-id="911d8-110">Ces transformations se produisent pour l’un des scénarios de génération *web.config* suivants :</span><span class="sxs-lookup"><span data-stu-id="911d8-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="911d8-111">Généré automatiquement par le SDK `Microsoft.NET.Sdk.Web`.</span><span class="sxs-lookup"><span data-stu-id="911d8-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="911d8-112">Fourni par le développeur dans la [racine de contenu](xref:fundamentals/index#content-root) de l’application.</span><span class="sxs-lookup"><span data-stu-id="911d8-112">Provided by the developer in the [content root](xref:fundamentals/index#content-root) of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="911d8-113">Configuration de build</span><span class="sxs-lookup"><span data-stu-id="911d8-113">Build configuration</span></span>

<span data-ttu-id="911d8-114">Les transformations de la configuration de build sont exécutées en premier.</span><span class="sxs-lookup"><span data-stu-id="911d8-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="911d8-115">Incluez un fichier *web.{CONFIGURATION}.config* pour chaque [configuration de build (Déboguer|Mettre en production)](/dotnet/core/tools/dotnet-publish#options) nécessitant une transformation *web.config* .</span><span class="sxs-lookup"><span data-stu-id="911d8-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="911d8-116">Dans l’exemple suivant, une variable d’environnement propre à la configuration est définie dans *web.Release.config* :</span><span class="sxs-lookup"><span data-stu-id="911d8-116">In the following example, a configuration-specific environment variable is set in *web.Release.config* :</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="911d8-117">La transformation est appliquée lorsque la configuration est définie sur *Version* :</span><span class="sxs-lookup"><span data-stu-id="911d8-117">The transform is applied when the configuration is set to *Release* :</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="911d8-118">La propriété MSBuild pour la configuration est `$(Configuration)`.</span><span class="sxs-lookup"><span data-stu-id="911d8-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="911d8-119">Profil</span><span class="sxs-lookup"><span data-stu-id="911d8-119">Profile</span></span>

<span data-ttu-id="911d8-120">Les transformations du profil sont exécutées ensuite, après les transformations de la [configuration de build](#build-configuration).</span><span class="sxs-lookup"><span data-stu-id="911d8-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="911d8-121">Incluez un fichier *web.{PROFILE}.config* pour chaque configuration de profil nécessitant une transformation *web.config* .</span><span class="sxs-lookup"><span data-stu-id="911d8-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="911d8-122">Dans l’exemple suivant, une variable d’environnement propre au profil est définie dans *web.FolderProfile.config* pour un profil de publication de dossier :</span><span class="sxs-lookup"><span data-stu-id="911d8-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="911d8-123">La transformation est appliquée lorsque le profil est *FolderProfile* :</span><span class="sxs-lookup"><span data-stu-id="911d8-123">The transform is applied when the profile is *FolderProfile* :</span></span>

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="911d8-124">La propriété MSBuild pour le nom du profil est `$(PublishProfile)`.</span><span class="sxs-lookup"><span data-stu-id="911d8-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="911d8-125">Si aucun profil n’est passé, le nom du profil par défaut est **FileSystem** et *web. FileSystem.config* est appliqué si le fichier est présent dans la racine du contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="911d8-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="911d8-126">Environnement</span><span class="sxs-lookup"><span data-stu-id="911d8-126">Environment</span></span>

<span data-ttu-id="911d8-127">Les transformations de l’environnement sont exécutées en troisième place, après les transformations de la [configuration de build](#build-configuration) et du [profil](#profile).</span><span class="sxs-lookup"><span data-stu-id="911d8-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="911d8-128">Incluez un fichier *web.{ENVIRONMENT}.config* pour chaque [environnement](xref:fundamentals/environments) nécessitant une transformation *web.config* .</span><span class="sxs-lookup"><span data-stu-id="911d8-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="911d8-129">Dans l’exemple suivant, une variable d’environnement propre à l’environnement est définie dans *web.Production.config* pour l’environnement de production :</span><span class="sxs-lookup"><span data-stu-id="911d8-129">In the following example, a environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="911d8-130">La transformation est appliquée lorsque l’environnement est *Production* :</span><span class="sxs-lookup"><span data-stu-id="911d8-130">The transform is applied when the environment is *Production* :</span></span>

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="911d8-131">La propriété MSBuild pour l’environnement est `$(EnvironmentName)`.</span><span class="sxs-lookup"><span data-stu-id="911d8-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="911d8-132">Lors de la publication à partir de Visual Studio et à l’aide d’un profil de publication, consultez <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span><span class="sxs-lookup"><span data-stu-id="911d8-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="911d8-133">La variable d’environnement `ASPNETCORE_ENVIRONMENT` est automatiquement ajoutée au fichier *web.config* lorsque le nom de l’environnement est spécifié.</span><span class="sxs-lookup"><span data-stu-id="911d8-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="911d8-134">Custom</span><span class="sxs-lookup"><span data-stu-id="911d8-134">Custom</span></span>

<span data-ttu-id="911d8-135">Les transformations personnalisées sont exécutées en dernier, après les transformations de la [configuration de build](#build-configuration), du [profil](#profile) et de [l’environnement](#environment).</span><span class="sxs-lookup"><span data-stu-id="911d8-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="911d8-136">Incluez un fichier *{CUSTOM_NAME}.transform* pour chaque configuration personnalisée nécessitant une transformation *web.config* .</span><span class="sxs-lookup"><span data-stu-id="911d8-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="911d8-137">Dans l’exemple suivant, une variable d’environnement de transformation personnalisée est définie dans *custom.transform* :</span><span class="sxs-lookup"><span data-stu-id="911d8-137">In the following example, a custom transform environment variable is set in *custom.transform* :</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="911d8-138">La transformation est appliquée lorsque la propriété `CustomTransformFileName` est passée à la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="911d8-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="911d8-139">La propriété MSBuild pour le nom du profil est `$(CustomTransformFileName)`.</span><span class="sxs-lookup"><span data-stu-id="911d8-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="911d8-140">Empêcher la transformation web.config</span><span class="sxs-lookup"><span data-stu-id="911d8-140">Prevent web.config transformation</span></span>

<span data-ttu-id="911d8-141">Pour empêcher les transformations du fichier *web.config* , définissez la propriété MSBuild `$(IsWebConfigTransformDisabled)` :</span><span class="sxs-lookup"><span data-stu-id="911d8-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="911d8-142">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="911d8-142">Additional resources</span></span>

* <span data-ttu-id="911d8-143">[Syntaxe de transformation d'un fichier Web.config pour le déploiement d'un projet d'application Web](/previous-versions/dd465326(v=vs.100))</span><span class="sxs-lookup"><span data-stu-id="911d8-143">[Web.config Transformation Syntax for Web Application Project Deployment](/previous-versions/dd465326(v=vs.100))</span></span>
* <span data-ttu-id="911d8-144">[Syntaxe de transformation d'un fichier Web.config pour le déploiement d'un projet Web à l'aide de Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="911d8-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
