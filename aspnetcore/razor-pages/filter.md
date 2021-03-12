---
title: Méthodes de filtre pour les Razor pages dans ASP.net Core
author: Rick-Anderson
description: Découvrez comment créer des méthodes de filtre pour les Razor pages dans ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
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
uid: razor-pages/filter
ms.openlocfilehash: 178e6348d2d50dae34feea6a0ed261de01037136
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586135"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a>Méthodes de filtre pour les Razor pages dans ASP.net Core

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor Filtres de page [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) et [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permettent Razor aux pages d’exécuter du code avant et après l’exécution d’un Razor Gestionnaire de page. Razor Les filtres de page sont similaires aux [filtres d’action MVC ASP.net Core](xref:mvc/controllers/filters#action-filters), sauf qu’ils ne peuvent pas être appliqués aux méthodes de gestionnaire de page individuelles.

Razor Filtres de page :

* Exécutent le code après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.
* Exécutent le code avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.
* Exécutent le code après l’exécution de la méthode de gestionnaire.
* Peuvent être implémentés dans une page ou globalement.
* Ne peuvent pas être appliqués à des méthodes de gestionnaire de page spécifiques.
* Peut avoir des dépendances de constructeur remplies par [injection de dépendance](xref:fundamentals/dependency-injection) (di). Pour plus d’informations, consultez [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) et [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).

Alors que les constructeurs de page et l’intergiciel (middleware) activent l’exécution de code personnalisé avant l’exécution d’une méthode de gestionnaire, seuls Razor les filtres de page permettent l’accès à <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> et à la page. L’intergiciel a accès à `HttpContext` , mais pas au « contexte de page ». Les filtres ont un <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> paramètre dérivé, qui fournit l’accès à `HttpContext` . Voici un exemple de filtre de page : [implémentez un attribut de filtre](#ifa) qui ajoute un en-tête à la réponse, ce qui ne peut pas être fait avec des constructeurs ou des intergiciels (middleware). L’accès au contexte de page, qui comprend l’accès aux instances de la page et de son modèle, sont disponibles uniquement lors de l’exécution de filtres, de gestionnaires ou du corps d’une Razor page.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/3.1sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Razor Les filtres de page fournissent les méthodes suivantes, qui peuvent être appliquées globalement ou au niveau de la page :

* Méthodes synchrones :

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : appelée après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, avant le résultat de l’action.

* Méthodes asynchrones :

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : appelée de manière asynchrone après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : appelée de manière asynchrone avant l’appel de la méthode de gestionnaire, une fois la liaison de modèle terminée.

Implémentez la version synchrone ou asynchrone d’une interface de filtre, mais **pas** **les deux** . Le framework vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface. Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone. Si les deux interfaces sont implémentées, seules les méthodes Async sont appelées. La même règle s’applique aux substitutions dans les pages : implémentez la version synchrone ou asynchrone de la substitution, mais pas les deux.

## <a name="implement-razor-page-filters-globally"></a>Implémenter des Razor filtres de page globalement

Le code suivant implémente `IAsyncPageFilter` :

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

Dans le code précédent, `ProcessUserAgent.Write` est le code fourni par l’utilisateur qui fonctionne avec la chaîne de l’agent utilisateur.

Le code suivant active le filtre `SampleAsyncPageFilter` dans la classe `Startup` :

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

Le code suivant appelle <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pour appliquer `SampleAsyncPageFilter` à uniquement les pages dans */movies*:

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

Le code suivant implémente le filtre `IPageFilter` synchrone :

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Le code suivant active le filtre `SamplePageFilter` :

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>Implémenter Razor des filtres de page en substituant des méthodes de filtre

Le code suivant remplace les filtres de page asynchrones Razor :

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implémenter un attribut de filtre

Le filtre de filtre basé sur les attributs intégré <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> peut être sous-classé. Le filtre suivant ajoute un en-tête à la réponse :

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Le code suivant applique l’attribut `AddHeader` :

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

Utilisez un outil tel que les outils de développement du navigateur pour examiner les en-têtes. Sous **en-têtes de réponse**, `author: Rick` est affiché.

Pour obtenir des instructions sur le remplacement de l’ordre, consultez [Remplacement de l’ordre par défaut](xref:mvc/controllers/filters#overriding-the-default-order).

Pour obtenir des instructions sur le court-circuitage du pipeline de filtres à partir d’un filtre, consultez [Annulation et court-circuit](xref:mvc/controllers/filters#cancellation-and-short-circuiting).

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Attribut de filtre Authorize

L’attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) peut être appliqué à `PageModel` :

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor Filtres de page [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) et [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permettent Razor aux pages d’exécuter du code avant et après l’exécution d’un Razor Gestionnaire de page. Razor Les filtres de page sont similaires aux [filtres d’action MVC ASP.net Core](xref:mvc/controllers/filters#action-filters), sauf qu’ils ne peuvent pas être appliqués aux méthodes de gestionnaire de page individuelles.

Razor Filtres de page :

* Exécutent le code après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.
* Exécutent le code avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.
* Exécutent le code après l’exécution de la méthode de gestionnaire.
* Peuvent être implémentés dans une page ou globalement.
* Ne peuvent pas être appliqués à des méthodes de gestionnaire de page spécifiques.

Le code peut être exécuté avant l’exécution d’une méthode de gestionnaire à l’aide du constructeur de page ou de l’intergiciel (middleware), mais seuls les Razor filtres de page ont accès à [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext). Les filtres ont un paramètre dérivé de [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) qui fournit un accès à `HttpContext`. Par exemple, l’exemple [Implémenter un attribut de filtre](#ifa) ajoute un en-tête à la réponse, ce qu’il est impossible de faire avec des constructeurs ou des middlewares.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/sample/PageFilter) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Razor Les filtres de page fournissent les méthodes suivantes, qui peuvent être appliquées globalement ou au niveau de la page :

* Méthodes synchrones :

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : appelée après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, avant le résultat de l’action.

* Méthodes asynchrones :

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : appelée de manière asynchrone après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : appelée de manière asynchrone avant l’appel de la méthode de gestionnaire, une fois la liaison de modèle terminée.

> [!NOTE]
> Implémentez la version synchrone **ou bien** la version asynchrone d’une interface de filtre, mais pas les deux. Le framework vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface. Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone. Si les deux interfaces sont implémentées, seules les méthodes Async sont appelées. La même règle s’applique aux substitutions dans les pages : implémentez la version synchrone ou asynchrone de la substitution, mais pas les deux.

## <a name="implement-razor-page-filters-globally"></a>Implémenter des Razor filtres de page globalement

Le code suivant implémente `IAsyncPageFilter` :

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

Dans le code précédent, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) n’est pas obligatoire. Il est utilisé dans l’exemple pour fournir des informations de trace pour l’application.

Le code suivant active le filtre `SampleAsyncPageFilter` dans la classe `Startup` :

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

Le code suivant montre l’intégralité de la classe `Startup` :

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

Le code suivant appelle `AddFolderApplicationModelConvention` pour appliquer le filtre `SampleAsyncPageFilter` uniquement aux pages dans */subFolder* :

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

Le code suivant implémente le filtre `IPageFilter` synchrone :

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Le code suivant active le filtre `SamplePageFilter` :

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>Implémenter Razor des filtres de page en substituant des méthodes de filtre

Le code suivant remplace les filtres de page synchrones Razor :

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implémenter un attribut de filtre

Le filtre intégré [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) basé sur un attribut peut être sous-classé. Le filtre suivant ajoute un en-tête à la réponse :

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Le code suivant applique l’attribut `AddHeader` :

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

Pour obtenir des instructions sur le remplacement de l’ordre, consultez [Remplacement de l’ordre par défaut](xref:mvc/controllers/filters#overriding-the-default-order).

Pour obtenir des instructions sur le court-circuitage du pipeline de filtres à partir d’un filtre, consultez [Annulation et court-circuit](xref:mvc/controllers/filters#cancellation-and-short-circuiting). 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Attribut de filtre Authorize

L’attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) peut être appliqué à `PageModel` :

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end