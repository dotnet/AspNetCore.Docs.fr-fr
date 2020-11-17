---
title: Filtres dans ASP.NET Core
author: Rick-Anderson
description: Découvrez comment les filtres fonctionnent et comment les utiliser dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: d075faa951a34fb3856b54eb9e21593b6616b4f1
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673963"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="4ee3b-103">Filtres dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ee3b-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4ee3b-104">Par [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4ee3b-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4ee3b-105">Les *filtres* dans ASP.NET Core permettent d’exécuter du code avant ou après des étapes spécifiques dans le pipeline de traitement des requêtes.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="4ee3b-106">Les filtres intégrés gèrent notamment les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="4ee3b-107">Autorisation (empêcher un utilisateur non autorisé d’accéder à des ressources).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="4ee3b-108">Mise en cache des réponses (court-circuitage du pipeline de requêtes pour retourner une réponse mise en cache).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="4ee3b-109">Il est possible de créer des filtres personnalisés pour gérer les problèmes transversaux.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="4ee3b-110">Les exemples de problèmes transversaux incluent la gestion des erreurs, la mise en cache, la configuration, l’autorisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="4ee3b-111">Les filtres évitent la duplication de code.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="4ee3b-112">Par exemple, un filtre d’exceptions de gestion des erreurs peut servir à consolider la gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="4ee3b-113">Ce document s’applique aux Razor pages, aux contrôleurs d’API et aux contrôleurs avec des vues.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="4ee3b-114">Les filtres ne fonctionnent pas directement avec les [ Razor composants](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="4ee3b-115">Un filtre peut uniquement affecter indirectement un composant lorsque :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="4ee3b-116">Le composant est incorporé dans une page ou une vue.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="4ee3b-117">La page ou le contrôleur/la vue utilise le filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="4ee3b-118">[Afficher ou télécharger l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([comment télécharger](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="4ee3b-119">Fonctionnement des filtres</span><span class="sxs-lookup"><span data-stu-id="4ee3b-119">How filters work</span></span>

<span data-ttu-id="4ee3b-120">Les filtres s’exécutent dans le *pipeline des appels d’action ASP.NET Core*, parfois appelé *pipeline de filtres*.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="4ee3b-121">Le pipeline de filtres s’exécute après la sélection par ASP.NET Core de l’action à exécuter.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La demande est traitée par un autre intergiciel, un intergiciel (middleware) de routage, une sélection d’action et le pipeline d’appel d’action.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="4ee3b-124">Types de filtres</span><span class="sxs-lookup"><span data-stu-id="4ee3b-124">Filter types</span></span>

<span data-ttu-id="4ee3b-125">Chaque type de filtre est exécuté dans une étape différente du pipeline de filtres :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="4ee3b-126">Les [filtres d’autorisations](#authorization-filters) s’exécutent en premier et sont utilisés pour déterminer si l’utilisateur est autorisé pour la requête.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="4ee3b-127">Filtres d’autorisation court-circuiter le pipeline si la demande n’est pas autorisée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="4ee3b-128">[Filtres de ressources](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="4ee3b-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="4ee3b-129">Exécuter après l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-129">Run after authorization.</span></span>  
  * <span data-ttu-id="4ee3b-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> exécute du code avant le reste du pipeline de filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="4ee3b-131">Par exemple, `OnResourceExecuting` exécute le code avant la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="4ee3b-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> exécute du code une fois que le reste du pipeline est terminé.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="4ee3b-133">[Filtres d’action](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="4ee3b-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="4ee3b-134">Exécutez le code juste avant et après l’appel d’une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="4ee3b-135">Peut modifier les arguments passés dans une action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="4ee3b-136">Peut modifier le résultat retourné par l’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="4ee3b-137">Ne sont **pas** pris en charge dans les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="4ee3b-138">Les [filtres d’exception](#exception-filters) appliquent des stratégies globales aux exceptions non gérées qui se produisent avant l’écriture du corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="4ee3b-139">Les [filtres de résultats](#result-filters) exécutent le code immédiatement avant et après l’exécution des résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="4ee3b-140">Ils s’exécutent uniquement au terme de l’exécution de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="4ee3b-141">Ils sont utiles pour la logique qui doit entourer l’exécution de la vue ou du formateur.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="4ee3b-142">Le diagramme suivant montre comment ces types de filtres interagissent dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La requête est traitée à travers les filtres d’autorisations, les filtres de ressources, la liaison de modèle, les filtres d’actions, l’exécution d’actions et la conversion des résultats d’actions, les filtres d’exceptions, les filtres de résultats et l’exécution de résultats.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="4ee3b-145">Implémentation</span><span class="sxs-lookup"><span data-stu-id="4ee3b-145">Implementation</span></span>

<span data-ttu-id="4ee3b-146">Les filtres prennent en charge les implémentations synchrones et asynchrones via différentes définitions d’interface.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="4ee3b-147">Les filtres synchrones exécutent le code avant et après leur étape de pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="4ee3b-148">Par exemple, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> est appelé avant l’appel de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="4ee3b-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> est appelé après le retour de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="4ee3b-150">Dans le code précédent, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) est une fonction utilitaire dans l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="4ee3b-151">Les filtres asynchrones définissent une `On-Stage-ExecutionAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="4ee3b-152">Par exemple, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="4ee3b-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="4ee3b-153">Dans le code précédent, `SampleAsyncActionFilter` a un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ) qui exécute la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="4ee3b-154">Plusieurs étapes de filtres</span><span class="sxs-lookup"><span data-stu-id="4ee3b-154">Multiple filter stages</span></span>

<span data-ttu-id="4ee3b-155">Vous pouvez implémenter des interfaces pour plusieurs étapes de filtres dans une même classe.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="4ee3b-156">Par exemple, la <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> classe implémente :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="4ee3b-157">Synchrone : <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> et  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="4ee3b-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="4ee3b-158">Asynchrone : <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="4ee3b-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="4ee3b-159">Implémentez la version synchrone ou asynchrone d’une interface de filtre, mais **pas** **les deux** .</span><span class="sxs-lookup"><span data-stu-id="4ee3b-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="4ee3b-160">Le runtime vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="4ee3b-161">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="4ee3b-162">Si des interfaces synchrones et asynchrones sont implémentées dans une classe, seule la méthode asynchrone est appelée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="4ee3b-163">Lorsque vous utilisez des classes abstraites telles que <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , remplacez uniquement les méthodes synchrones ou les méthodes asynchrones pour chaque type de filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous methods for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="4ee3b-164">Attributs de filtre intégrés</span><span class="sxs-lookup"><span data-stu-id="4ee3b-164">Built-in filter attributes</span></span>

<span data-ttu-id="4ee3b-165">ASP.NET Core inclut les filtres intégrés basés sur des attributs que vous pouvez définir dans une sous-classe et personnaliser.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="4ee3b-166">Par exemple, le filtre de résultats suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="4ee3b-167">Les attributs autorisent les filtres à accepter des arguments, comme indiqué dans l’exemple ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="4ee3b-168">Appliquez le `AddHeaderAttribute` à un contrôleur ou une méthode d’action et spécifiez le nom et la valeur de l’en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="4ee3b-169">Utilisez un outil tel que les [outils de développement du navigateur](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) pour examiner les en-têtes.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="4ee3b-170">Sous **en-têtes de réponse**, `author: Rick Anderson` est affiché.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="4ee3b-171">Le code suivant implémente un `ActionFilterAttribute` qui :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="4ee3b-172">Lit le titre et le nom à partir du système de configuration.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="4ee3b-173">Contrairement à l’exemple précédent, le code suivant ne nécessite pas l’ajout de paramètres de filtre au code.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="4ee3b-174">Ajoute le titre et le nom à l’en-tête de réponse.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="4ee3b-175">Les options de configuration sont fournies par le [système de configuration](xref:fundamentals/configuration/index) à l’aide du modèle d' [options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="4ee3b-176">Par exemple, à partir du *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="4ee3b-177">Dans le `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="4ee3b-178">La `PositionOptions` classe est ajoutée au conteneur de services avec la `"Position"` zone de configuration.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="4ee3b-179">Le `MyActionFilterAttribute` est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="4ee3b-180">Le code suivant illustre la `PositionOptions` classe :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="4ee3b-181">Le code suivant applique `MyActionFilterAttribute` à la `Index2` méthode :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="4ee3b-182">Sous **les en-têtes de réponse**, `author: Rick Anderson` et `Editor: Joe Smith` s’affiche lorsque le `Sample/Index2` point de terminaison est appelé.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="4ee3b-183">Le code suivant applique le `MyActionFilterAttribute` et le `AddHeaderAttribute` à la Razor page :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="4ee3b-184">Les filtres ne peuvent pas être appliqués aux Razor méthodes de gestionnaire de page.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="4ee3b-185">Ils peuvent être appliqués au modèle de Razor page ou globalement.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="4ee3b-186">Plusieurs des interfaces de filtre ont des attributs correspondants qui peuvent être utilisés comme classes de base pour des implémentations personnalisées.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="4ee3b-187">Les attributs de filtre :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="4ee3b-188">Étendues de filtre et ordre d’exécution</span><span class="sxs-lookup"><span data-stu-id="4ee3b-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="4ee3b-189">Un filtre peut être ajouté au pipeline à l’une des trois *étendues* suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="4ee3b-190">Utilisation d’un attribut sur une action de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="4ee3b-191">Les attributs de filtre ne peuvent pas être appliqués aux Razor méthodes de gestionnaire de pages.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="4ee3b-192">Utilisation d’un attribut sur un contrôleur ou une Razor page.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="4ee3b-193">Globalement pour tous les contrôleurs, actions et Razor pages, comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="4ee3b-194">Ordre d’exécution par défaut</span><span class="sxs-lookup"><span data-stu-id="4ee3b-194">Default order of execution</span></span>

<span data-ttu-id="4ee3b-195">Quand il existe plusieurs filtres pour une étape particulière du pipeline, l’étendue détermine l’ordre par défaut de l’exécution du filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="4ee3b-196">Les filtres globaux entourent les filtres de classe, qui à leur tour entourent les filtres de méthode.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="4ee3b-197">En raison de l’imbrication de filtres, le code *après* des filtres s’exécute dans l’ordre inverse du code *avant*.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="4ee3b-198">La séquence de filtre :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-198">The filter sequence:</span></span>

* <span data-ttu-id="4ee3b-199">Le code *avant* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="4ee3b-200">*Avant* le code du contrôleur et des Razor filtres de page.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="4ee3b-201">Le code *avant* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="4ee3b-202">Le code *après* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="4ee3b-203">*Après* le code du contrôleur et des Razor filtres de page.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="4ee3b-204">Le code *après* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="4ee3b-205">Voici un exemple qui illustre l’ordre dans lequel les méthodes de filtre sont appelées pour les filtres d’actions synchrones.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="4ee3b-206">Séquence</span><span class="sxs-lookup"><span data-stu-id="4ee3b-206">Sequence</span></span> | <span data-ttu-id="4ee3b-207">Étendue de filtre</span><span class="sxs-lookup"><span data-stu-id="4ee3b-207">Filter scope</span></span> | <span data-ttu-id="4ee3b-208">Méthode de filtre</span><span class="sxs-lookup"><span data-stu-id="4ee3b-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="4ee3b-209">1</span><span class="sxs-lookup"><span data-stu-id="4ee3b-209">1</span></span> | <span data-ttu-id="4ee3b-210">Global</span><span class="sxs-lookup"><span data-stu-id="4ee3b-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4ee3b-211">2</span><span class="sxs-lookup"><span data-stu-id="4ee3b-211">2</span></span> | <span data-ttu-id="4ee3b-212">Contrôleur ou Razor page</span><span class="sxs-lookup"><span data-stu-id="4ee3b-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="4ee3b-213">3</span><span class="sxs-lookup"><span data-stu-id="4ee3b-213">3</span></span> | <span data-ttu-id="4ee3b-214">Méthode</span><span class="sxs-lookup"><span data-stu-id="4ee3b-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4ee3b-215">4</span><span class="sxs-lookup"><span data-stu-id="4ee3b-215">4</span></span> | <span data-ttu-id="4ee3b-216">Méthode</span><span class="sxs-lookup"><span data-stu-id="4ee3b-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="4ee3b-217">5</span><span class="sxs-lookup"><span data-stu-id="4ee3b-217">5</span></span> | <span data-ttu-id="4ee3b-218">Contrôleur ou Razor page</span><span class="sxs-lookup"><span data-stu-id="4ee3b-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="4ee3b-219">6</span><span class="sxs-lookup"><span data-stu-id="4ee3b-219">6</span></span> | <span data-ttu-id="4ee3b-220">Global</span><span class="sxs-lookup"><span data-stu-id="4ee3b-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="4ee3b-221">Filtres au niveau du contrôleur</span><span class="sxs-lookup"><span data-stu-id="4ee3b-221">Controller level filters</span></span>

<span data-ttu-id="4ee3b-222">Chaque contrôleur qui hérite de la classe de base <xref:Microsoft.AspNetCore.Mvc.Controller> inclut les méthodes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) et [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="4ee3b-223">Ces méthodes :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-223">These methods:</span></span>

* <span data-ttu-id="4ee3b-224">Incluent dans un wrapper les filtres qui s’exécutent pour une action donnée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="4ee3b-225">`OnActionExecuting` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="4ee3b-226">`OnActionExecuted` est appelé après tous les filtres d’actions.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="4ee3b-227">`OnActionExecutionAsync` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="4ee3b-228">Le code dans le filtre après `next` s’exécute après la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="4ee3b-229">Par exemple, dans l’échantillon à télécharger, `MySampleActionFilter` est appliqué globalement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="4ee3b-230">`TestController` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-230">The `TestController`:</span></span>

* <span data-ttu-id="4ee3b-231">Applique le `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à l' `FilterTest2` action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="4ee3b-232">Remplace `OnActionExecuting` et `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="4ee3b-233">La navigation vers `https://localhost:5001/Test/FilterTest2` exécute le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-233">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="4ee3b-234">Les filtres au niveau du contrôleur définissent la propriété [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) sur `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="4ee3b-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="4ee3b-235">Les filtres au niveau du contrôleur **ne peuvent pas** être configurés pour s’exécuter après les filtres appliqués aux méthodes.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="4ee3b-236">L’ordre est expliqué dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-236">Order is explained in the next section.</span></span>

<span data-ttu-id="4ee3b-237">Pour les Razor pages, consultez [implémenter des filtres de Razor page en substituant des méthodes de filtre](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="4ee3b-238">Remplacement de l’ordre par défaut</span><span class="sxs-lookup"><span data-stu-id="4ee3b-238">Overriding the default order</span></span>

<span data-ttu-id="4ee3b-239">Vous pouvez remplacer la séquence d’exécution par défaut en implémentant <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="4ee3b-240">`IOrderedFilter` expose la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> qui est prioritaire sur l’étendue afin de déterminer l’ordre d’exécution.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="4ee3b-241">Un filtre avec une valeur `Order` inférieure :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="4ee3b-242">Exécute le code *avant* avant celui d’un filtre avec une valeur supérieure à `Order`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="4ee3b-243">Exécute le code *après* après celui d’un filtre avec une valeur `Order` supérieure.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="4ee3b-244">La `Order` propriété est définie avec un paramètre de constructeur :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="4ee3b-245">Examinez les deux filtres d’action dans le contrôleur suivant :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="4ee3b-246">Un filtre global est ajouté dans `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="4ee3b-247">Les 3 filtres s’exécutent dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="4ee3b-248">La propriété `Order` remplace l’étendue lors de la détermination de l’ordre dans lequel les filtres s’exécutent.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="4ee3b-249">Les filtres sont d’abord classés par ordre, puis l’étendue est utilisée pour couper les liens.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="4ee3b-250">Tous les filtres intégrés implémentent `IOrderedFilter` et affectent 0 à la valeur `Order` par défaut.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="4ee3b-251">Comme mentionné précédemment, les filtres au niveau du contrôleur définissent la propriété [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) sur `int.MinValue` pour les filtres intégrés, Scope détermine l’ordre, à moins que `Order` ne soit défini sur une valeur différente de zéro.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="4ee3b-252">Dans le code précédent, `MySampleActionFilter` a une portée globale pour qu’elle s’exécute avant `MyAction2FilterAttribute` , qui a une portée de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="4ee3b-253">Pour effectuer `MyAction2FilterAttribute` l’exécution en premier, définissez l’ordre sur `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="4ee3b-254">Pour que le filtre global `MySampleActionFilter` s’exécute en premier, définissez `Order` sur `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="4ee3b-255">Annulation et court-circuit</span><span class="sxs-lookup"><span data-stu-id="4ee3b-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="4ee3b-256">Vous pouvez court-circuiter le pipeline de filtres en définissant la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sur le paramètre <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fourni à la méthode de filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="4ee3b-257">Par exemple, le filtre de ressources suivant empêche l’exécution du reste du pipeline :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="4ee3b-258">Dans le code suivant, les filtres `ShortCircuitingResourceFilter` et `AddHeader` ciblent tous deux la méthode d’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="4ee3b-259">`ShortCircuitingResourceFilter` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="4ee3b-260">S’exécute en premier (puisqu’il s’agit d’un filtre de ressources et que `AddHeader` est un filtre d’action).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="4ee3b-261">Court-circuite le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="4ee3b-262">Le filtre `AddHeader` ne s’exécute donc jamais pour l’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="4ee3b-263">Ce comportement est le même si les deux filtres sont appliqués au niveau de la méthode d’action, à condition que `ShortCircuitingResourceFilter` soit exécuté en premier.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="4ee3b-264">`ShortCircuitingResourceFilter` s’exécute en premier en raison de son type de filtre ou de l’utilisation explicite de la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="4ee3b-265">Injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="4ee3b-265">Dependency injection</span></span>

<span data-ttu-id="4ee3b-266">Les filtres peuvent être ajoutés par type ou par instance.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="4ee3b-267">Si vous ajoutez une instance, celle-ci sera utilisée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="4ee3b-268">Si un type est ajouté, il est activé par type.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="4ee3b-269">Un filtre activé par type signifie :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-269">A type-activated filter means:</span></span>

* <span data-ttu-id="4ee3b-270">Une instance est créée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-270">An instance is created for each request.</span></span>
* <span data-ttu-id="4ee3b-271">Les dépendances de constructeur sont remplies par [l’injection de dépendances](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="4ee3b-272">Les filtres qui sont implémentés en tant qu’attributs et ajoutés directement à des classes de contrôleur ou à de méthodes d’action ne peut pas avoir de dépendances de constructeur fournies par [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="4ee3b-273">Les dépendances de constructeur ne peuvent pas être fournies par l’injection de dépendance, car :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="4ee3b-274">Les paramètres du constructeur des attributs doivent être fournis là où ils sont appliqués.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="4ee3b-275">Il s’agit d’une limitation de la façon dont les attributs fonctionnent.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="4ee3b-276">Les filtres suivants prennent en charge les dépendances de constructeur fournies à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="4ee3b-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémenté sur l’attribut.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="4ee3b-278">Les filtres précédents peuvent être appliqués à une méthode de contrôleur ou d’action :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="4ee3b-279">Des enregistreurs d’événements sont disponibles à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-279">Loggers are available from DI.</span></span> <span data-ttu-id="4ee3b-280">Toutefois, évitez la création et l’utilisation de filtres à des fins purement d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="4ee3b-281">L’[enregistrement d’infrastructure intégrée](xref:fundamentals/logging/index) fournit généralement ce qui est nécessaire pour l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="4ee3b-282">Enregistrement ajouté aux filtres :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-282">Logging added to filters:</span></span>

* <span data-ttu-id="4ee3b-283">Doit se concentrer sur les problèmes ou le comportement du domaine métier spécifique au filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="4ee3b-284">Ne doit **pas** enregistrer des actions ou d’autres événements d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="4ee3b-285">Les filtres intégrés consignent les actions et les événements du Framework.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="4ee3b-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="4ee3b-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="4ee3b-287">Les types d’implémentation du filtre de services sont enregistrés dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="4ee3b-288">Un <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> récupère une instance du filtre à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="4ee3b-289">Le code suivant affiche le `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="4ee3b-290">Dans le code suivant, `AddHeaderResultServiceFilter` est ajouté au conteneur d’injection de dépendance :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="4ee3b-291">Dans le code suivant, l’attribut `ServiceFilter` récupère une instance du filtre `AddHeaderResultServiceFilter` à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="4ee3b-292">Lors de l’utilisation de `ServiceFilterAttribute`, définir [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="4ee3b-293">Conseille que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="4ee3b-294">Le runtime ASP.NET Core ne garantit pas :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="4ee3b-295">Qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="4ee3b-296">Le filtre ne sera pas demandé à nouveau à partir du conteneur d’injection de dépendance à un stade ultérieur.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="4ee3b-297">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="4ee3b-298">L'objet <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="4ee3b-299">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="4ee3b-300">`CreateInstance` charge le type spécifié à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="4ee3b-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="4ee3b-301">TypeFilterAttribute</span></span>

<span data-ttu-id="4ee3b-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> est similaire à <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mais son type n’est pas résolu directement à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="4ee3b-303">Il instancie le type en utilisant <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="4ee3b-304">Étant donné que les types `TypeFilterAttribute` ne sont pas résolus directement à partir du conteneur d’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="4ee3b-305">Les types qui sont référencés avec `TypeFilterAttribute` ne doivent pas d’abord être inscrits auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="4ee3b-306">Leurs dépendances ne sont pas remplies par le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="4ee3b-307">`TypeFilterAttribute` peut éventuellement accepter des arguments de constructeur pour le type.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="4ee3b-308">Lors de l’utilisation de `TypeFilterAttribute`, définir [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="4ee3b-309">Indique que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="4ee3b-310">Le runtime ASP.NET Core ne fournit aucune garantie qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="4ee3b-311">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="4ee3b-312">L’exemple suivant indique comment passer des arguments vers un type en utilisant `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="4ee3b-313">Filtres d’autorisations</span><span class="sxs-lookup"><span data-stu-id="4ee3b-313">Authorization filters</span></span>

<span data-ttu-id="4ee3b-314">Filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-314">Authorization filters:</span></span>

* <span data-ttu-id="4ee3b-315">Sont les premiers filtres à être exécutés dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="4ee3b-316">Contrôlent l’accès aux méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-316">Control access to action methods.</span></span>
* <span data-ttu-id="4ee3b-317">Ont une méthode avant, mais pas de méthode après.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="4ee3b-318">Les filtres d’autorisations personnalisés nécessitent une infrastructure d’autorisation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="4ee3b-319">Préférez la configuration des stratégies d’autorisation ou l’écriture d’une stratégie d’autorisation personnalisée à l’écriture d’un filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="4ee3b-320">Le filtre d'autorisations intégré :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="4ee3b-321">Appelle le système d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-321">Calls the authorization system.</span></span>
* <span data-ttu-id="4ee3b-322">N’autoriser les requêtes.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-322">Does not authorize requests.</span></span>

<span data-ttu-id="4ee3b-323">Ne levez **pas** d’exceptions dans les filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="4ee3b-324">L’exception ne sera pas gérée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-324">The exception will not be handled.</span></span>
* <span data-ttu-id="4ee3b-325">Les filtres d’exceptions ne gèreront pas l’exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="4ee3b-326">Songez à émettre une stimulation quand un filtre d’autorisations se produit.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="4ee3b-327">Découvrez plus d’informations sur [l’autorisation](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="4ee3b-328">Filtres de ressources</span><span class="sxs-lookup"><span data-stu-id="4ee3b-328">Resource filters</span></span>

<span data-ttu-id="4ee3b-329">Filtres de ressources :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-329">Resource filters:</span></span>

* <span data-ttu-id="4ee3b-330">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="4ee3b-331">L’exécution inclut dans un wrapper la majeure partie du pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="4ee3b-332">Seuls les [filtres d’autorisations](#authorization-filters) s’exécutent avant les filtres de ressources.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="4ee3b-333">Les filtres de ressources sont utiles pour court-circuiter la majeure partie du pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="4ee3b-334">Par exemple, un filtre de mise en cache peut éviter le reste du pipeline dans une correspondance dans le cache.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="4ee3b-335">Exemples de filtre de ressources :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-335">Resource filter examples:</span></span>

* <span data-ttu-id="4ee3b-336">[Le filtre de ressources de court-circuit](#short-circuiting-resource-filter) illustré précédemment.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="4ee3b-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs) :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="4ee3b-338">Il empêche la liaison de données d’accéder aux données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="4ee3b-339">Il est utilisé pour les chargements de fichiers volumineux et pour empêcher que le formulaire de données ne soit lu en mémoire.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="4ee3b-340">Filtres d’actions</span><span class="sxs-lookup"><span data-stu-id="4ee3b-340">Action filters</span></span>

<span data-ttu-id="4ee3b-341">Les filtres d’action ne s’appliquent **pas** aux Razor pages.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="4ee3b-342">Razor Les pages prennent en charge <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="4ee3b-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="4ee3b-343">Pour plus d’informations, consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="4ee3b-344">Filtres d'actions :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-344">Action filters:</span></span>

* <span data-ttu-id="4ee3b-345">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="4ee3b-346">Leur exécution entoure l’exécution de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="4ee3b-347">Le code suivant montre un exemple de filtre d’actions :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="4ee3b-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fournit les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="4ee3b-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> -active la lecture des entrées dans une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="4ee3b-350"><xref:Microsoft.AspNetCore.Mvc.Controller> : permet de manipuler l’instance de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="4ee3b-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> : la définition de `Result` court-circuite l’exécution de la méthode d’action et les filtres d’actions suivants.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="4ee3b-352">Levée d’une exception dans une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="4ee3b-353">Empêche l’exécution des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="4ee3b-354">Contrairement au paramètre `Result`, est traité comme un échec plutôt que comme un résultat positif.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="4ee3b-355"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fournit `Controller` et `Result`, en plus des propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="4ee3b-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> : sa valeur est true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="4ee3b-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> : sa valeur est non Null si l’action ou un filtre d’actions précédemment exécuté a lancé une exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="4ee3b-358">Paramètre de cette propriété sur null :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-358">Setting this property to null:</span></span>

  * <span data-ttu-id="4ee3b-359">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="4ee3b-360">`Result` est exécuté comme s’il avait été retourné à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="4ee3b-361">Pour un `IAsyncActionFilter`, un appel à <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="4ee3b-362">Exécute tous les filtres d’actions suivants et la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="4ee3b-363">Retourne `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="4ee3b-364">Pour court-circuiter, attribuez <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> à une instance de résultat et n’appelez pas le `next` (le `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="4ee3b-365">L’infrastructure fournit un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="4ee3b-366">Le filtre d’actions `OnActionExecuting` peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="4ee3b-367">Valider l’état du modèle.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-367">Validate model state.</span></span>
* <span data-ttu-id="4ee3b-368">Renvoyer une erreur si l’état n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="4ee3b-369">Les contrôleurs annotés avec l' `[ApiController]` attribut valident automatiquement l’état du modèle et retournent une réponse 400.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="4ee3b-370">Pour plus d’informations, consultez [Réponses HTTP 400 automatiques](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="4ee3b-371">La méthode `OnActionExecuted` s’exécute après la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="4ee3b-372">Et peut voir et manipuler les résultats de l’action via la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="4ee3b-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> est défini sur true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="4ee3b-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> est défini sur une valeur non Null si l’action ou un filtre d’actions suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="4ee3b-375">Le fait de définir `Exception` avec la valeur null :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="4ee3b-376">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="4ee3b-377">`ActionExecutedContext.Result` est exécuté comme s’il avait été retourné normalement à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="4ee3b-378">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="4ee3b-378">Exception filters</span></span>

<span data-ttu-id="4ee3b-379">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-379">Exception filters:</span></span>

* <span data-ttu-id="4ee3b-380">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="4ee3b-381">Ils peuvent être utilisés pour implémenter des stratégies de gestion des erreurs communes.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="4ee3b-382">L’échantillon de filtre d’exceptions suivant utilise un affichage personnalisé des erreurs pour afficher des détails sur les exceptions qui se produisent pendant que l’application est en développement :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="4ee3b-383">Le code suivant teste le filtre d’exception :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="4ee3b-384">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-384">Exception filters:</span></span>

* <span data-ttu-id="4ee3b-385">N’ont pas d’événements avant et après.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-385">Don't have before and after events.</span></span>
* <span data-ttu-id="4ee3b-386">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="4ee3b-387">Gérer les exceptions non gérées qui se produisent lors de la création d’une Razor page ou d’un contrôleur, la [liaison de modèle](xref:mvc/models/model-binding), les filtres d’action ou les méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="4ee3b-388">N’interceptent **pas** les exceptions qui se produisent dans l’exécution des filtres de ressources, des filtres de résultats ou des résultats MVC.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="4ee3b-389">Pour gérer une exception, définissez la propriété <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> sur `true` ou écrivez une réponse.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="4ee3b-390">Ceci arrête la propagation de l’exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-390">This stops propagation of the exception.</span></span> <span data-ttu-id="4ee3b-391">Un filtre d’exceptions ne peut pas changer une exception en « réussite ».</span><span class="sxs-lookup"><span data-stu-id="4ee3b-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="4ee3b-392">Seul un filtre d’actions peut le faire.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-392">Only an action filter can do that.</span></span>

<span data-ttu-id="4ee3b-393">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-393">Exception filters:</span></span>

* <span data-ttu-id="4ee3b-394">Conviennent pour intercepter des exceptions qui se produisent dans des actions.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="4ee3b-395">N’offrent pas la même souplesse que le middleware (intergiciel) de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="4ee3b-396">Privilégiez le middleware pour la gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="4ee3b-397">Utilisez les filtres d’exceptions uniquement lorsque la gestion des erreurs *diffère* selon la méthode d’action appelée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="4ee3b-398">Par exemple, votre application peut avoir des méthodes d’action à la fois pour des points de terminaison d’API et pour des affichages/HTML.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="4ee3b-399">Les points de terminaison d’API peuvent retourner des informations d’erreur au format JSON, tandis que les actions basées sur une vue peuvent retourner une page d’erreur au format HTML.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="4ee3b-400">Filtres de résultats</span><span class="sxs-lookup"><span data-stu-id="4ee3b-400">Result filters</span></span>

<span data-ttu-id="4ee3b-401">Filtres de résultats :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-401">Result filters:</span></span>

* <span data-ttu-id="4ee3b-402">Implémenter une interface :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-402">Implement an interface:</span></span>
  * <span data-ttu-id="4ee3b-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="4ee3b-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="4ee3b-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="4ee3b-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="4ee3b-405">Leur exécution entoure l’exécution de résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="4ee3b-406">IResultFilter et IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="4ee3b-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="4ee3b-407">Le code suivant indique un filtre de résultats qui ajoute un en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="4ee3b-408">Le type de résultat à exécuter dépend de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="4ee3b-409">Une action qui retourne une vue comprend tout le traitement Razor dans le cadre du <xref:Microsoft.AspNetCore.Mvc.ViewResult> en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="4ee3b-410">Une méthode d’API peut effectuer une sérialisation dans le cadre de l’exécution du résultat.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="4ee3b-411">En savoir plus sur les [résultats des actions](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="4ee3b-412">Les filtres de résultats ne sont exécutés que lorsqu’une action ou un filtre d’action produit un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="4ee3b-413">Les filtres de résultats ne sont pas exécutés dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="4ee3b-414">Un filtre d’autorisation ou des courts-circuits de filtre de ressources le pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="4ee3b-415">Un filtre d’exception gère une exception en générant un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="4ee3b-416">La méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> peut court-circuiter l’exécution du résultat d’action et les filtres de résultats suivants en définissant <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> sur `true`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="4ee3b-417">Écrivez dans l’objet de réponse quand vous court-circuitez pour éviter de générer une réponse vide.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="4ee3b-418">Levée d’une exception dans `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="4ee3b-419">Empêche l’exécution du résultat de l’action et des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="4ee3b-420">Est traité comme un échec au lieu d’un résultat réussi.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="4ee3b-421">Lorsque la <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> méthode s’exécute, la réponse a probablement déjà été envoyée au client.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="4ee3b-422">Si la réponse a déjà été envoyée au client, elle ne peut pas être modifiée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="4ee3b-423">`ResultExecutedContext.Canceled` est défini sur `true` si l’exécution du résultat d’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="4ee3b-424">`ResultExecutedContext.Exception` est défini sur une valeur non Null si le résultat d’action ou un filtre de résultats suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="4ee3b-425">La définition `Exception` de la valeur null gère efficacement une exception et empêche la levée de l’exception plus tard dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="4ee3b-426">Il n’existe aucun moyen fiable pour écrire des données dans une réponse lors de la gestion d’une exception dans un filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="4ee3b-427">Si les en-têtes ont été vidés pour le client lorsqu’un résultat d’action lance une exception, il n’existe aucun mécanisme fiable pour envoyer un code d’échec.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="4ee3b-428">Pour un <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, un appel à `await next` sur le <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> exécute tous les filtres de résultats suivants et le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="4ee3b-429">Pour court-circuiter, définissez [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) sur `true` et n’appelez pas `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="4ee3b-430">L’infrastructure fournit un `ResultFilterAttribute` abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="4ee3b-431">La classe [AddHeaderAttribute](#add-header-attribute) ci-dessus est un exemple d’un attribut de filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="4ee3b-432">IAlwaysRunResultFilter et IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="4ee3b-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="4ee3b-433">Les interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> déclarent une implémentation <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> qui s’exécute pour tous les résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="4ee3b-434">Cela comprend les résultats d’action produits par :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-434">This includes action results produced by:</span></span>

* <span data-ttu-id="4ee3b-435">Filtres d’autorisation et filtres de ressources qui court-circuitent.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="4ee3b-436">Filtres d’exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-436">Exception filters.</span></span>

<span data-ttu-id="4ee3b-437">Par exemple, le filtre suivant exécute et définit toujours un résultat d’action (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) avec un code d’état *422 Entité non traitée* en cas d’échec de la négociation de contenu :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="4ee3b-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="4ee3b-438">IFilterFactory</span></span>

<span data-ttu-id="4ee3b-439">L'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="4ee3b-440">Par conséquent, une instance de `IFilterFactory` peut être utilisée comme instance de `IFilterMetadata` n’importe où dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="4ee3b-441">Quan se prépare à appeler le filtre, il tente de le caster en `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="4ee3b-442">Si ce cast réussit, la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> est appelée pour créer l’instance `IFilterMetadata` qui sera appelée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="4ee3b-443">La conception est flexible, car il n’est pas nécessaire de définir explicitement le pipeline de filtres exact quand l’application démarre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="4ee3b-444">`IFilterFactory.IsReusable`:</span><span class="sxs-lookup"><span data-stu-id="4ee3b-444">`IFilterFactory.IsReusable`:</span></span>

* <span data-ttu-id="4ee3b-445">Est un indicateur par la fabrique que l’instance de filtre créée par la fabrique peut être réutilisée en dehors de la portée de la demande dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-445">Is a hint by the factory that the filter instance created by the factory may be reused outside of the request scope it was created within.</span></span>
* <span data-ttu-id="4ee3b-446">\***Not** _ doit être utilisé avec un filtre qui dépend de services dont la durée de vie n’est pas le singleton.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-446">Should \***not** _ be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="4ee3b-447">Le runtime ASP.NET Core ne garantit pas :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-447">The ASP.NET Core runtime doesn't guarantee:</span></span>

<span data-ttu-id="4ee3b-448">_ Qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-448">_ That a single instance of the filter will be created.</span></span>
* <span data-ttu-id="4ee3b-449">Le filtre ne sera pas demandé à nouveau à partir du conteneur d’injection de dépendance à un stade ultérieur.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-449">The filter will not be re-requested from the DI container at some later point.</span></span>

[!WARNING]<span data-ttu-id="4ee3b-450"> Configurez uniquement `IFilterFactory.IsReusable` pour retourner la valeur `true` si la source des filtres n’est pas ambiguë, si les filtres sont sans État et peuvent être utilisés en toute sécurité sur plusieurs requêtes http.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-450"> Only configure `IFilterFactory.IsReusable` to return `true` if the source of the filters is unambiguous, the filters are stateless, and are safe to use across multiple HTTP requests.</span></span> <span data-ttu-id="4ee3b-451">Par exemple, ne retournent pas de filtres de DI qui sont enregistrés comme étendus ou transitoires si `IFilterFactory.IsReusable` retourne `true`</span><span class="sxs-lookup"><span data-stu-id="4ee3b-451">For instance, do not return filters from DI that are registered as scoped or transient if `IFilterFactory.IsReusable` returns `true`</span></span>

<span data-ttu-id="4ee3b-452">Une autre approche pour la création de filtres est d’implémenter `IFilterFactory` à l’aide des implémentations d’attribut personnalisé :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-452">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="4ee3b-453">Le filtre est appliqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-453">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="4ee3b-454">Testez le code précédent en exécutant l' [exemple Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="4ee3b-454">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="4ee3b-455">Appeler les outils de développement F12.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-455">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="4ee3b-456">Accédez à `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-456">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="4ee3b-457">Les outils de développement F12 affichent les en-têtes de réponse suivants ajoutés par l’exemple de code :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-457">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="4ee3b-458">**Auteur :**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="4ee3b-458">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="4ee3b-459">**globaladdheader :** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="4ee3b-459">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="4ee3b-460">**interne :**`My header`</span><span class="sxs-lookup"><span data-stu-id="4ee3b-460">**internal:** `My header`</span></span>

<span data-ttu-id="4ee3b-461">Le code précédent crée l’en-tête de réponse **interne :** `My header`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-461">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="4ee3b-462">IFilterFactory implémenté sur un attribut</span><span class="sxs-lookup"><span data-stu-id="4ee3b-462">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="4ee3b-463">Les filtres qui implémentent `IFilterFactory` sont utiles pour les filtres qui :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-463">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="4ee3b-464">Ne nécessitent pas le passage de paramètres.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-464">Don't require passing parameters.</span></span>
* <span data-ttu-id="4ee3b-465">Disposent de dépendances de constructeur qui doivent être remplies par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-465">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="4ee3b-466">L'objet <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-466"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="4ee3b-467">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-467">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="4ee3b-468">`CreateInstance` charge le type spécifié à partir du conteneur de services (injection de dépendances).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-468">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="4ee3b-469">Le code suivant illustre trois approches pour appliquer `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-469">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="4ee3b-470">Dans le code précédent, la décoration de la méthode avec `[SampleActionFilter]` est la meilleure approche pour appliquer `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-470">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="4ee3b-471">Utilisation d’un intergiciel dans le pipeline de filtres</span><span class="sxs-lookup"><span data-stu-id="4ee3b-471">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="4ee3b-472">Les filtres de ressources fonctionnent comme un [intergiciel](xref:fundamentals/middleware/index) dans la mesure où ils entourent l’exécution de tout ce qui vient plus tard dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-472">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="4ee3b-473">Toutefois, les filtres diffèrent des intergiciels (middleware) en ce sens qu’ils font partie du runtime, ce qui signifie qu’ils ont accès au contexte et aux constructions.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-473">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="4ee3b-474">Pour utiliser un intergiciel comme filtre, créez un type avec une méthode `Configure` qui spécifie l’intergiciel que vous voulez injecter dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-474">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="4ee3b-475">Voici un exemple qui utilise l’intergiciel de localisation pour établir la culture actuelle d’une requête :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-475">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="4ee3b-476">Utilisez le <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> pour exécuter l’intergiciel :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-476">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="4ee3b-477">Les filtres d’intergiciels s’exécutent à la même étape du pipeline de filtres en tant que filtres de ressources, avant la liaison de modèle et après le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-477">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="4ee3b-478">Actions suivantes</span><span class="sxs-lookup"><span data-stu-id="4ee3b-478">Next actions</span></span>

* <span data-ttu-id="4ee3b-479">Consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-479">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="4ee3b-480">Pour expérimenter les filtres, [téléchargez, testez et modifiez l’échantillon Github](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-480">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4ee3b-481">Par [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4ee3b-481">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4ee3b-482">Les *filtres* dans ASP.NET Core permettent d’exécuter du code avant ou après des étapes spécifiques dans le pipeline de traitement des requêtes.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-482">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="4ee3b-483">Les filtres intégrés gèrent notamment les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-483">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="4ee3b-484">Autorisation (empêcher un utilisateur non autorisé d’accéder à des ressources).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-484">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="4ee3b-485">Mise en cache des réponses (court-circuitage du pipeline de requêtes pour retourner une réponse mise en cache).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-485">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="4ee3b-486">Il est possible de créer des filtres personnalisés pour gérer les problèmes transversaux.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-486">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="4ee3b-487">Les exemples de problèmes transversaux incluent la gestion des erreurs, la mise en cache, la configuration, l’autorisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-487">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="4ee3b-488">Les filtres évitent la duplication de code.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-488">Filters avoid duplicating code.</span></span> <span data-ttu-id="4ee3b-489">Par exemple, un filtre d’exceptions de gestion des erreurs peut servir à consolider la gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-489">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="4ee3b-490">Ce document s’applique aux Razor pages, aux contrôleurs d’API et aux contrôleurs avec des vues.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-490">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="4ee3b-491">[Afficher ou télécharger l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([comment télécharger](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-491">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="4ee3b-492">Fonctionnement des filtres</span><span class="sxs-lookup"><span data-stu-id="4ee3b-492">How filters work</span></span>

<span data-ttu-id="4ee3b-493">Les filtres s’exécutent dans le *pipeline des appels d’action ASP.NET Core*, parfois appelé *pipeline de filtres*.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-493">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="4ee3b-494">Le pipeline de filtres s’exécute après la sélection par ASP.NET Core de l’action à exécuter.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-494">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La requête est traitée via un autre intergiciel, un intergiciel de routage, une sélection d’action et le pipeline d’appels d’action ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="4ee3b-497">Types de filtres</span><span class="sxs-lookup"><span data-stu-id="4ee3b-497">Filter types</span></span>

<span data-ttu-id="4ee3b-498">Chaque type de filtre est exécuté dans une étape différente du pipeline de filtres :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-498">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="4ee3b-499">Les [filtres d’autorisations](#authorization-filters) s’exécutent en premier et sont utilisés pour déterminer si l’utilisateur est autorisé pour la requête.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-499">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="4ee3b-500">Les filtres d’autorisations peuvent court-circuiter le pipeline si la requête n’est pas autorisée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-500">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="4ee3b-501">[Filtres de ressources](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="4ee3b-501">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="4ee3b-502">Exécuter après l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-502">Run after authorization.</span></span>  
  * <span data-ttu-id="4ee3b-503"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> peut exécuter du code avant le reste du pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-503"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="4ee3b-504">Par exemple, `OnResourceExecuting` peut exécuter du code avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-504">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="4ee3b-505"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> peut exécuter du code une fois que le reste du pipeline terminé.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-505"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="4ee3b-506">Les [filtres d’actions](#action-filters) peuvent exécuter du code juste avant et juste après l’appel d’une méthode d’action individuelle.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-506">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="4ee3b-507">Ils peuvent être utilisés pour manipuler les arguments passés à une action et le résultat retourné depuis l’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-507">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="4ee3b-508">Les filtres d’action **ne sont pas** pris en charge dans les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-508">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="4ee3b-509">Les [filtres d’exceptions](#exception-filters) sont utilisés pour appliquer des stratégies globales à des exceptions non gérées qui se produisent avant que des éléments aient été écrits dans le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-509">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="4ee3b-510">Les [filtres de résultats](#result-filters) peuvent exécuter du code juste avant et juste après l’exécution des résultats d’une action individuelle.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-510">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="4ee3b-511">Ils s’exécutent uniquement au terme de l’exécution de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-511">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="4ee3b-512">Ils sont utiles pour la logique qui doit entourer l’exécution de la vue ou du formateur.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-512">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="4ee3b-513">Le diagramme suivant montre comment ces types de filtres interagissent dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-513">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La requête est traitée à travers les filtres d’autorisations, les filtres de ressources, la liaison de modèle, les filtres d’actions, l’exécution d’actions et la conversion des résultats d’actions, les filtres d’exceptions, les filtres de résultats et l’exécution de résultats.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="4ee3b-516">Implémentation</span><span class="sxs-lookup"><span data-stu-id="4ee3b-516">Implementation</span></span>

<span data-ttu-id="4ee3b-517">Les filtres prennent en charge les implémentations synchrones et asynchrones via différentes définitions d’interface.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-517">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="4ee3b-518">Les filtres synchrones peuvent exécuter du code avant (`On-Stage-Executing`) et après (`On-Stage-Executed`) leur étape de pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-518">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="4ee3b-519">Par exemple, `OnActionExecuting` est appelé avant l’appel de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-519">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="4ee3b-520">`OnActionExecuted` est appelé après le retour de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-520">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="4ee3b-521">Les filtres asynchrones définissent une méthode `On-Stage-ExecutionAsync` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-521">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="4ee3b-522">Dans le code précédent, le `SampleAsyncActionFilter` a un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) qui exécute la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-522">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="4ee3b-523">Chacune des méthodes `On-Stage-ExecutionAsync` prennent un `FilterType-ExecutionDelegate` qui exécute l’étape de pipeline du filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-523">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="4ee3b-524">Plusieurs étapes de filtres</span><span class="sxs-lookup"><span data-stu-id="4ee3b-524">Multiple filter stages</span></span>

<span data-ttu-id="4ee3b-525">Vous pouvez implémenter des interfaces pour plusieurs étapes de filtres dans une même classe.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-525">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="4ee3b-526">Par exemple, la classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implémente `IActionFilter`, `IResultFilter` et leurs équivalents asynchrones.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-526">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="4ee3b-527">Implémentez la version synchrone ou asynchrone d’une interface de filtre, mais **pas** **les deux** .</span><span class="sxs-lookup"><span data-stu-id="4ee3b-527">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="4ee3b-528">Le runtime vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-528">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="4ee3b-529">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-529">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="4ee3b-530">Si des interfaces synchrones et asynchrones sont implémentées dans une classe, seule la méthode asynchrone est appelée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-530">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="4ee3b-531">Quand vous utilisez des classes abstraites comme <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, vous devez remplacer seulement les méthodes synchrones ou la méthode asynchrone pour chaque type de filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-531">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="4ee3b-532">Attributs de filtre intégrés</span><span class="sxs-lookup"><span data-stu-id="4ee3b-532">Built-in filter attributes</span></span>

<span data-ttu-id="4ee3b-533">ASP.NET Core inclut les filtres intégrés basés sur des attributs que vous pouvez définir dans une sous-classe et personnaliser.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-533">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="4ee3b-534">Par exemple, le filtre de résultats suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-534">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="4ee3b-535">Les attributs autorisent les filtres à accepter des arguments, comme indiqué dans l’exemple ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-535">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="4ee3b-536">Appliquez le `AddHeaderAttribute` à un contrôleur ou une méthode d’action et spécifiez le nom et la valeur de l’en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-536">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="4ee3b-537">Plusieurs des interfaces de filtre ont des attributs correspondants qui peuvent être utilisés comme classes de base pour des implémentations personnalisées.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-537">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="4ee3b-538">Les attributs de filtre :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-538">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="4ee3b-539">Étendues de filtre et ordre d’exécution</span><span class="sxs-lookup"><span data-stu-id="4ee3b-539">Filter scopes and order of execution</span></span>

<span data-ttu-id="4ee3b-540">Un filtre peut être ajouté au pipeline à l’une des trois *étendues* suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-540">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="4ee3b-541">À l’aide d’un attribut sur une action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-541">Using an attribute on an action.</span></span>
* <span data-ttu-id="4ee3b-542">À l’aide d’un attribut sur un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-542">Using an attribute on a controller.</span></span>
* <span data-ttu-id="4ee3b-543">Dans le monde entier pour tous les contrôleurs et actions, comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-543">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="4ee3b-544">Le code précédent ajoute trois filtres globalement à l’aide de la collection [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-544">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="4ee3b-545">Ordre d’exécution par défaut</span><span class="sxs-lookup"><span data-stu-id="4ee3b-545">Default order of execution</span></span>

<span data-ttu-id="4ee3b-546">Lorsqu’il existe plusieurs filtres *du même type*, l’étendue détermine l’ordre par défaut de l’exécution du filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-546">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="4ee3b-547">Filtres globaux-filtres de classe surround.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-547">Global filters surround class filters.</span></span> <span data-ttu-id="4ee3b-548">Les filtres de classe entourent les filtres de méthode.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-548">Class filters surround method filters.</span></span>

<span data-ttu-id="4ee3b-549">En raison de l’imbrication de filtres, le code *après* des filtres s’exécute dans l’ordre inverse du code *avant*.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-549">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="4ee3b-550">La séquence de filtre :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-550">The filter sequence:</span></span>

* <span data-ttu-id="4ee3b-551">Le code *avant* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-551">The *before* code of global filters.</span></span>
  * <span data-ttu-id="4ee3b-552">Le code *avant* des filtres du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-552">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="4ee3b-553">Le code *avant* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-553">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="4ee3b-554">Le code *après* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-554">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="4ee3b-555">Le code *après* des filtres du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-555">The *after* code of controller filters.</span></span>
* <span data-ttu-id="4ee3b-556">Le code *après* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-556">The *after* code of global filters.</span></span>
  
<span data-ttu-id="4ee3b-557">Voici un exemple qui illustre l’ordre dans lequel les méthodes de filtre sont appelées pour les filtres d’actions synchrones.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-557">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="4ee3b-558">Séquence</span><span class="sxs-lookup"><span data-stu-id="4ee3b-558">Sequence</span></span> | <span data-ttu-id="4ee3b-559">Étendue de filtre</span><span class="sxs-lookup"><span data-stu-id="4ee3b-559">Filter scope</span></span> | <span data-ttu-id="4ee3b-560">Méthode de filtre</span><span class="sxs-lookup"><span data-stu-id="4ee3b-560">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="4ee3b-561">1</span><span class="sxs-lookup"><span data-stu-id="4ee3b-561">1</span></span> | <span data-ttu-id="4ee3b-562">Global</span><span class="sxs-lookup"><span data-stu-id="4ee3b-562">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4ee3b-563">2</span><span class="sxs-lookup"><span data-stu-id="4ee3b-563">2</span></span> | <span data-ttu-id="4ee3b-564">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="4ee3b-564">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4ee3b-565">3</span><span class="sxs-lookup"><span data-stu-id="4ee3b-565">3</span></span> | <span data-ttu-id="4ee3b-566">Méthode</span><span class="sxs-lookup"><span data-stu-id="4ee3b-566">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4ee3b-567">4</span><span class="sxs-lookup"><span data-stu-id="4ee3b-567">4</span></span> | <span data-ttu-id="4ee3b-568">Méthode</span><span class="sxs-lookup"><span data-stu-id="4ee3b-568">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="4ee3b-569">5</span><span class="sxs-lookup"><span data-stu-id="4ee3b-569">5</span></span> | <span data-ttu-id="4ee3b-570">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="4ee3b-570">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="4ee3b-571">6</span><span class="sxs-lookup"><span data-stu-id="4ee3b-571">6</span></span> | <span data-ttu-id="4ee3b-572">Global</span><span class="sxs-lookup"><span data-stu-id="4ee3b-572">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="4ee3b-573">Cette séquence montre que :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-573">This sequence shows:</span></span>

* <span data-ttu-id="4ee3b-574">Le filtre de méthode est imbriqué dans le filtre de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-574">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="4ee3b-575">Le filtre de contrôleur est imbriqué dans le filtre global.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-575">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-no-locrazor-page-level-filters"></a><span data-ttu-id="4ee3b-576">Filtres au niveau du contrôleur et de la Razor page</span><span class="sxs-lookup"><span data-stu-id="4ee3b-576">Controller and Razor Page level filters</span></span>

<span data-ttu-id="4ee3b-577">Chaque contrôleur qui hérite de la classe de base <xref:Microsoft.AspNetCore.Mvc.Controller> inclut les méthodes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) et [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-577">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="4ee3b-578">Ces méthodes :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-578">These methods:</span></span>

* <span data-ttu-id="4ee3b-579">Incluent dans un wrapper les filtres qui s’exécutent pour une action donnée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-579">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="4ee3b-580">`OnActionExecuting` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-580">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="4ee3b-581">`OnActionExecuted` est appelé après tous les filtres d’actions.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-581">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="4ee3b-582">`OnActionExecutionAsync` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-582">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="4ee3b-583">Le code dans le filtre après `next` s’exécute après la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-583">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="4ee3b-584">Par exemple, dans l’échantillon à télécharger, `MySampleActionFilter` est appliqué globalement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-584">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="4ee3b-585">`TestController` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-585">The `TestController`:</span></span>

* <span data-ttu-id="4ee3b-586">Applique le `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à l' `FilterTest2` action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-586">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="4ee3b-587">Remplace `OnActionExecuting` et `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-587">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="4ee3b-588">La navigation vers `https://localhost:5001/Test/FilterTest2` exécute le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-588">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="4ee3b-589">Pour les Razor pages, consultez [implémenter des filtres de Razor page en substituant des méthodes de filtre](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-589">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="4ee3b-590">Remplacement de l’ordre par défaut</span><span class="sxs-lookup"><span data-stu-id="4ee3b-590">Overriding the default order</span></span>

<span data-ttu-id="4ee3b-591">Vous pouvez remplacer la séquence d’exécution par défaut en implémentant <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-591">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="4ee3b-592">`IOrderedFilter` expose la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> qui est prioritaire sur l’étendue afin de déterminer l’ordre d’exécution.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-592">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="4ee3b-593">Un filtre avec une valeur `Order` inférieure :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-593">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="4ee3b-594">Exécute le code *avant* avant celui d’un filtre avec une valeur supérieure à `Order`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-594">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="4ee3b-595">Exécute le code *après* après celui d’un filtre avec une valeur `Order` supérieure.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-595">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="4ee3b-596">La propriété `Order` peut être définie avec un paramètre de constructeur :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-596">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="4ee3b-597">Prenez en compte les mêmes 3 filtres d’actions indiqués dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-597">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="4ee3b-598">Si la propriété `Order` du contrôleur et les filtres globaux sont définis sur 1 et 2 respectivement, l’ordre d’exécution est inversé.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-598">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="4ee3b-599">Séquence</span><span class="sxs-lookup"><span data-stu-id="4ee3b-599">Sequence</span></span> | <span data-ttu-id="4ee3b-600">Étendue de filtre</span><span class="sxs-lookup"><span data-stu-id="4ee3b-600">Filter scope</span></span> | <span data-ttu-id="4ee3b-601">Propriété`Order`</span><span class="sxs-lookup"><span data-stu-id="4ee3b-601">`Order` property</span></span> | <span data-ttu-id="4ee3b-602">Méthode de filtre</span><span class="sxs-lookup"><span data-stu-id="4ee3b-602">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="4ee3b-603">1</span><span class="sxs-lookup"><span data-stu-id="4ee3b-603">1</span></span> | <span data-ttu-id="4ee3b-604">Méthode</span><span class="sxs-lookup"><span data-stu-id="4ee3b-604">Method</span></span> | <span data-ttu-id="4ee3b-605">0</span><span class="sxs-lookup"><span data-stu-id="4ee3b-605">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4ee3b-606">2</span><span class="sxs-lookup"><span data-stu-id="4ee3b-606">2</span></span> | <span data-ttu-id="4ee3b-607">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="4ee3b-607">Controller</span></span> | <span data-ttu-id="4ee3b-608">1</span><span class="sxs-lookup"><span data-stu-id="4ee3b-608">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="4ee3b-609">3</span><span class="sxs-lookup"><span data-stu-id="4ee3b-609">3</span></span> | <span data-ttu-id="4ee3b-610">Global</span><span class="sxs-lookup"><span data-stu-id="4ee3b-610">Global</span></span> | <span data-ttu-id="4ee3b-611">2</span><span class="sxs-lookup"><span data-stu-id="4ee3b-611">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="4ee3b-612">4</span><span class="sxs-lookup"><span data-stu-id="4ee3b-612">4</span></span> | <span data-ttu-id="4ee3b-613">Global</span><span class="sxs-lookup"><span data-stu-id="4ee3b-613">Global</span></span> | <span data-ttu-id="4ee3b-614">2</span><span class="sxs-lookup"><span data-stu-id="4ee3b-614">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="4ee3b-615">5</span><span class="sxs-lookup"><span data-stu-id="4ee3b-615">5</span></span> | <span data-ttu-id="4ee3b-616">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="4ee3b-616">Controller</span></span> | <span data-ttu-id="4ee3b-617">1</span><span class="sxs-lookup"><span data-stu-id="4ee3b-617">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="4ee3b-618">6</span><span class="sxs-lookup"><span data-stu-id="4ee3b-618">6</span></span> | <span data-ttu-id="4ee3b-619">Méthode</span><span class="sxs-lookup"><span data-stu-id="4ee3b-619">Method</span></span> | <span data-ttu-id="4ee3b-620">0</span><span class="sxs-lookup"><span data-stu-id="4ee3b-620">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="4ee3b-621">La propriété `Order` remplace l’étendue lors de la détermination de l’ordre dans lequel les filtres s’exécutent.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-621">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="4ee3b-622">Les filtres sont d’abord classés par ordre, puis l’étendue est utilisée pour couper les liens.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-622">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="4ee3b-623">Tous les filtres intégrés implémentent `IOrderedFilter` et affectent 0 à la valeur `Order` par défaut.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-623">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="4ee3b-624">Pour les filtres intégrés, l’étendue détermine l’ordre, sauf si `Order` est défini sur une valeur différente de zéro.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-624">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="4ee3b-625">Annulation et court-circuit</span><span class="sxs-lookup"><span data-stu-id="4ee3b-625">Cancellation and short-circuiting</span></span>

<span data-ttu-id="4ee3b-626">Vous pouvez court-circuiter le pipeline de filtres en définissant la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sur le paramètre <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fourni à la méthode de filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-626">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="4ee3b-627">Par exemple, le filtre de ressources suivant empêche l’exécution du reste du pipeline :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-627">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="4ee3b-628">Dans le code suivant, les filtres `ShortCircuitingResourceFilter` et `AddHeader` ciblent tous deux la méthode d’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-628">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="4ee3b-629">`ShortCircuitingResourceFilter` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-629">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="4ee3b-630">S’exécute en premier (puisqu’il s’agit d’un filtre de ressources et que `AddHeader` est un filtre d’action).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-630">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="4ee3b-631">Court-circuite le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-631">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="4ee3b-632">Le filtre `AddHeader` ne s’exécute donc jamais pour l’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-632">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="4ee3b-633">Ce comportement est le même si les deux filtres sont appliqués au niveau de la méthode d’action, à condition que `ShortCircuitingResourceFilter` soit exécuté en premier.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-633">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="4ee3b-634">`ShortCircuitingResourceFilter` s’exécute en premier en raison de son type de filtre ou de l’utilisation explicite de la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-634">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="4ee3b-635">Injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="4ee3b-635">Dependency injection</span></span>

<span data-ttu-id="4ee3b-636">Les filtres peuvent être ajoutés par type ou par instance.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-636">Filters can be added by type or by instance.</span></span> <span data-ttu-id="4ee3b-637">Si vous ajoutez une instance, celle-ci sera utilisée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-637">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="4ee3b-638">Si un type est ajouté, il est activé par type.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-638">If a type is added, it's type-activated.</span></span> <span data-ttu-id="4ee3b-639">Un filtre activé par type signifie :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-639">A type-activated filter means:</span></span>

* <span data-ttu-id="4ee3b-640">Une instance est créée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-640">An instance is created for each request.</span></span>
* <span data-ttu-id="4ee3b-641">Les dépendances de constructeur sont remplies par [l’injection de dépendances](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-641">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="4ee3b-642">Les filtres qui sont implémentés en tant qu’attributs et ajoutés directement à des classes de contrôleur ou à de méthodes d’action ne peut pas avoir de dépendances de constructeur fournies par [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-642">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="4ee3b-643">Les dépendances de constructeur ne peuvent pas être fournies par l’injection de dépendance, car :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-643">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="4ee3b-644">Les paramètres du constructeur des attributs doivent être fournis là où ils sont appliqués.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-644">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="4ee3b-645">Il s’agit d’une limitation de la façon dont les attributs fonctionnent.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-645">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="4ee3b-646">Les filtres suivants prennent en charge les dépendances de constructeur fournies à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-646">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="4ee3b-647"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémenté sur l’attribut.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-647"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="4ee3b-648">Les filtres précédents peuvent être appliqués à une méthode de contrôleur ou d’action :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-648">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="4ee3b-649">Des enregistreurs d’événements sont disponibles à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-649">Loggers are available from DI.</span></span> <span data-ttu-id="4ee3b-650">Toutefois, évitez la création et l’utilisation de filtres à des fins purement d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-650">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="4ee3b-651">L’[enregistrement d’infrastructure intégrée](xref:fundamentals/logging/index) fournit généralement ce qui est nécessaire pour l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-651">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="4ee3b-652">Enregistrement ajouté aux filtres :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-652">Logging added to filters:</span></span>

* <span data-ttu-id="4ee3b-653">Doit se concentrer sur les problèmes ou le comportement du domaine métier spécifique au filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-653">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="4ee3b-654">Ne doit **pas** enregistrer des actions ou d’autres événements d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-654">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="4ee3b-655">Les filtres intégrés enregistrent des actions et des événements d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-655">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="4ee3b-656">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="4ee3b-656">ServiceFilterAttribute</span></span>

<span data-ttu-id="4ee3b-657">Les types d’implémentation du filtre de services sont enregistrés dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-657">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="4ee3b-658">Un <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> récupère une instance du filtre à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-658">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="4ee3b-659">Le code suivant affiche le `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-659">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="4ee3b-660">Dans le code suivant, `AddHeaderResultServiceFilter` est ajouté au conteneur d’injection de dépendance :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-660">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="4ee3b-661">Dans le code suivant, l’attribut `ServiceFilter` récupère une instance du filtre `AddHeaderResultServiceFilter` à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-661">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="4ee3b-662">Lors de l’utilisation de `ServiceFilterAttribute`, définir [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-662">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="4ee3b-663">Conseille que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-663">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="4ee3b-664">Le runtime ASP.NET Core ne garantit pas :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-664">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="4ee3b-665">Qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-665">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="4ee3b-666">Le filtre ne sera pas demandé à nouveau à partir du conteneur d’injection de dépendance à un stade ultérieur.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-666">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="4ee3b-667">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-667">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="4ee3b-668">L'objet <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-668"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="4ee3b-669">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-669">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="4ee3b-670">`CreateInstance` charge le type spécifié à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-670">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="4ee3b-671">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="4ee3b-671">TypeFilterAttribute</span></span>

<span data-ttu-id="4ee3b-672"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> est similaire à <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mais son type n’est pas résolu directement à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-672"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="4ee3b-673">Il instancie le type en utilisant <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-673">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="4ee3b-674">Étant donné que les types `TypeFilterAttribute` ne sont pas résolus directement à partir du conteneur d’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-674">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="4ee3b-675">Les types qui sont référencés avec `TypeFilterAttribute` ne doivent pas d’abord être inscrits auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-675">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="4ee3b-676">Leurs dépendances ne sont pas remplies par le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-676">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="4ee3b-677">`TypeFilterAttribute` peut éventuellement accepter des arguments de constructeur pour le type.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-677">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="4ee3b-678">Lors de l’utilisation de `TypeFilterAttribute`, définir [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-678">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="4ee3b-679">Indique que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-679">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="4ee3b-680">Le runtime ASP.NET Core ne fournit aucune garantie qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-680">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="4ee3b-681">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-681">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="4ee3b-682">L’exemple suivant indique comment passer des arguments vers un type en utilisant `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-682">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="4ee3b-683">Filtres d’autorisations</span><span class="sxs-lookup"><span data-stu-id="4ee3b-683">Authorization filters</span></span>

<span data-ttu-id="4ee3b-684">Filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-684">Authorization filters:</span></span>

* <span data-ttu-id="4ee3b-685">Sont les premiers filtres à être exécutés dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-685">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="4ee3b-686">Contrôlent l’accès aux méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-686">Control access to action methods.</span></span>
* <span data-ttu-id="4ee3b-687">Ont une méthode avant, mais pas de méthode après.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-687">Have a before method, but no after method.</span></span>

<span data-ttu-id="4ee3b-688">Les filtres d’autorisations personnalisés nécessitent une infrastructure d’autorisation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-688">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="4ee3b-689">Préférez la configuration des stratégies d’autorisation ou l’écriture d’une stratégie d’autorisation personnalisée à l’écriture d’un filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-689">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="4ee3b-690">Le filtre d'autorisations intégré :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-690">The built-in authorization filter:</span></span>

* <span data-ttu-id="4ee3b-691">Appelle le système d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-691">Calls the authorization system.</span></span>
* <span data-ttu-id="4ee3b-692">N’autoriser les requêtes.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-692">Does not authorize requests.</span></span>

<span data-ttu-id="4ee3b-693">Ne levez **pas** d’exceptions dans les filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-693">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="4ee3b-694">L’exception ne sera pas gérée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-694">The exception will not be handled.</span></span>
* <span data-ttu-id="4ee3b-695">Les filtres d’exceptions ne gèreront pas l’exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-695">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="4ee3b-696">Songez à émettre une stimulation quand un filtre d’autorisations se produit.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-696">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="4ee3b-697">Découvrez plus d’informations sur [l’autorisation](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-697">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="4ee3b-698">Filtres de ressources</span><span class="sxs-lookup"><span data-stu-id="4ee3b-698">Resource filters</span></span>

<span data-ttu-id="4ee3b-699">Filtres de ressources :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-699">Resource filters:</span></span>

* <span data-ttu-id="4ee3b-700">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-700">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="4ee3b-701">L’exécution inclut dans un wrapper la majeure partie du pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-701">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="4ee3b-702">Seuls les [filtres d’autorisations](#authorization-filters) s’exécutent avant les filtres de ressources.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-702">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="4ee3b-703">Les filtres de ressources sont utiles pour court-circuiter la majeure partie du pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-703">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="4ee3b-704">Par exemple, un filtre de mise en cache peut éviter le reste du pipeline dans une correspondance dans le cache.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-704">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="4ee3b-705">Exemples de filtre de ressources :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-705">Resource filter examples:</span></span>

* <span data-ttu-id="4ee3b-706">[Le filtre de ressources de court-circuit](#short-circuiting-resource-filter) illustré précédemment.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-706">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="4ee3b-707">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs) :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-707">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="4ee3b-708">Il empêche la liaison de données d’accéder aux données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-708">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="4ee3b-709">Il est utilisé pour les chargements de fichiers volumineux et pour empêcher que le formulaire de données ne soit lu en mémoire.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-709">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="4ee3b-710">Filtres d’actions</span><span class="sxs-lookup"><span data-stu-id="4ee3b-710">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4ee3b-711">Les filtres d’action ne s’appliquent **pas** aux Razor pages.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-711">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="4ee3b-712">Razor Les pages prennent en charge <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="4ee3b-712">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="4ee3b-713">Pour plus d’informations, consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-713">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="4ee3b-714">Filtres d'actions :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-714">Action filters:</span></span>

* <span data-ttu-id="4ee3b-715">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-715">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="4ee3b-716">Leur exécution entoure l’exécution de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-716">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="4ee3b-717">Le code suivant montre un exemple de filtre d’actions :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-717">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="4ee3b-718"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fournit les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-718">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="4ee3b-719"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> : permet de lire les entrées vers une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-719"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="4ee3b-720"><xref:Microsoft.AspNetCore.Mvc.Controller> : permet de manipuler l’instance de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-720"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="4ee3b-721"><xref:System.Web.Mvc.ActionExecutingContext.Result> : la définition de `Result` court-circuite l’exécution de la méthode d’action et les filtres d’actions suivants.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-721"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="4ee3b-722">Levée d’une exception dans une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-722">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="4ee3b-723">Empêche l’exécution des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-723">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="4ee3b-724">Contrairement au paramètre `Result`, est traité comme un échec plutôt que comme un résultat positif.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-724">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="4ee3b-725"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fournit `Controller` et `Result`, en plus des propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-725">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="4ee3b-726"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> : sa valeur est true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-726"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="4ee3b-727"><xref:System.Web.Mvc.ActionExecutedContext.Exception> : sa valeur est non Null si l’action ou un filtre d’actions précédemment exécuté a lancé une exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-727"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="4ee3b-728">Paramètre de cette propriété sur null :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-728">Setting this property to null:</span></span>

  * <span data-ttu-id="4ee3b-729">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-729">Effectively handles the exception.</span></span>
  * <span data-ttu-id="4ee3b-730">`Result` est exécuté comme s’il avait été retourné à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-730">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="4ee3b-731">Pour un `IAsyncActionFilter`, un appel à <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-731">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="4ee3b-732">Exécute tous les filtres d’actions suivants et la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-732">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="4ee3b-733">Retourne `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-733">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="4ee3b-734">Pour court-circuiter, attribuez <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> à une instance de résultat et n’appelez pas le `next` (le `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-734">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="4ee3b-735">L’infrastructure fournit un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-735">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="4ee3b-736">Le filtre d’actions `OnActionExecuting` peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-736">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="4ee3b-737">Valider l’état du modèle.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-737">Validate model state.</span></span>
* <span data-ttu-id="4ee3b-738">Renvoyer une erreur si l’état n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-738">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="4ee3b-739">La méthode `OnActionExecuted` s’exécute après la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-739">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="4ee3b-740">Et peut voir et manipuler les résultats de l’action via la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-740">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="4ee3b-741"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> est défini sur true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-741"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="4ee3b-742"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> est défini sur une valeur non Null si l’action ou un filtre d’actions suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-742"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="4ee3b-743">Le fait de définir `Exception` avec la valeur null :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-743">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="4ee3b-744">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-744">Effectively handles an exception.</span></span>
  * <span data-ttu-id="4ee3b-745">`ActionExecutedContext.Result` est exécuté comme s’il avait été retourné normalement à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-745">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="4ee3b-746">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="4ee3b-746">Exception filters</span></span>

<span data-ttu-id="4ee3b-747">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-747">Exception filters:</span></span>

* <span data-ttu-id="4ee3b-748">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-748">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="4ee3b-749">Ils peuvent être utilisés pour implémenter des stratégies de gestion des erreurs communes.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-749">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="4ee3b-750">L’échantillon de filtre d’exceptions suivant utilise un affichage personnalisé des erreurs pour afficher des détails sur les exceptions qui se produisent pendant que l’application est en développement :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-750">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="4ee3b-751">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-751">Exception filters:</span></span>

* <span data-ttu-id="4ee3b-752">N’ont pas d’événements avant et après.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-752">Don't have before and after events.</span></span>
* <span data-ttu-id="4ee3b-753">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-753">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="4ee3b-754">Gérer les exceptions non gérées qui se produisent lors de la création d’une Razor page ou d’un contrôleur, la [liaison de modèle](xref:mvc/models/model-binding), les filtres d’action ou les méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-754">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="4ee3b-755">N’interceptent **pas** les exceptions qui se produisent dans l’exécution des filtres de ressources, des filtres de résultats ou des résultats MVC.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-755">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="4ee3b-756">Pour gérer une exception, définissez la propriété <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> sur `true` ou écrivez une réponse.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-756">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="4ee3b-757">Ceci arrête la propagation de l’exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-757">This stops propagation of the exception.</span></span> <span data-ttu-id="4ee3b-758">Un filtre d’exceptions ne peut pas changer une exception en « réussite ».</span><span class="sxs-lookup"><span data-stu-id="4ee3b-758">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="4ee3b-759">Seul un filtre d’actions peut le faire.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-759">Only an action filter can do that.</span></span>

<span data-ttu-id="4ee3b-760">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-760">Exception filters:</span></span>

* <span data-ttu-id="4ee3b-761">Conviennent pour intercepter des exceptions qui se produisent dans des actions.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-761">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="4ee3b-762">N’offrent pas la même souplesse que le middleware (intergiciel) de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-762">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="4ee3b-763">Privilégiez le middleware pour la gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-763">Prefer middleware for exception handling.</span></span> <span data-ttu-id="4ee3b-764">Utilisez les filtres d’exceptions uniquement lorsque la gestion des erreurs *diffère* selon la méthode d’action appelée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-764">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="4ee3b-765">Par exemple, votre application peut avoir des méthodes d’action à la fois pour des points de terminaison d’API et pour des affichages/HTML.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-765">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="4ee3b-766">Les points de terminaison d’API peuvent retourner des informations d’erreur au format JSON, tandis que les actions basées sur une vue peuvent retourner une page d’erreur au format HTML.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-766">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="4ee3b-767">Filtres de résultats</span><span class="sxs-lookup"><span data-stu-id="4ee3b-767">Result filters</span></span>

<span data-ttu-id="4ee3b-768">Filtres de résultats :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-768">Result filters:</span></span>

* <span data-ttu-id="4ee3b-769">Implémenter une interface :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-769">Implement an interface:</span></span>
  * <span data-ttu-id="4ee3b-770"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="4ee3b-770"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="4ee3b-771"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="4ee3b-771"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="4ee3b-772">Leur exécution entoure l’exécution de résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-772">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="4ee3b-773">IResultFilter et IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="4ee3b-773">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="4ee3b-774">Le code suivant indique un filtre de résultats qui ajoute un en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-774">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="4ee3b-775">Le type de résultat à exécuter dépend de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-775">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="4ee3b-776">Une action retournant un affichage inclut tous les traitements Razor dans le cadre de la <xref:Microsoft.AspNetCore.Mvc.ViewResult> à exécuter.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-776">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="4ee3b-777">Une méthode d’API peut effectuer une sérialisation dans le cadre de l’exécution du résultat.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-777">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="4ee3b-778">En savoir plus sur les [résultats des actions](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-778">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="4ee3b-779">Les filtres de résultats ne sont exécutés que lorsqu’une action ou un filtre d’action produit un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-779">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="4ee3b-780">Les filtres de résultats ne sont pas exécutés dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-780">Result filters are not executed when:</span></span>

* <span data-ttu-id="4ee3b-781">Un filtre d’autorisation ou des courts-circuits de filtre de ressources le pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-781">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="4ee3b-782">Un filtre d’exception gère une exception en générant un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-782">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="4ee3b-783">La méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> peut court-circuiter l’exécution du résultat d’action et les filtres de résultats suivants en définissant <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> sur `true`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-783">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="4ee3b-784">Écrivez dans l’objet de réponse quand vous court-circuitez pour éviter de générer une réponse vide.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-784">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="4ee3b-785">La levée d’une exception dans `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-785">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="4ee3b-786">Empêche l’exécution du résultat d’action et des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-786">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="4ee3b-787">Est traitée comme une erreur et non comme une réussite.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-787">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="4ee3b-788">Lorsque la <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> méthode s’exécute, la réponse a probablement déjà été envoyée au client.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-788">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="4ee3b-789">Si la réponse a déjà été envoyée au client, elle ne peut plus être modifiée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-789">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="4ee3b-790">`ResultExecutedContext.Canceled` est défini sur `true` si l’exécution du résultat d’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-790">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="4ee3b-791">`ResultExecutedContext.Exception` est défini sur une valeur non Null si le résultat d’action ou un filtre de résultats suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-791">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="4ee3b-792">Le paramètre de `Exception` sur Null gère effectivement une exception et évite à l’exception d’être à nouveau levée par ASP.NET Core plus loin dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-792">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="4ee3b-793">Il n’existe aucun moyen fiable pour écrire des données dans une réponse lors de la gestion d’une exception dans un filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-793">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="4ee3b-794">Si les en-têtes ont été vidés pour le client lorsqu’un résultat d’action lance une exception, il n’existe aucun mécanisme fiable pour envoyer un code d’échec.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-794">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="4ee3b-795">Pour un <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, un appel à `await next` sur le <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> exécute tous les filtres de résultats suivants et le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-795">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="4ee3b-796">Pour court-circuiter, définissez [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) sur `true` et n’appelez pas `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-796">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="4ee3b-797">L’infrastructure fournit un `ResultFilterAttribute` abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-797">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="4ee3b-798">La classe [AddHeaderAttribute](#add-header-attribute) ci-dessus est un exemple d’un attribut de filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-798">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="4ee3b-799">IAlwaysRunResultFilter et IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="4ee3b-799">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="4ee3b-800">Les interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> déclarent une implémentation <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> qui s’exécute pour tous les résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-800">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="4ee3b-801">Cela comprend les résultats d’action produits par :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-801">This includes action results produced by:</span></span>

* <span data-ttu-id="4ee3b-802">Filtres d’autorisation et filtres de ressources qui court-circuitent.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-802">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="4ee3b-803">Filtres d’exception.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-803">Exception filters.</span></span>

<span data-ttu-id="4ee3b-804">Par exemple, le filtre suivant exécute et définit toujours un résultat d’action (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) avec un code d’état *422 Entité non traitée* en cas d’échec de la négociation de contenu :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-804">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="4ee3b-805">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="4ee3b-805">IFilterFactory</span></span>

<span data-ttu-id="4ee3b-806">L'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-806"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="4ee3b-807">Par conséquent, une instance de `IFilterFactory` peut être utilisée comme instance de `IFilterMetadata` n’importe où dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-807">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="4ee3b-808">Quan se prépare à appeler le filtre, il tente de le caster en `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-808">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="4ee3b-809">Si ce cast réussit, la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> est appelée pour créer l’instance `IFilterMetadata` qui sera appelée.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-809">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="4ee3b-810">La conception est flexible, car il n’est pas nécessaire de définir explicitement le pipeline de filtres exact quand l’application démarre.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-810">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="4ee3b-811">Une autre approche pour la création de filtres est d’implémenter `IFilterFactory` à l’aide des implémentations d’attribut personnalisé :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-811">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="4ee3b-812">Le code précédent peut être testé en exécutant l’[échantillon de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-812">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="4ee3b-813">Appeler les outils de développement F12.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-813">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="4ee3b-814">Accédez à `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-814">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="4ee3b-815">Les outils de développement F12 affichent les en-têtes de réponse suivants ajoutés par l’exemple de code :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-815">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="4ee3b-816">**Auteur :**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="4ee3b-816">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="4ee3b-817">**globaladdheader :** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="4ee3b-817">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="4ee3b-818">**interne :**`My header`</span><span class="sxs-lookup"><span data-stu-id="4ee3b-818">**internal:** `My header`</span></span>

<span data-ttu-id="4ee3b-819">Le code précédent crée l’en-tête de réponse **interne :** `My header`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-819">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="4ee3b-820">IFilterFactory implémenté sur un attribut</span><span class="sxs-lookup"><span data-stu-id="4ee3b-820">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="4ee3b-821">Les filtres qui implémentent `IFilterFactory` sont utiles pour les filtres qui :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-821">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="4ee3b-822">Ne nécessitent pas le passage de paramètres.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-822">Don't require passing parameters.</span></span>
* <span data-ttu-id="4ee3b-823">Disposent de dépendances de constructeur qui doivent être remplies par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-823">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="4ee3b-824">L'objet <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-824"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="4ee3b-825">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-825">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="4ee3b-826">`CreateInstance` charge le type spécifié à partir du conteneur de services (injection de dépendances).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-826">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="4ee3b-827">Le code suivant illustre trois approches pour appliquer `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-827">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="4ee3b-828">Dans le code précédent, la décoration de la méthode avec `[SampleActionFilter]` est la meilleure approche pour appliquer `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-828">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="4ee3b-829">Utilisation d’un intergiciel dans le pipeline de filtres</span><span class="sxs-lookup"><span data-stu-id="4ee3b-829">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="4ee3b-830">Les filtres de ressources fonctionnent comme un [intergiciel](xref:fundamentals/middleware/index) dans la mesure où ils entourent l’exécution de tout ce qui vient plus tard dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-830">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="4ee3b-831">Les filtres diffèrent cependant d’un intergiciel dans la mesure où ils font partie du runtime ASP.NET Core, ce qui signifie qu’ils ont accès au contexte et aux constructions ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-831">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="4ee3b-832">Pour utiliser un intergiciel comme filtre, créez un type avec une méthode `Configure` qui spécifie l’intergiciel que vous voulez injecter dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-832">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="4ee3b-833">Voici un exemple qui utilise l’intergiciel de localisation pour établir la culture actuelle d’une requête :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-833">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="4ee3b-834">Utilisez le <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> pour exécuter l’intergiciel :</span><span class="sxs-lookup"><span data-stu-id="4ee3b-834">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="4ee3b-835">Les filtres d’intergiciels s’exécutent à la même étape du pipeline de filtres en tant que filtres de ressources, avant la liaison de modèle et après le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ee3b-835">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="4ee3b-836">Actions suivantes</span><span class="sxs-lookup"><span data-stu-id="4ee3b-836">Next actions</span></span>

* <span data-ttu-id="4ee3b-837">Consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-837">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="4ee3b-838">Pour expérimenter les filtres, [téléchargez, testez et modifiez l’échantillon Github](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="4ee3b-838">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
