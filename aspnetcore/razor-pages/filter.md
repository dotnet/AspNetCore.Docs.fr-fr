---
title: 'Méthodes de filtre pour les Razor pages dans ASP.net Core'
author: Rick-Anderson
description: 'Découvrez comment créer des méthodes de filtre pour les Razor pages dans ASP.net core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
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
uid: razor-pages/filter
ms.openlocfilehash: a6d25c1b88e09560c1aad9aefd9148f7fe293909
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056827"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="ba85c-103">Méthodes de filtre pour les Razor pages dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ba85c-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba85c-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ba85c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ba85c-105">Razor Filtres de page [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) et [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permettent Razor aux pages d’exécuter du code avant et après l’exécution d’un Razor Gestionnaire de page.</span><span class="sxs-lookup"><span data-stu-id="ba85c-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="ba85c-106">Razor Les filtres de page sont similaires aux [filtres d’action MVC ASP.net Core](xref:mvc/controllers/filters#action-filters), sauf qu’ils ne peuvent pas être appliqués aux méthodes de gestionnaire de page individuelles.</span><span class="sxs-lookup"><span data-stu-id="ba85c-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="ba85c-107">Razor Filtres de page :</span><span class="sxs-lookup"><span data-stu-id="ba85c-107">Razor Page filters:</span></span>

* <span data-ttu-id="ba85c-108">Exécutent le code après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="ba85c-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="ba85c-109">Exécutent le code avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.</span><span class="sxs-lookup"><span data-stu-id="ba85c-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="ba85c-110">Exécutent le code après l’exécution de la méthode de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="ba85c-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="ba85c-111">Peuvent être implémentés dans une page ou globalement.</span><span class="sxs-lookup"><span data-stu-id="ba85c-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="ba85c-112">Ne peuvent pas être appliqués à des méthodes de gestionnaire de page spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ba85c-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="ba85c-113">Peut avoir des dépendances de constructeur remplies par [injection de dépendance](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="ba85c-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="ba85c-114">Pour plus d’informations, consultez [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) et [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="ba85c-114">For more information, see [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) and [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span></span>

<span data-ttu-id="ba85c-115">Alors que les constructeurs de page et l’intergiciel (middleware) activent l’exécution de code personnalisé avant l’exécution d’une méthode de gestionnaire, seuls Razor les filtres de page permettent l’accès à <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> et à la page.</span><span class="sxs-lookup"><span data-stu-id="ba85c-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="ba85c-116">L’intergiciel a accès à `HttpContext` , mais pas au « contexte de page ».</span><span class="sxs-lookup"><span data-stu-id="ba85c-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="ba85c-117">Les filtres ont un <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> paramètre dérivé, qui fournit l’accès à `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="ba85c-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="ba85c-118">Voici un exemple de filtre de page : [implémentez un attribut de filtre](#ifa) qui ajoute un en-tête à la réponse, ce qui ne peut pas être fait avec des constructeurs ou des intergiciels (middleware).</span><span class="sxs-lookup"><span data-stu-id="ba85c-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="ba85c-119">L’accès au contexte de page, qui comprend l’accès aux instances de la page et de son modèle, sont disponibles uniquement lors de l’exécution de filtres, de gestionnaires ou du corps d’une Razor page.</span><span class="sxs-lookup"><span data-stu-id="ba85c-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a Razor Page.</span></span>

<span data-ttu-id="ba85c-120">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ba85c-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ba85c-121">Razor Les filtres de page fournissent les méthodes suivantes, qui peuvent être appliquées globalement ou au niveau de la page :</span><span class="sxs-lookup"><span data-stu-id="ba85c-121">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="ba85c-122">Méthodes synchrones :</span><span class="sxs-lookup"><span data-stu-id="ba85c-122">Synchronous methods:</span></span>

  * <span data-ttu-id="ba85c-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : appelée après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="ba85c-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="ba85c-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.</span><span class="sxs-lookup"><span data-stu-id="ba85c-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="ba85c-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, avant le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="ba85c-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="ba85c-126">Méthodes asynchrones :</span><span class="sxs-lookup"><span data-stu-id="ba85c-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="ba85c-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : appelée de manière asynchrone après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="ba85c-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="ba85c-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : appelée de manière asynchrone avant l’appel de la méthode de gestionnaire, une fois la liaison de modèle terminée.</span><span class="sxs-lookup"><span data-stu-id="ba85c-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="ba85c-129">Implémentez la version synchrone ou asynchrone d’une interface de filtre, mais **pas** **les deux** .</span><span class="sxs-lookup"><span data-stu-id="ba85c-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="ba85c-130">Le framework vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="ba85c-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="ba85c-131">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="ba85c-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="ba85c-132">Si les deux interfaces sont implémentées, seules les méthodes Async sont appelées.</span><span class="sxs-lookup"><span data-stu-id="ba85c-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="ba85c-133">La même règle s’applique aux substitutions dans les pages : implémentez la version synchrone ou asynchrone de la substitution, mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="ba85c-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="ba85c-134">Implémenter des Razor filtres de page globalement</span><span class="sxs-lookup"><span data-stu-id="ba85c-134">Implement Razor Page filters globally</span></span>

<span data-ttu-id="ba85c-135">Le code suivant implémente `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="ba85c-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="ba85c-136">Dans le code précédent, `ProcessUserAgent.Write` est le code fourni par l’utilisateur qui fonctionne avec la chaîne de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ba85c-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="ba85c-137">Le code suivant active le filtre `SampleAsyncPageFilter` dans la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="ba85c-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="ba85c-138">Le code suivant appelle <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pour appliquer `SampleAsyncPageFilter` à uniquement les pages dans */movies* :</span><span class="sxs-lookup"><span data-stu-id="ba85c-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies* :</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="ba85c-139">Le code suivant implémente le filtre `IPageFilter` synchrone :</span><span class="sxs-lookup"><span data-stu-id="ba85c-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="ba85c-140">Le code suivant active le filtre `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="ba85c-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="ba85c-141">Implémenter Razor des filtres de page en substituant des méthodes de filtre</span><span class="sxs-lookup"><span data-stu-id="ba85c-141">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="ba85c-142">Le code suivant remplace les filtres de page asynchrones Razor :</span><span class="sxs-lookup"><span data-stu-id="ba85c-142">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="ba85c-143">Implémenter un attribut de filtre</span><span class="sxs-lookup"><span data-stu-id="ba85c-143">Implement a filter attribute</span></span>

<span data-ttu-id="ba85c-144">Le filtre de filtre basé sur les attributs intégré <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> peut être sous-classé.</span><span class="sxs-lookup"><span data-stu-id="ba85c-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="ba85c-145">Le filtre suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="ba85c-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="ba85c-146">Le code suivant applique l’attribut `AddHeader` :</span><span class="sxs-lookup"><span data-stu-id="ba85c-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="ba85c-147">Utilisez un outil tel que les outils de développement du navigateur pour examiner les en-têtes.</span><span class="sxs-lookup"><span data-stu-id="ba85c-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="ba85c-148">Sous **en-têtes de réponse** , `author: Rick` est affiché.</span><span class="sxs-lookup"><span data-stu-id="ba85c-148">Under **Response Headers** , `author: Rick` is displayed.</span></span>

<span data-ttu-id="ba85c-149">Pour obtenir des instructions sur le remplacement de l’ordre, consultez [Remplacement de l’ordre par défaut](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="ba85c-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="ba85c-150">Pour obtenir des instructions sur le court-circuitage du pipeline de filtres à partir d’un filtre, consultez [Annulation et court-circuit](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="ba85c-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="ba85c-151">Attribut de filtre Authorize</span><span class="sxs-lookup"><span data-stu-id="ba85c-151">Authorize filter attribute</span></span>

<span data-ttu-id="ba85c-152">L’attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) peut être appliqué à `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="ba85c-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ba85c-153">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ba85c-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ba85c-154">Razor Filtres de page [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) et [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permettent Razor aux pages d’exécuter du code avant et après l’exécution d’un Razor Gestionnaire de page.</span><span class="sxs-lookup"><span data-stu-id="ba85c-154">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="ba85c-155">Razor Les filtres de page sont similaires aux [filtres d’action MVC ASP.net Core](xref:mvc/controllers/filters#action-filters), sauf qu’ils ne peuvent pas être appliqués aux méthodes de gestionnaire de page individuelles.</span><span class="sxs-lookup"><span data-stu-id="ba85c-155">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="ba85c-156">Razor Filtres de page :</span><span class="sxs-lookup"><span data-stu-id="ba85c-156">Razor Page filters:</span></span>

* <span data-ttu-id="ba85c-157">Exécutent le code après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="ba85c-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="ba85c-158">Exécutent le code avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.</span><span class="sxs-lookup"><span data-stu-id="ba85c-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="ba85c-159">Exécutent le code après l’exécution de la méthode de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="ba85c-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="ba85c-160">Peuvent être implémentés dans une page ou globalement.</span><span class="sxs-lookup"><span data-stu-id="ba85c-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="ba85c-161">Ne peuvent pas être appliqués à des méthodes de gestionnaire de page spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ba85c-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="ba85c-162">Le code peut être exécuté avant l’exécution d’une méthode de gestionnaire à l’aide du constructeur de page ou de l’intergiciel (middleware), mais seuls les Razor filtres de page ont accès à [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="ba85c-162">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="ba85c-163">Les filtres ont un paramètre dérivé de [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) qui fournit un accès à `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="ba85c-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="ba85c-164">Par exemple, l’exemple [Implémenter un attribut de filtre](#ifa) ajoute un en-tête à la réponse, ce qu’il est impossible de faire avec des constructeurs ou des middlewares.</span><span class="sxs-lookup"><span data-stu-id="ba85c-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="ba85c-165">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ba85c-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ba85c-166">Razor Les filtres de page fournissent les méthodes suivantes, qui peuvent être appliquées globalement ou au niveau de la page :</span><span class="sxs-lookup"><span data-stu-id="ba85c-166">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="ba85c-167">Méthodes synchrones :</span><span class="sxs-lookup"><span data-stu-id="ba85c-167">Synchronous methods:</span></span>

  * <span data-ttu-id="ba85c-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : appelée après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="ba85c-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="ba85c-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.</span><span class="sxs-lookup"><span data-stu-id="ba85c-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="ba85c-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, avant le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="ba85c-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="ba85c-171">Méthodes asynchrones :</span><span class="sxs-lookup"><span data-stu-id="ba85c-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="ba85c-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : appelée de manière asynchrone après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="ba85c-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="ba85c-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : appelée de manière asynchrone avant l’appel de la méthode de gestionnaire, une fois la liaison de modèle terminée.</span><span class="sxs-lookup"><span data-stu-id="ba85c-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="ba85c-174">Implémentez la version synchrone **ou bien** la version asynchrone d’une interface de filtre, mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="ba85c-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="ba85c-175">Le framework vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="ba85c-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="ba85c-176">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="ba85c-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="ba85c-177">Si les deux interfaces sont implémentées, seules les méthodes Async sont appelées.</span><span class="sxs-lookup"><span data-stu-id="ba85c-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="ba85c-178">La même règle s’applique aux substitutions dans les pages : implémentez la version synchrone ou asynchrone de la substitution, mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="ba85c-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="ba85c-179">Implémenter des Razor filtres de page globalement</span><span class="sxs-lookup"><span data-stu-id="ba85c-179">Implement Razor Page filters globally</span></span>

<span data-ttu-id="ba85c-180">Le code suivant implémente `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="ba85c-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="ba85c-181">Dans le code précédent, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="ba85c-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="ba85c-182">Il est utilisé dans l’exemple pour fournir des informations de trace pour l’application.</span><span class="sxs-lookup"><span data-stu-id="ba85c-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="ba85c-183">Le code suivant active le filtre `SampleAsyncPageFilter` dans la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="ba85c-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="ba85c-184">Le code suivant montre l’intégralité de la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="ba85c-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="ba85c-185">Le code suivant appelle `AddFolderApplicationModelConvention` pour appliquer le filtre `SampleAsyncPageFilter` uniquement aux pages dans */subFolder* :</span><span class="sxs-lookup"><span data-stu-id="ba85c-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder* :</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="ba85c-186">Le code suivant implémente le filtre `IPageFilter` synchrone :</span><span class="sxs-lookup"><span data-stu-id="ba85c-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="ba85c-187">Le code suivant active le filtre `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="ba85c-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="ba85c-188">Implémenter Razor des filtres de page en substituant des méthodes de filtre</span><span class="sxs-lookup"><span data-stu-id="ba85c-188">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="ba85c-189">Le code suivant remplace les filtres de page synchrones Razor :</span><span class="sxs-lookup"><span data-stu-id="ba85c-189">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="ba85c-190">Implémenter un attribut de filtre</span><span class="sxs-lookup"><span data-stu-id="ba85c-190">Implement a filter attribute</span></span>

<span data-ttu-id="ba85c-191">Le filtre intégré [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) basé sur un attribut peut être sous-classé.</span><span class="sxs-lookup"><span data-stu-id="ba85c-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="ba85c-192">Le filtre suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="ba85c-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="ba85c-193">Le code suivant applique l’attribut `AddHeader` :</span><span class="sxs-lookup"><span data-stu-id="ba85c-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="ba85c-194">Pour obtenir des instructions sur le remplacement de l’ordre, consultez [Remplacement de l’ordre par défaut](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="ba85c-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="ba85c-195">Pour obtenir des instructions sur le court-circuitage du pipeline de filtres à partir d’un filtre, consultez [Annulation et court-circuit](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="ba85c-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="ba85c-196">Attribut de filtre Authorize</span><span class="sxs-lookup"><span data-stu-id="ba85c-196">Authorize filter attribute</span></span>

<span data-ttu-id="ba85c-197">L’attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) peut être appliqué à `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="ba85c-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end