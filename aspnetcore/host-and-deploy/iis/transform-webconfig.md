---
title: Transformer web.config
author: rick-anderson
description: Découvrez comment transformer le fichier web.config lors de la publication d’une application ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: d264aaee7889ec1c8ee0fe6b1f52ccc4cf355745
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854624"
---
# <a name="transform-webconfig"></a>Transformer web.config

Par [Vijay Ramakrishnan](https://github.com/vijayrkn)

Les transformations du fichier *web.config* peuvent être appliquées automatiquement lorsqu’une application est publiée en fonction de :

* [Configuration de build](#build-configuration)
* [Profil](#profile)
* [Environment](#environment)
* [Personnalisée](#custom)

Ces transformations se produisent pour l’un des scénarios de génération *web.config* suivants :

* Généré automatiquement par le SDK `Microsoft.NET.Sdk.Web`.
* Fourni par le développeur dans la [racine de contenu](xref:fundamentals/index#content-root) de l’application.

## <a name="build-configuration"></a>Configuration de build

Les transformations de la configuration de build sont exécutées en premier.

Incluez un fichier *web.{CONFIGURATION}.config* pour chaque [configuration de build (Déboguer|Mettre en production)](/dotnet/core/tools/dotnet-publish#options) nécessitant une transformation *web.config*.

Dans l’exemple suivant, une variable d’environnement propre à la configuration est définie dans *web.Release.config* :

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

La transformation est appliquée lorsque la configuration est définie sur *Version* :

```dotnetcli
dotnet publish --configuration Release
```

La propriété MSBuild pour la configuration est `$(Configuration)`.

## <a name="profile"></a>Profil

Les transformations du profil sont exécutées ensuite, après les transformations de la [configuration de build](#build-configuration).

Incluez un fichier *web.{PROFILE}.config* pour chaque configuration de profil nécessitant une transformation *web.config*.

Dans l’exemple suivant, une variable d’environnement propre au profil est définie dans *web.FolderProfile.config* pour un profil de publication de dossier :

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

La transformation est appliquée lorsque le profil est *FolderProfile* :

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

La propriété MSBuild pour le nom du profil est `$(PublishProfile)`.

Si aucun profil n’est passé, le nom du profil par défaut est **FileSystem** et *web. FileSystem.config* est appliqué si le fichier est présent dans la racine du contenu de l’application.

## <a name="environment"></a>Environnement

Les transformations de l’environnement sont exécutées en troisième place, après les transformations de la [configuration de build](#build-configuration) et du [profil](#profile).

Incluez un fichier *web.{ENVIRONMENT}.config* pour chaque [environnement](xref:fundamentals/environments) nécessitant une transformation *web.config*.

Dans l’exemple suivant, une variable d’environnement spécifique à l’environnement est définie dans *web.Production.config* pour l’environnement de production :

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

La transformation est appliquée lorsque l’environnement est *Production* :

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

La propriété MSBuild pour l’environnement est `$(EnvironmentName)`.

Lors de la publication à partir de Visual Studio et à l’aide d’un profil de publication, consultez <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.

La variable d’environnement `ASPNETCORE_ENVIRONMENT` est automatiquement ajoutée au fichier *web.config* lorsque le nom de l’environnement est spécifié.

## <a name="custom"></a>Custom

Les transformations personnalisées sont exécutées en dernier, après les transformations de la [configuration de build](#build-configuration), du [profil](#profile) et de [l’environnement](#environment).

Incluez un fichier *{CUSTOM_NAME}.transform* pour chaque configuration personnalisée nécessitant une transformation *web.config*.

Dans l’exemple suivant, une variable d’environnement de transformation personnalisée est définie dans *custom.transform* :

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

La transformation est appliquée lorsque la propriété `CustomTransformFileName` est passée à la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) :

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

La propriété MSBuild pour le nom du profil est `$(CustomTransformFileName)`.

## <a name="prevent-webconfig-transformation"></a>Empêcher la transformation web.config

Pour empêcher les transformations du fichier *web.config*, définissez la propriété MSBuild `$(IsWebConfigTransformDisabled)` :

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Syntaxe de transformation d'un fichier Web.config pour le déploiement d'un projet d'application Web](/previous-versions/dd465326(v=vs.100))
* [Syntaxe de transformation d'un fichier Web.config pour le déploiement d'un projet Web à l'aide de Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))
