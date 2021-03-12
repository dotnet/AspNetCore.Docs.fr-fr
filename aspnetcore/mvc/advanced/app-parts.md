---
title: Partager des contrôleurs, des affichages, des Razor pages et d’autres éléments avec des composants d’application dans ASP.net Core
author: rick-anderson
description: Partager des contrôleurs, des affichages, des Razor pages et bien plus encore avec des composants d’application dans ASP.net Core
ms.author: riande
ms.date: 11/11/2019
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 23bc1db6a184e7babe87e2d311a8ac4a59e78dd0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588358"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a>Partager des contrôleurs, des affichages, des Razor pages et d’autres éléments avec des composants d’application

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Une *partie d’application* est une abstraction sur les ressources d’une application. Les composants de l’application permettent à ASP.NET Core de découvrir les contrôleurs, les composants de vue, les tag helpers, Razor les pages, les sources de compilation Razor et bien plus encore. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> est une partie de l’application. `AssemblyPart` encapsule une référence d’assembly et expose des types et des références de compilation.

Les [fournisseurs de fonctionnalités](#fp) utilisent des composants d’application pour remplir les fonctionnalités d’une application ASP.net core. Le principal cas d’utilisation des composants d’application consiste à configurer une application pour découvrir (ou éviter le chargement) ASP.NET Core fonctionnalités d’un assembly. Par exemple, vous pouvez souhaiter partager des fonctionnalités communes entre plusieurs applications. À l’aide de composants d’application, vous pouvez partager un assembly (DLL) contenant des contrôleurs, des vues, des Razor pages, des sources de compilation Razor, des balises d’aide et bien plus encore avec plusieurs applications. Le partage d’un assembly est préférable à la duplication du code dans plusieurs projets.

Les applications ASP.NET Core chargent les fonctionnalités à partir de <xref:System.Web.WebPages.ApplicationPart> . La <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classe représente une partie d’application qui est sauvegardée par un assembly.

## <a name="load-aspnet-core-features"></a>Charger les fonctionnalités de ASP.NET Core

Utilisez les <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes et pour découvrir et charger des fonctionnalités de ASP.net Core (contrôleurs, composants de vue, etc.). Le <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> effectue le suivi des composants d’application et des fournisseurs de fonctionnalités disponibles. `ApplicationPartManager` est configuré dans `Startup.ConfigureServices` :

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

Le code suivant présente une approche alternative à la configuration de `ApplicationPartManager` à l’aide de `AssemblyPart` :

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

Les deux exemples de code précédents chargent `SharedController` à partir d’un assembly. Le `SharedController` n’est pas dans le projet de l’application. Consultez le téléchargement de l’exemple de [solution WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) .

### <a name="include-views"></a>Inclure les vues

Utilisez une [ Razor bibliothèque de classes](xref:razor-pages/ui-class) pour inclure des vues dans l’assembly.

### <a name="prevent-loading-resources"></a>Empêcher le chargement des ressources

Les composants d’application peuvent être utilisés pour *éviter* de charger des ressources dans un assembly ou un emplacement particulier. Ajoutez ou supprimez des membres du  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> regroupement pour masquer ou rendre disponibles les ressources. L’ordre des entrées dans la collection `ApplicationParts` n’est pas important. Configurez l `ApplicationPartManager` 'avant de l’utiliser pour configurer les services dans le conteneur. Par exemple, configurez le `ApplicationPartManager` avant d’appeler `AddControllersAsServices` . Appelez `Remove` sur la `ApplicationParts` collection pour supprimer une ressource.

Le `ApplicationPartManager` comprend des parties pour :

* Assembly de l’application et assemblys dépendants.
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

<a name="fp"></a>

## <a name="feature-providers"></a>Fournisseurs de fonctionnalités

Les fournisseurs de fonctionnalités d’application examinent les parties de l’application et fournissent des fonctionnalités pour ces parties. Il existe des fournisseurs de fonctionnalités intégrés pour les fonctionnalités de ASP.NET Core suivantes :

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* `internal class`[ Razor CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)

Les fournisseurs de fonctionnalités héritent de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, où `T` correspond au type de la fonctionnalité. Les fournisseurs de fonctionnalités peuvent être implémentés pour l’un des types de fonctionnalités précédemment listés. L’ordre des fournisseurs de fonctionnalités dans le peut avoir un `ApplicationPartManager.FeatureProviders` impact sur le comportement de l’exécution. Les fournisseurs ajoutés ultérieurement peuvent réagir aux actions effectuées par les fournisseurs précédemment ajoutés.

### <a name="display-available-features"></a>Afficher les fonctionnalités disponibles

Les fonctionnalités disponibles pour une application peuvent être énumérées en demandant un `ApplicationPartManager` par [injection de dépendances](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

L' [exemple Download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2) utilise le code précédent pour afficher les fonctionnalités de l’application :

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Détection dans les composants de l’application

Les erreurs HTTP 404 ne sont pas rares lors du développement avec des composants d’application. Ces erreurs sont généralement dues à l’absence d’une exigence essentielle pour la façon dont les composants des applications sont détectés. Si votre application renvoie une erreur HTTP 404, vérifiez que les conditions suivantes sont remplies :

* Le `applicationName` paramètre doit être défini sur l’assembly racine utilisé pour la découverte. L’assembly racine utilisé pour la découverte est normalement l’assembly de point d’entrée.
* L’assembly racine doit avoir une référence aux parties utilisées pour la découverte. La référence peut être directe ou transitive.
* L’assembly racine doit référencer le kit de développement logiciel (SDK) Web. L’infrastructure a une logique qui marque les attributs dans l’assembly racine utilisé pour la découverte.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Une *partie d’application* est une abstraction sur les ressources d’une application. Les composants de l’application permettent à ASP.NET Core de découvrir les contrôleurs, les composants de vue, les tag helpers, Razor les pages, les sources de compilation Razor et bien plus encore. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) est une partie de l’application. `AssemblyPart` encapsule une référence d’assembly et expose des types et des références de compilation.

Les *fournisseurs de fonctionnalités* utilisent des composants d’application pour remplir les fonctionnalités d’une application ASP.net core. Le principal cas d’utilisation des composants d’application consiste à configurer une application pour découvrir (ou éviter le chargement) ASP.NET Core fonctionnalités d’un assembly. Par exemple, vous pouvez souhaiter partager des fonctionnalités communes entre plusieurs applications. À l’aide de composants d’application, vous pouvez partager un assembly (DLL) contenant des contrôleurs, des vues, des Razor pages, des sources de compilation Razor, des balises d’aide et bien plus encore avec plusieurs applications. Le partage d’un assembly est préférable à la duplication du code dans plusieurs projets.

Les applications ASP.NET Core chargent les fonctionnalités à partir de <xref:System.Web.WebPages.ApplicationPart> . La <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classe représente une partie d’application qui est sauvegardée par un assembly.

## <a name="load-aspnet-core-features"></a>Charger les fonctionnalités de ASP.NET Core

Utilisez les `ApplicationPart` `AssemblyPart` classes et pour découvrir et charger des fonctionnalités de ASP.net Core (contrôleurs, composants de vue, etc.). Le <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> effectue le suivi des composants d’application et des fournisseurs de fonctionnalités disponibles. `ApplicationPartManager` est configuré dans `Startup.ConfigureServices` :

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

Le code suivant présente une approche alternative à la configuration de `ApplicationPartManager` à l’aide de `AssemblyPart` :

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

Les deux exemples de code précédents chargent `SharedController` à partir d’un assembly. Le `SharedController` n’est pas dans le projet de l’application. Consultez le téléchargement de l’exemple de [solution WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .

### <a name="include-views"></a>Inclure les vues

Utilisez une [ Razor bibliothèque de classes](xref:razor-pages/ui-class) pour inclure des vues dans l’assembly.

### <a name="prevent-loading-resources"></a>Empêcher le chargement des ressources

Les composants d’application peuvent être utilisés pour *éviter* de charger des ressources dans un assembly ou un emplacement particulier. Ajoutez ou supprimez des membres du  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> regroupement pour masquer ou rendre disponibles les ressources. L’ordre des entrées dans la collection `ApplicationParts` n’est pas important. Configurez l `ApplicationPartManager` 'avant de l’utiliser pour configurer les services dans le conteneur. Par exemple, configurez le `ApplicationPartManager` avant d’appeler `AddControllersAsServices` . Appelez `Remove` sur la `ApplicationParts` collection pour supprimer une ressource.

Le code suivant utilise <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> pour supprimer `MyDependentLibrary` de l’application : [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

Le `ApplicationPartManager` comprend des parties pour :

* Assembly de l’application et assemblys dépendants.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Fournisseurs de fonctionnalités d’application

Les fournisseurs de fonctionnalités d’application examinent les parties de l’application et fournissent des fonctionnalités pour ces parties. Il existe des fournisseurs de fonctionnalités intégrés pour les fonctionnalités de ASP.NET Core suivantes :

* [Controllers](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Tag Helpers](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Afficher les composants](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Les fournisseurs de fonctionnalités héritent de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, où `T` correspond au type de la fonctionnalité. Les fournisseurs de fonctionnalités peuvent être implémentés pour l’un des types de fonctionnalités précédemment listés. L’ordre des fournisseurs de fonctionnalités dans le peut avoir un `ApplicationPartManager.FeatureProviders` impact sur le comportement de l’exécution. Les fournisseurs ajoutés ultérieurement peuvent réagir aux actions effectuées par les fournisseurs précédemment ajoutés.

### <a name="display-available-features"></a>Afficher les fonctionnalités disponibles

Les fonctionnalités disponibles pour une application peuvent être énumérées en demandant un `ApplicationPartManager` par [injection de dépendances](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

L' [exemple Download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2) utilise le code précédent pour afficher les fonctionnalités de l’application :

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Détection dans les composants de l’application

Les erreurs HTTP 404 ne sont pas rares lors du développement avec des composants d’application. Ces erreurs sont généralement dues à l’absence d’une exigence essentielle pour la façon dont les composants des applications sont détectés. Si votre application renvoie une erreur HTTP 404, vérifiez que les conditions suivantes sont remplies :

* Le `applicationName` paramètre doit être défini sur l’assembly racine utilisé pour la découverte. L’assembly racine utilisé pour la découverte est normalement l’assembly de point d’entrée.
* L’assembly racine doit avoir une référence aux parties utilisées pour la découverte. La référence peut être directe ou transitive.
* L’assembly racine doit référencer le kit de développement logiciel (SDK) Web.
  * L’infrastructure de ASP.NET Core a une logique de génération personnalisée qui marque les attributs dans l’assembly racine utilisé pour la découverte.

::: moniker-end
