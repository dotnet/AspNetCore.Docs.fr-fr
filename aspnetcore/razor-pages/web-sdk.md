---
title: Kit de développement logiciel (SDK) Web ASP.NET Core
author: Rick-Anderson
description: Vue d’ensemble de Microsoft. NET. Sdk. Web.
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: 8cc0a51d3d917300f3add31f5884b4784dd573dd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059752"
---
# <a name="aspnet-core-web-sdk"></a>Kit de développement logiciel (SDK) Web ASP.NET Core

### <a name="overview"></a>Vue d’ensemble

`Microsoft.NET.Sdk.Web` est un [Kit de développement logiciel (SDK) de projet MSBuild](/visualstudio/msbuild/how-to-use-project-sdk) pour la génération d’applications ASP.net core. Il est possible de créer une application ASP.NET Core sans ce kit de développement logiciel (SDK), mais le kit de développement logiciel (SDK) Web est le suivant :

* Adapté à la fourniture d’une expérience de première classe.
* Cible recommandée pour la plupart des utilisateurs.

Utilisez le kit de développement logiciel (SDK) Web dans un projet :

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Fonctionnalités activées à l’aide du kit de développement logiciel (SDK) Web :

* Les projets ciblant .NET Core 3,0 ou une version ultérieure référencent implicitement :

  * [Framework partagé ASP.net Core](xref:fundamentals/metapackage-app).
  * [Analyseurs](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) conçus pour générer des applications ASP.net core.
* Le kit de développement logiciel (SDK) Web importe les cibles MSBuild qui permettent l’utilisation de profils de publication et la publication à l’aide de WebDeploy.

### <a name="properties"></a>Propriétés

| Propriété | Description |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Désactive la référence implicite à l' `Microsoft.AspNetCore.App` infrastructure partagée. |
| `DisableImplicitAspNetCoreAnalyzers` | Désactive la référence implicite aux analyseurs de ASP.NET Core. |
| `DisableImplicitComponentsAnalyzers` | Désactive la référence implicite aux Razor analyseurs de composants lors de la génération d' Blazor applications (serveur). |