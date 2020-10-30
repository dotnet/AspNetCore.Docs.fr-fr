---
title: RazorSDK ASP.net Core
author: Rick-Anderson
description: Découvrez comment Razor les pages de ASP.net Core rendent le codage des scénarios orientés page plus facile et plus productif que l’utilisation de MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
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
uid: razor-pages/sdk
ms.openlocfilehash: d3b01889b7634dce8ef1d6a4886a9a6ac39a6473
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060870"
---
# <a name="aspnet-core-no-locrazor-sdk"></a>RazorSDK ASP.net Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Vue d'ensemble

Le [!INCLUDE[](~/includes/2.1-SDK.md)] comprend le `Microsoft.NET.Sdk.Razor` Kit de développement logiciel (SDK Razor ) MSBuild. Le Razor Kit de développement logiciel :

::: moniker range=">= aspnetcore-3.0"

* Est requis pour générer, empaqueter et publier des projets contenant [Razor](xref:mvc/views/razor) des fichiers pour des projets ASP.net Core basés sur MVC ou [Blazor](xref:blazor/index) .
* Comprend un ensemble de cibles, de propriétés et d’éléments prédéfinis qui permettent de personnaliser la compilation des Razor fichiers ( *. cshtml* ou *. Razor* ).

Le Razor Kit de développement logiciel (SDK) comprend `Content` des éléments dont `Include` les attributs sont définis sur les `**\*.cshtml` `**\*.razor` modèles et globbing. Les fichiers correspondants sont publiés.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Normalise l’expérience de la création, de l’empaquetage et de la publication de projets contenant [Razor](xref:mvc/views/razor) des fichiers pour ASP.net Core projets basés sur Mvc.
* Comprend un ensemble de cibles, de propriétés et d’éléments prédéfinis qui permettent de personnaliser la compilation des Razor fichiers.

Le Razor Kit de développement logiciel (SDK) comprend un `Content` élément dont `Include` l’attribut a pour valeur le `**\*.cshtml` modèle globbing. Les fichiers correspondants sont publiés.

::: moniker-end

## <a name="prerequisites"></a>Prérequis

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-no-locrazor-sdk"></a>Utiliser le Razor Kit de développement logiciel

La plupart des applications Web ne sont pas requises pour référencer explicitement le Razor Kit de développement logiciel (SDK).

::: moniker range=">= aspnetcore-3.0"

Pour utiliser le Razor Kit de développement logiciel (SDK) afin de générer des bibliothèques de classes contenant des Razor vues ou des Razor pages, nous vous recommandons de commencer avec le Razor modèle de projet Bibliothèque de classes (RCL). Un RCL utilisé pour générer Blazor des fichiers ( *. Razor* ) au minimum requiert une référence au package [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . Un RCL qui est utilisé pour créer des Razor vues ou des pages (fichiers *. cshtml* ) nécessite `netcoreapp3.0` au minimum le ciblage ou une version ultérieure, et un `FrameworkReference` vers le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) dans son fichier projet.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Pour utiliser le Razor Kit de développement logiciel (SDK) pour créer des bibliothèques de classes contenant des Razor vues ou des Razor pages :

* Utilisez `Microsoft.NET.Sdk.Razor` à la place de `Microsoft.NET.Sdk` :

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* En règle générale, une référence de package à `Microsoft.AspNetCore.Mvc` est requise pour recevoir des dépendances supplémentaires qui sont requises pour générer et compiler des Razor pages et des Razor vues. Au minimum, votre projet doit ajouter des références de package à :

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Le `Microsoft.AspNetCore.Razor.Design` package fournit les Razor tâches et les cibles de compilation pour le projet.

  Les packages précédents sont inclus dans `Microsoft.AspNetCore.Mvc`. Le balisage suivant montre un fichier projet qui utilise le Razor Kit de développement logiciel (SDK) pour générer Razor des fichiers pour une Razor application ASP.net Core pages :

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Les `Microsoft.AspNetCore.Razor.Design` `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages et sont inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app). Toutefois, la référence de package sans version `Microsoft.AspNetCore.App` fournit un package à l’application qui n’inclut pas la dernière version de `Microsoft.AspNetCore.Razor.Design` . Les projets doivent référencer une version cohérente de `Microsoft.AspNetCore.Razor.Design` (ou `Microsoft.AspNetCore.Mvc` ) afin que les correctifs les plus récents au moment de la génération pour Razor soient inclus. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Propriétés

Les propriétés suivantes contrôlent le Razor comportement du kit de développement logiciel (SDK) dans le cadre d’une génération de projet :

* `RazorCompileOnBuild`: Quand `true` , compile et émet l' Razor assembly dans le cadre de la génération du projet. La valeur par défaut est `true`.
* `RazorCompileOnPublish`: Quand `true` , compile et émet l' Razor assembly dans le cadre de la publication du projet. La valeur par défaut est `true`.

Les propriétés et les éléments du tableau suivant sont utilisés pour configurer les entrées et la sortie vers le Razor Kit de développement logiciel (SDK).

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> À compter de ASP.NET Core 3,0, les vues ou Razor les pages MVC ne sont pas servies par défaut si les `RazorCompileOnBuild` `RazorCompileOnPublish` Propriétés ou MSBuild dans le fichier projet sont désactivées. Les applications doivent ajouter une référence explicite à [Microsoft. AspNetCore. Mvc. Razor Package RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) si l’application s’appuie sur la compilation au moment de l’exécution pour traiter les fichiers *. cshtml* .

::: moniker-end

| Éléments | Description |
| ----- | ----------- |
| `RazorGenerate` | Éléments Item (fichiers *. cshtml* ) qui sont des entrées de la génération de code. |
| `RazorComponent` | Éléments Item (fichiers *. Razor* ) qui sont des entrées de la Razor génération de code du composant. |
| `RazorCompile` | Éléments Item (fichiers *. cs* ) qui sont des entrées dans les Razor cibles de compilation. Utilisez cette `ItemGroup` valeur pour spécifier des fichiers supplémentaires à compiler dans l' Razor assembly. |
| `RazorTargetAssemblyAttribute` | Éléments Item utilisés pour coder la génération d’attributs pour l' Razor assembly. Exemple :  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Éléments Item ajoutés en tant que ressources incorporées à l' Razor assembly généré. |

::: moniker range=">= aspnetcore-3.0"

| Propriété | Description |
| -------- | ----------- |
| `RazorTargetName` | Nom de fichier (sans extension) de l’assembly généré par Razor . |
| `RazorOutputPath` | RazorRépertoire de sortie. |
| `RazorCompileToolset` | Utilisé pour déterminer l’ensemble d’outils utilisé pour générer l' Razor assembly. Les valeurs correctes sont `Implicit`, `RazorSDK` et `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | La valeur par défaut est `true`. Lorsque `true` , comprend des fichiers *web.config* , *. JSON* et *. cshtml* en tant que contenu dans le projet. Lorsqu’ils sont référencés via `Microsoft.NET.Sdk.Web` , les fichiers situés sous les fichiers *wwwroot* et config sont également inclus. |
| `EnableDefaultRazorGenerateItems` | Si la valeur est `true`, inclut les fichiers *.cshtml* des éléments `Content` dans les éléments `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Lorsque `true` , génère un fichier *. cs* contenant les attributs spécifiés par `RazorAssemblyAttribute` et comprend le fichier dans la sortie de compilation. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Si la valeur est `true`, ajoute un ensemble par défaut d’attributs d’assembly à `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Lorsque la `true` valeur est, copie `RazorGenerate` les fichiers d’éléments ( *. cshtml* ) dans le répertoire de publication. En règle générale, Razor les fichiers ne sont pas requis pour une application publiée s’ils participent à la compilation au moment de la génération ou de la publication. La valeur par défaut est `false`. |
| `PreserveCompilationReferences` | Si la valeur est `true`, copie les éléments d’assembly de référence dans le répertoire de publication. En général, les assemblys de référence ne sont pas requis pour une application publiée si la Razor compilation se produit au moment de la génération ou de la publication. Affectez `true` la valeur si votre application publiée requiert la compilation du Runtime. Par exemple, définissez la valeur sur `true` si l’application modifie les fichiers *. cshtml* au moment de l’exécution ou utilise des vues incorporées. La valeur par défaut est `false`. |
| `IncludeRazorContentInPack` | Lorsque `true` , tous les Razor éléments de contenu (fichiers *. cshtml* ) sont marqués pour être inclus dans le package NuGet généré. La valeur par défaut est `false`. |
| `EmbedRazorGenerateSources` | Lorsque la `true` valeur est, ajoute Razor des éléments Generate ( *. cshtml* ) comme fichiers incorporés à l' Razor assembly généré. La valeur par défaut est `false`. |
| `UseRazorBuildServer` | Si la valeur est `true`, utilise un processus de serveur de build persistant pour décharger le travail de génération de code. Utilise par défaut la valeur de `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Lorsque `true` la valeur est, le kit de développement logiciel (SDK) génère des attributs supplémentaires utilisés par MVC au moment de l’exécution pour effectuer la découverte des parties |
| `DefaultWebContentItemExcludes` | Modèle globbing pour les éléments Item qui doivent être exclus du `Content` groupe d’éléments dans les projets ciblant le Web ou le Razor Kit de développement logiciel (SDK) |
| `ExcludeConfigFilesFromBuildOutput` | Lorsque `true` les fichiers *. config* et *. JSON* ne sont pas copiés dans le répertoire de sortie de la génération. |
| `AddRazorSupportForMvc` | Lorsque `true` la valeur est, configure le Razor Kit de développement logiciel (SDK) pour ajouter la prise en charge de la configuration MVC requise lors de la génération d’applications contenant des vues ou des Razor pages Mvc. Cette propriété est implicitement définie pour les projets .NET Core 3,0 ou ultérieur ciblant le kit de développement logiciel (SDK) Web |
| `RazorLangVersion` | Version du Razor langage à cibler. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Propriété | Description |
| -------- | ----------- |
| `RazorTargetName` | Nom de fichier (sans extension) de l’assembly généré par Razor . |
| `RazorOutputPath` | RazorRépertoire de sortie. |
| `RazorCompileToolset` | Utilisé pour déterminer l’ensemble d’outils utilisé pour générer l' Razor assembly. Les valeurs correctes sont `Implicit`, `RazorSDK` et `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | La valeur par défaut est `true`. Lorsque `true` , comprend des fichiers *web.config* , *. JSON* et *. cshtml* en tant que contenu dans le projet. Lorsqu’ils sont référencés via `Microsoft.NET.Sdk.Web` , les fichiers situés sous les fichiers *wwwroot* et config sont également inclus. |
| `EnableDefaultRazorGenerateItems` | Si la valeur est `true`, inclut les fichiers *.cshtml* des éléments `Content` dans les éléments `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Lorsque `true` , génère un fichier *. cs* contenant les attributs spécifiés par `RazorAssemblyAttribute` et comprend le fichier dans la sortie de compilation. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Si la valeur est `true`, ajoute un ensemble par défaut d’attributs d’assembly à `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Lorsque la `true` valeur est, copie `RazorGenerate` les fichiers d’éléments ( *. cshtml* ) dans le répertoire de publication. En règle générale, Razor les fichiers ne sont pas requis pour une application publiée s’ils participent à la compilation au moment de la génération ou de la publication. La valeur par défaut est `false`. |
| `CopyRefAssembliesToPublishDirectory` | Si la valeur est `true`, copie les éléments d’assembly de référence dans le répertoire de publication. En général, les assemblys de référence ne sont pas requis pour une application publiée si la Razor compilation se produit au moment de la génération ou de la publication. Affectez `true` la valeur si votre application publiée requiert la compilation du Runtime. Par exemple, définissez la valeur sur `true` si l’application modifie les fichiers *. cshtml* au moment de l’exécution ou utilise des vues incorporées. La valeur par défaut est `false`. |
| `IncludeRazorContentInPack` | Lorsque `true` , tous les Razor éléments de contenu (fichiers *. cshtml* ) sont marqués pour être inclus dans le package NuGet généré. La valeur par défaut est `false`. |
| `EmbedRazorGenerateSources` | Lorsque la `true` valeur est, ajoute Razor des éléments Generate ( *. cshtml* ) comme fichiers incorporés à l' Razor assembly généré. La valeur par défaut est `false`. |
| `UseRazorBuildServer` | Si la valeur est `true`, utilise un processus de serveur de build persistant pour décharger le travail de génération de code. Utilise par défaut la valeur de `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Lorsque `true` la valeur est, le kit de développement logiciel (SDK) génère des attributs supplémentaires utilisés par MVC au moment de l’exécution pour effectuer la découverte des parties |
| `DefaultWebContentItemExcludes` | Modèle globbing pour les éléments Item qui doivent être exclus du `Content` groupe d’éléments dans les projets ciblant le Web ou le Razor Kit de développement logiciel (SDK) |
| `ExcludeConfigFilesFromBuildOutput` | Lorsque `true` les fichiers *. config* et *. JSON* ne sont pas copiés dans le répertoire de sortie de la génération. |
| `AddRazorSupportForMvc` | Lorsque `true` la valeur est, configure le Razor Kit de développement logiciel (SDK) pour ajouter la prise en charge de la configuration MVC requise lors de la génération d’applications contenant des vues ou des Razor pages Mvc. Cette propriété est implicitement définie pour les projets .NET Core 3,0 ou ultérieur ciblant le kit de développement logiciel (SDK) Web |
| `RazorLangVersion` | Version du Razor langage à cibler. |

::: moniker-end

Pour plus d’informations sur les propriétés, consultez [propriétés MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Cibles

Le Razor Kit de développement logiciel (SDK) définit deux cibles principales :

* `RazorGenerate`: Le code génère des fichiers *. cs* à partir d' `RazorGenerate` éléments Item. Utilisez la `RazorGenerateDependsOn` propriété pour spécifier des cibles supplémentaires qui peuvent s’exécuter avant ou après cette cible.
* `RazorCompile`: Compile les fichiers *. cs* générés dans en un Razor assembly. Utilisez `RazorCompileDependsOn` pour spécifier des cibles supplémentaires qui peuvent s’exécuter avant ou après cette cible.
* `RazorComponentGenerate`: Le code génère des fichiers *. cs* pour les `RazorComponent` éléments Item. Utilisez la `RazorComponentGenerateDependsOn` propriété pour spécifier des cibles supplémentaires qui peuvent s’exécuter avant ou après cette cible.

### <a name="runtime-compilation-of-no-locrazor-views"></a>Compilation du runtime des Razor vues

* Par défaut, le Razor Kit de développement logiciel (SDK) ne publie pas les assemblys de référence requis pour effectuer la compilation du Runtime. Cela entraîne des échecs de compilation quand le modèle d’application s’appuie sur la compilation au moment du runtime&mdash;par exemple, l’application utilise des vues incorporées ou change les vues une fois l’application publiée. Définissez `CopyRefAssembliesToPublishDirectory` sur `true` pour continuer à publier les assemblys de référence.

* Pour une application Web, vérifiez que votre application cible le `Microsoft.NET.Sdk.Web` Kit de développement logiciel (SDK).

## <a name="no-locrazor-language-version"></a>Razor version du langage

Lorsque vous ciblez le `Microsoft.NET.Sdk.Web` Kit de développement logiciel (SDK), la Razor version du langage est déduite de la version du Framework cible de l’application. Pour les projets ciblant le `Microsoft.NET.Sdk.Razor` Kit de développement logiciel (SDK) ou dans le cas rare où l’application requiert une version de langue différente de Razor la valeur déduite, une version peut être configurée en définissant la `<RazorLangVersion>` propriété dans le fichier projet de l’application :

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razorla version de langage de est étroitement intégrée à la version du runtime pour laquelle elle a été générée. Le ciblage d’une version de langage qui n’est pas conçue pour le runtime n’est pas pris en charge et génère probablement des erreurs de Build.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Ajouts au format csproj pour .NET Core](/dotnet/core/tools/csproj)
* [Éléments communs des projets MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
