---
title: BlazorCycle de vie ASP.net Core
author: guardrex
description: Découvrez comment utiliser les Razor méthodes de cycle de vie des composants dans les Blazor applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 6e9d2c3180fb9e4c3e5ccc0b6d8e17183f78d698
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109843"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="f2424-103">BlazorCycle de vie ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f2424-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="f2424-104">L' Blazor infrastructure comprend des méthodes de cycle de vie synchrones et asynchrones.</span><span class="sxs-lookup"><span data-stu-id="f2424-104">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="f2424-105">Substituez les méthodes de cycle de vie pour effectuer des opérations supplémentaires sur les composants lors de l’initialisation et du rendu des composants.</span><span class="sxs-lookup"><span data-stu-id="f2424-105">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="f2424-106">Les diagrammes suivants illustrent le Blazor cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="f2424-106">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="f2424-107">Les méthodes de cycle de vie sont définies avec des exemples dans les sections suivantes de cet article.</span><span class="sxs-lookup"><span data-stu-id="f2424-107">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="f2424-108">Événements de cycle de vie des composants :</span><span class="sxs-lookup"><span data-stu-id="f2424-108">Component lifecycle events:</span></span>

1. <span data-ttu-id="f2424-109">Si le composant est rendu pour la première fois sur une demande :</span><span class="sxs-lookup"><span data-stu-id="f2424-109">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="f2424-110">Créez l’instance du composant.</span><span class="sxs-lookup"><span data-stu-id="f2424-110">Create the component's instance.</span></span>
   * <span data-ttu-id="f2424-111">Effectuez l’injection de propriété.</span><span class="sxs-lookup"><span data-stu-id="f2424-111">Perform property injection.</span></span> <span data-ttu-id="f2424-112">Exécutez [`SetParametersAsync`](#before-parameters-are-set).</span><span class="sxs-lookup"><span data-stu-id="f2424-112">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="f2424-113">Appelez [`OnInitialized{Async}`](#component-initialization-methods) .</span><span class="sxs-lookup"><span data-stu-id="f2424-113">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="f2424-114">Si un <xref:System.Threading.Tasks.Task> est retourné, <xref:System.Threading.Tasks.Task> est attendu et le composant est rendu.</span><span class="sxs-lookup"><span data-stu-id="f2424-114">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="f2424-115">Si un <xref:System.Threading.Tasks.Task> n’est pas retourné, le composant est rendu.</span><span class="sxs-lookup"><span data-stu-id="f2424-115">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="f2424-116">Appelez [`OnParametersSet{Async}`](#after-parameters-are-set) et affichez le composant.</span><span class="sxs-lookup"><span data-stu-id="f2424-116">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="f2424-117">Si un <xref:System.Threading.Tasks.Task> est retourné à partir de `OnParametersSetAsync` , <xref:System.Threading.Tasks.Task> est attendu, puis le composant est restitué à un rendu.</span><span class="sxs-lookup"><span data-stu-id="f2424-117">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Événements de cycle de vie d’un composant ::: No-Loc (Razor) ::: Component dans ::: No-Loc (éblouissant) :::](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="f2424-119">Traitement des événements Document Object Model (DOM) :</span><span class="sxs-lookup"><span data-stu-id="f2424-119">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="f2424-120">Le gestionnaire d’événements est exécuté.</span><span class="sxs-lookup"><span data-stu-id="f2424-120">The event handler is run.</span></span>
1. <span data-ttu-id="f2424-121">Si un <xref:System.Threading.Tasks.Task> est retourné, <xref:System.Threading.Tasks.Task> est attendu, puis le composant est rendu.</span><span class="sxs-lookup"><span data-stu-id="f2424-121">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="f2424-122">Si un <xref:System.Threading.Tasks.Task> n’est pas retourné, le composant est rendu.</span><span class="sxs-lookup"><span data-stu-id="f2424-122">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Traitement des événements Document Object Model (DOM)](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="f2424-124">Le `Render` cycle de vie :</span><span class="sxs-lookup"><span data-stu-id="f2424-124">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="f2424-125">Évitez les opérations de rendu supplémentaires sur le composant :</span><span class="sxs-lookup"><span data-stu-id="f2424-125">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="f2424-126">Après le premier rendu.</span><span class="sxs-lookup"><span data-stu-id="f2424-126">After the first render.</span></span>
   * <span data-ttu-id="f2424-127">Lorsque [`ShouldRender`](#suppress-ui-refreshing) est `false` .</span><span class="sxs-lookup"><span data-stu-id="f2424-127">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="f2424-128">Générez la diffation de l’arborescence de rendu (différence) et le rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="f2424-128">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="f2424-129">Attendez le DOM à mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="f2424-129">Await the DOM to update.</span></span>
1. <span data-ttu-id="f2424-130">Appelez [`OnAfterRender{Async}`](#after-component-render) .</span><span class="sxs-lookup"><span data-stu-id="f2424-130">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Cycle de vie du rendu](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="f2424-132">Les développeurs appellent pour [`StateHasChanged`](#state-changes) générer un rendu.</span><span class="sxs-lookup"><span data-stu-id="f2424-132">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="f2424-133">Pour plus d’informations, consultez <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="f2424-133">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="f2424-134">Méthodes de cycle de vie</span><span class="sxs-lookup"><span data-stu-id="f2424-134">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="f2424-135">Les paramètres Before sont définis</span><span class="sxs-lookup"><span data-stu-id="f2424-135">Before parameters are set</span></span>

<span data-ttu-id="f2424-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> définit les paramètres fournis par le parent du composant dans l’arborescence de rendu ou à partir des paramètres de routage.</span><span class="sxs-lookup"><span data-stu-id="f2424-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree or from route parameters.</span></span> <span data-ttu-id="f2424-137">En remplaçant la méthode, le code de développeur peut interagir directement avec les <xref:Microsoft.AspNetCore.Components.ParameterView> paramètres de.</span><span class="sxs-lookup"><span data-stu-id="f2424-137">By overriding the method, developer code can interact directly with the <xref:Microsoft.AspNetCore.Components.ParameterView>'s parameters.</span></span>

<span data-ttu-id="f2424-138">Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> affecte la `Param` valeur du paramètre à `value` si l’analyse d’un paramètre d’itinéraire pour `Param` est réussie.</span><span class="sxs-lookup"><span data-stu-id="f2424-138">In the following example, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> assigns the `Param` parameter's value to `value` if parsing a route parameter for `Param` is successful.</span></span> <span data-ttu-id="f2424-139">Dans le cas `value` contraire `null` , la valeur est affichée par le composant.</span><span class="sxs-lookup"><span data-stu-id="f2424-139">When `value` isn't `null`, the value is displayed by the component.</span></span>

<span data-ttu-id="f2424-140">Bien que la [correspondance des paramètres d’itinéraire ne](xref:blazor/fundamentals/routing#route-parameters)respecte pas la casse, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> ne met en correspondance que les noms de paramètres respectant la casse dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="f2424-140">Although [route parameter matching is case insensitive](xref:blazor/fundamentals/routing#route-parameters), <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> only matches case sensitive parameter names in the route template.</span></span> <span data-ttu-id="f2424-141">L’exemple suivant est requis pour utiliser `/{Param?}` , et non `/{param?}` , afin d’extraire la valeur.</span><span class="sxs-lookup"><span data-stu-id="f2424-141">The following example is required to use `/{Param?}`, not `/{param?}`, in order to get the value.</span></span> <span data-ttu-id="f2424-142">Si `/{param?}` est utilisé dans ce scénario, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> retourne `false` et `message` n’a pas la valeur String.</span><span class="sxs-lookup"><span data-stu-id="f2424-142">If `/{param?}` is used in this scenario, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> returns `false` and `message` isn't set to either string.</span></span>

<span data-ttu-id="f2424-143">`Pages/SetParametersAsyncExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="f2424-143">`Pages/SetParametersAsyncExample.razor`:</span></span>

```razor
@page "/setparametersasync-example/{Param?}"

<h1>SetParametersAsync Example</h1>

<p>@message</p>

@code {
    private string message;

    [Parameter]
    public string Param { get; set; }

    public override async Task SetParametersAsync(ParameterView parameters)
    {
        if (parameters.TryGetValue<string>(nameof(Param), out var value))
        {
            if (value is null)
            {
                message = "The value of 'Param' is null.";
            }
            else
            {
                message = $"The value of 'Param' is {value}.";
            }
        }

        await base.SetParametersAsync(parameters);
    }
}
```

<span data-ttu-id="f2424-144"><xref:Microsoft.AspNetCore.Components.ParameterView> contient l’ensemble des valeurs de paramètre pour le composant chaque fois que <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> est appelé.</span><span class="sxs-lookup"><span data-stu-id="f2424-144"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="f2424-145">L’implémentation par défaut de <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> définit la valeur de chaque propriété avec [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) l' [ `[CascadingParameter]` attribut](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) ou ayant une valeur correspondante dans le <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="f2424-145">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="f2424-146">Les paramètres qui n’ont pas de valeur correspondante dans <xref:Microsoft.AspNetCore.Components.ParameterView> ne sont pas modifiés.</span><span class="sxs-lookup"><span data-stu-id="f2424-146">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="f2424-147">Si [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) n’est pas appelé, le code personnalisé peut interpréter la valeur des paramètres entrants de la façon requise.</span><span class="sxs-lookup"><span data-stu-id="f2424-147">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="f2424-148">Par exemple, il n’est pas nécessaire d’assigner les paramètres entrants aux propriétés de la classe.</span><span class="sxs-lookup"><span data-stu-id="f2424-148">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="f2424-149">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="f2424-149">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="f2424-150">Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="f2424-150">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="f2424-151">Méthodes d’initialisation de composant</span><span class="sxs-lookup"><span data-stu-id="f2424-151">Component initialization methods</span></span>

<span data-ttu-id="f2424-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> sont appelés lorsque le composant est initialisé après avoir reçu ses paramètres initiaux de son composant parent dans <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="f2424-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="f2424-153">Utilisez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> lorsque le composant exécute une opération asynchrone et doit être actualisé lorsque l’opération est terminée.</span><span class="sxs-lookup"><span data-stu-id="f2424-153">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="f2424-154">Pour une opération synchrone, remplacez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="f2424-154">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="f2424-155">Pour effectuer une opération asynchrone, substituez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> et utilisez l' [`await`](/dotnet/csharp/language-reference/operators/await) opérateur sur l’opération :</span><span class="sxs-lookup"><span data-stu-id="f2424-155">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="f2424-156">Blazor Server applications qui [préaffichent le contenu](xref:blazor/fundamentals/signalr#render-mode) de l’appel à <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *deux reprises*:</span><span class="sxs-lookup"><span data-stu-id="f2424-156">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/signalr#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="f2424-157">Une fois lorsque le composant est initialement restitué de manière statique dans le cadre de la page.</span><span class="sxs-lookup"><span data-stu-id="f2424-157">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="f2424-158">Une deuxième fois lorsque le navigateur établit une connexion au serveur.</span><span class="sxs-lookup"><span data-stu-id="f2424-158">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="f2424-159">Pour empêcher <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> l’exécution à deux reprises du code du développeur, consultez la section [reconnexion avec état après le prérendu](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="f2424-159">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="f2424-160">Pendant Blazor Server le prérendu d’une application, certaines actions, telles que l’appel de JavaScript, ne sont pas possibles, car une connexion avec le navigateur n’a pas été établie.</span><span class="sxs-lookup"><span data-stu-id="f2424-160">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="f2424-161">Les composants peuvent avoir besoin d’être restitués différemment lorsqu’ils sont prérendus.</span><span class="sxs-lookup"><span data-stu-id="f2424-161">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="f2424-162">Pour plus d’informations, consultez la section [détecter quand l’application est un prérendu](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="f2424-162">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="f2424-163">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="f2424-163">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="f2424-164">Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="f2424-164">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="f2424-165">Une fois les paramètres définis</span><span class="sxs-lookup"><span data-stu-id="f2424-165">After parameters are set</span></span>

<span data-ttu-id="f2424-166"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ou <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> sont appelés :</span><span class="sxs-lookup"><span data-stu-id="f2424-166"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="f2424-167">Une fois le composant initialisé dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> ou <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="f2424-167">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="f2424-168">Lorsque le composant parent est à nouveau rendu et fournit :</span><span class="sxs-lookup"><span data-stu-id="f2424-168">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="f2424-169">Seuls les types immuables primitifs connus dont au moins un paramètre a été modifié.</span><span class="sxs-lookup"><span data-stu-id="f2424-169">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="f2424-170">Tous les paramètres de type complexe.</span><span class="sxs-lookup"><span data-stu-id="f2424-170">Any complex-typed parameters.</span></span> <span data-ttu-id="f2424-171">L’infrastructure ne peut pas savoir si les valeurs d’un paramètre de type complexe ont muté en interne, donc elle traite le jeu de paramètres comme étant modifié.</span><span class="sxs-lookup"><span data-stu-id="f2424-171">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="f2424-172">Un travail asynchrone lors de l’application de paramètres et de valeurs de propriété doit se produire pendant l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> événement de cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="f2424-172">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="f2424-173">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="f2424-173">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="f2424-174">Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="f2424-174">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="f2424-175">Après le rendu du composant</span><span class="sxs-lookup"><span data-stu-id="f2424-175">After component render</span></span>

<span data-ttu-id="f2424-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sont appelées après la fin d’un rendu d’un composant.</span><span class="sxs-lookup"><span data-stu-id="f2424-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="f2424-177">Les références d’élément et de composant sont remplies à ce stade.</span><span class="sxs-lookup"><span data-stu-id="f2424-177">Element and component references are populated at this point.</span></span> <span data-ttu-id="f2424-178">Utilisez cette étape pour effectuer des étapes d’initialisation supplémentaires à l’aide du contenu rendu, par exemple l’activation de bibliothèques JavaScript tierces qui opèrent sur les éléments DOM rendus.</span><span class="sxs-lookup"><span data-stu-id="f2424-178">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="f2424-179">Le `firstRender` paramètre pour <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="f2424-179">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="f2424-180">A la valeur `true` la première fois que l’instance de composant est restituée.</span><span class="sxs-lookup"><span data-stu-id="f2424-180">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="f2424-181">Peut être utilisé pour s’assurer que le travail d’initialisation n’est effectué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="f2424-181">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="f2424-182">Le travail asynchrone immédiatement après le rendu doit se produire pendant l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> événement de cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="f2424-182">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="f2424-183">Même si vous retournez un <xref:System.Threading.Tasks.Task> à partir de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , l’infrastructure ne planifie pas un cycle de rendu supplémentaire pour votre composant une fois cette tâche terminée.</span><span class="sxs-lookup"><span data-stu-id="f2424-183">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="f2424-184">Cela permet d’éviter une boucle de rendu infinie.</span><span class="sxs-lookup"><span data-stu-id="f2424-184">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="f2424-185">Elle est différente des autres méthodes de cycle de vie, qui planifient un cycle de rendu supplémentaire une fois que la tâche retournée est terminée.</span><span class="sxs-lookup"><span data-stu-id="f2424-185">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="f2424-186"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *ne sont pas appelées pendant le processus de prérendu sur le serveur*.</span><span class="sxs-lookup"><span data-stu-id="f2424-186"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="f2424-187">Les méthodes sont appelées lorsque le composant est rendu de manière interactive une fois le prérendu terminé.</span><span class="sxs-lookup"><span data-stu-id="f2424-187">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="f2424-188">Lors du prérendu de l’application :</span><span class="sxs-lookup"><span data-stu-id="f2424-188">When the app prerenders:</span></span>

1. <span data-ttu-id="f2424-189">Le composant s’exécute sur le serveur pour produire un balisage HTML statique dans la réponse HTTP.</span><span class="sxs-lookup"><span data-stu-id="f2424-189">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="f2424-190">Pendant cette phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ne sont pas appelés.</span><span class="sxs-lookup"><span data-stu-id="f2424-190">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="f2424-191">Quand `blazor.server.js` ou `blazor.webassembly.js` Démarrer dans le navigateur, le composant est redémarré en mode de rendu interactif.</span><span class="sxs-lookup"><span data-stu-id="f2424-191">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="f2424-192">Après le redémarrage d’un composant, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **sont** appelés parce que l’application n’est plus à l’intérieur de la phase de prérendu.</span><span class="sxs-lookup"><span data-stu-id="f2424-192">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="f2424-193">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="f2424-193">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="f2424-194">Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="f2424-194">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="f2424-195">Supprimer l’actualisation de l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="f2424-195">Suppress UI refreshing</span></span>

<span data-ttu-id="f2424-196">Substituez <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pour supprimer l’actualisation de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f2424-196">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="f2424-197">Si l’implémentation retourne `true` , l’interface utilisateur est actualisée :</span><span class="sxs-lookup"><span data-stu-id="f2424-197">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="f2424-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> est appelé chaque fois que le composant est restitué.</span><span class="sxs-lookup"><span data-stu-id="f2424-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="f2424-199">Même si <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> est substitué, le composant est toujours restitué initialement.</span><span class="sxs-lookup"><span data-stu-id="f2424-199">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="f2424-200">Pour plus d’informations, consultez <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span><span class="sxs-lookup"><span data-stu-id="f2424-200">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="f2424-201">Modifications d'état</span><span class="sxs-lookup"><span data-stu-id="f2424-201">State changes</span></span>

<span data-ttu-id="f2424-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifie le composant que son état a changé.</span><span class="sxs-lookup"><span data-stu-id="f2424-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="f2424-203">Le cas échéant, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> l’appel de entraîne le rerendu du composant.</span><span class="sxs-lookup"><span data-stu-id="f2424-203">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="f2424-204"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> est appelé automatiquement pour les <xref:Microsoft.AspNetCore.Components.EventCallback> méthodes.</span><span class="sxs-lookup"><span data-stu-id="f2424-204"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="f2424-205">Pour plus d’informations, consultez <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="f2424-205">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="f2424-206">Pour plus d’informations, consultez <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="f2424-206">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="f2424-207">Gérer les actions asynchrones incomplètes au rendu</span><span class="sxs-lookup"><span data-stu-id="f2424-207">Handle incomplete async actions at render</span></span>

<span data-ttu-id="f2424-208">Les actions asynchrones exécutées dans des événements de cycle de vie peuvent ne pas être terminées avant le rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="f2424-208">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="f2424-209">Les objets peuvent être `null` ou être remplis de façon incomplète avec des données pendant l’exécution de la méthode Lifecycle.</span><span class="sxs-lookup"><span data-stu-id="f2424-209">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="f2424-210">Fournissez une logique de rendu pour confirmer que les objets sont initialisés.</span><span class="sxs-lookup"><span data-stu-id="f2424-210">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="f2424-211">Affichez les éléments d’interface utilisateur d’espace réservé (par exemple, un message de chargement) tandis que les objets sont `null` .</span><span class="sxs-lookup"><span data-stu-id="f2424-211">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="f2424-212">Dans le `FetchData` composant des Blazor modèles, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> est substitué pour recevoir des données de prévision de manière asynchrone ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="f2424-212">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="f2424-213">Lorsque `forecasts` a `null` la valeur, un message de chargement est affiché à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f2424-213">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="f2424-214">Une fois la `Task` retournée <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> terminée, le composant est rerendu avec l’État mis à jour.</span><span class="sxs-lookup"><span data-stu-id="f2424-214">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="f2424-215">`Pages/FetchData.razor` dans le Blazor Server modèle :</span><span class="sxs-lookup"><span data-stu-id="f2424-215">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

## <a name="handle-errors"></a><span data-ttu-id="f2424-216">des erreurs</span><span class="sxs-lookup"><span data-stu-id="f2424-216">Handle errors</span></span>

<span data-ttu-id="f2424-217">Pour plus d’informations sur la gestion des erreurs lors de l’exécution de la méthode de cycle de vie, consultez <xref:blazor/fundamentals/handle-errors> .</span><span class="sxs-lookup"><span data-stu-id="f2424-217">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="f2424-218">Reconnexion avec état après le prérendu</span><span class="sxs-lookup"><span data-stu-id="f2424-218">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="f2424-219">Dans une Blazor Server application quand <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> est <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , le composant est initialement restitué de manière statique dans le cadre de la page.</span><span class="sxs-lookup"><span data-stu-id="f2424-219">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="f2424-220">Une fois que le navigateur a établi une connexion au serveur, le composant est *à nouveau* rendu et le composant est maintenant interactif.</span><span class="sxs-lookup"><span data-stu-id="f2424-220">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="f2424-221">Si la [`OnInitialized{Async}`](#component-initialization-methods) méthode de cycle de vie pour l’initialisation du composant est présente, la méthode est exécutée *deux fois*:</span><span class="sxs-lookup"><span data-stu-id="f2424-221">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="f2424-222">Lorsque le composant est prérendu statiquement.</span><span class="sxs-lookup"><span data-stu-id="f2424-222">When the component is prerendered statically.</span></span>
* <span data-ttu-id="f2424-223">Après l’établissement de la connexion au serveur.</span><span class="sxs-lookup"><span data-stu-id="f2424-223">After the server connection has been established.</span></span>

<span data-ttu-id="f2424-224">Cela peut entraîner une modification notable des données affichées dans l’interface utilisateur lorsque le composant est finalement restitué.</span><span class="sxs-lookup"><span data-stu-id="f2424-224">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="f2424-225">Pour éviter le double-rendu du scénario dans une Blazor Server application :</span><span class="sxs-lookup"><span data-stu-id="f2424-225">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="f2424-226">Transmettez un identificateur qui peut être utilisé pour mettre en cache l’État pendant le prérendu et pour récupérer l’état après le redémarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="f2424-226">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="f2424-227">Utilisez l’identificateur pendant le prérendu pour enregistrer l’état du composant.</span><span class="sxs-lookup"><span data-stu-id="f2424-227">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="f2424-228">Utilisez l’identificateur après le prérendu pour récupérer l’État mis en cache.</span><span class="sxs-lookup"><span data-stu-id="f2424-228">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="f2424-229">Le code suivant illustre une mise à jour `WeatherForecastService` dans une application basée sur un modèle Blazor Server qui évite le double rendu :</span><span class="sxs-lookup"><span data-stu-id="f2424-229">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="f2424-230">Pour plus d’informations sur le <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , consultez <xref:blazor/fundamentals/signalr#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="f2424-230">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/signalr#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="f2424-231">Détecter quand l’application est prérendu</span><span class="sxs-lookup"><span data-stu-id="f2424-231">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="f2424-232">Élimination des composants avec `IDisposable`</span><span class="sxs-lookup"><span data-stu-id="f2424-232">Component disposal with `IDisposable`</span></span>

<span data-ttu-id="f2424-233">Si un composant implémente <xref:System.IDisposable> , l’infrastructure appelle la [méthode de suppression](/dotnet/standard/garbage-collection/implementing-dispose) lorsque le composant est supprimé de l’interface utilisateur, où les ressources non managées peuvent être libérées.</span><span class="sxs-lookup"><span data-stu-id="f2424-233">If a component implements <xref:System.IDisposable>, the framework calls the [disposal method](/dotnet/standard/garbage-collection/implementing-dispose) when the component is removed from the UI, where unmanaged resources can be released.</span></span> <span data-ttu-id="f2424-234">La suppression peut avoir lieu à tout moment, y compris lors de [l’initialisation du composant](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="f2424-234">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="f2424-235">Le composant suivant implémente <xref:System.IDisposable> avec la [`@implements`](xref:mvc/views/razor#implements) Razor directive :</span><span class="sxs-lookup"><span data-stu-id="f2424-235">The following component implements <xref:System.IDisposable> with the [`@implements`](xref:mvc/views/razor#implements) Razor directive:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

<span data-ttu-id="f2424-236">Si un objet nécessite une suppression, une expression lambda peut être utilisée pour supprimer l’objet lorsque <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> est appelé :</span><span class="sxs-lookup"><span data-stu-id="f2424-236">If an object requires disposal, a lambda can be used to dispose of the object when <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> is called:</span></span>

<span data-ttu-id="f2424-237">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="f2424-237">`Pages/CounterWithTimerDisposal.razor`:</span></span>

```razor
@page "/counter-with-timer-disposal"
@using System.Timers
@implements IDisposable

<h1>Counter with <code>Timer</code> disposal</h1>

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    private void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    public void IDisposable.Dispose() => timer.Dispose();
}
```

<span data-ttu-id="f2424-238">L’exemple précédent apparaît dans <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system> .</span><span class="sxs-lookup"><span data-stu-id="f2424-238">The preceding example appears in <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system>.</span></span>

<span data-ttu-id="f2424-239">Pour les tâches de suppression asynchrones, utilisez `DisposeAsync` au lieu de <xref:System.IDisposable.Dispose> :</span><span class="sxs-lookup"><span data-stu-id="f2424-239">For asynchronous disposal tasks, use `DisposeAsync` instead of <xref:System.IDisposable.Dispose>:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> <span data-ttu-id="f2424-240"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>L’appel de dans `Dispose` n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="f2424-240">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="f2424-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> peut être appelé dans le cadre du détachement du convertisseur, donc demander des mises à jour de l’interface utilisateur à ce stade n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="f2424-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="f2424-242">Annule l’abonnement des gestionnaires d’événements des événements .NET.</span><span class="sxs-lookup"><span data-stu-id="f2424-242">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="f2424-243">Les exemples de [ Blazor formulaires](xref:blazor/forms-validation) suivants montrent comment annuler l’abonnement à un gestionnaire d’événements dans la `Dispose` méthode :</span><span class="sxs-lookup"><span data-stu-id="f2424-243">The following [Blazor form](xref:blazor/forms-validation) examples show how to unsubscribe an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="f2424-244">Approche de champ privé et lambda</span><span class="sxs-lookup"><span data-stu-id="f2424-244">Private field and lambda approach</span></span>

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

* <span data-ttu-id="f2424-245">Approche de la méthode privée</span><span class="sxs-lookup"><span data-stu-id="f2424-245">Private method approach</span></span>

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

<span data-ttu-id="f2424-246">Lorsque des fonctions, des méthodes ou des expressions [anonymes](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions)sont utilisées, il n’est pas nécessaire d’implémenter <xref:System.IDisposable> et de désabonner des délégués.</span><span class="sxs-lookup"><span data-stu-id="f2424-246">When [anonymous functions](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), methods or expressions, are used, it isn't necessary to implement <xref:System.IDisposable> and unsubscribe delegates.</span></span> <span data-ttu-id="f2424-247">Toutefois, l’échec de l’annulation de l’abonnement à un délégué est un problème **lorsque l’objet qui expose l’événement indique la durée de vie du composant qui inscrit le délégué**.</span><span class="sxs-lookup"><span data-stu-id="f2424-247">However, failing to unsubscribe a delegate is a problem **when the object exposing the event outlives the lifetime of the component registering the delegate**.</span></span> <span data-ttu-id="f2424-248">Dans ce cas, une fuite de mémoire est due au fait que le délégué inscrit conserve l’objet d’origine actif.</span><span class="sxs-lookup"><span data-stu-id="f2424-248">When this occurs, a memory leak results because the registered delegate keeps the original object alive.</span></span> <span data-ttu-id="f2424-249">Par conséquent, utilisez uniquement les approches suivantes lorsque vous savez que le délégué d’événement s’en supprime rapidement.</span><span class="sxs-lookup"><span data-stu-id="f2424-249">Therefore, only use the following approaches when you know that the event delegate disposes quickly.</span></span> <span data-ttu-id="f2424-250">En cas de doute sur la durée de vie des objets qui nécessitent une suppression, abonnez une méthode déléguée et supprimez correctement le délégué comme le montrent les exemples précédents.</span><span class="sxs-lookup"><span data-stu-id="f2424-250">When in doubt about the lifetime of objects that require disposal, subscribe a delegate method and properly dispose the delegate as the preceding examples show.</span></span>

* <span data-ttu-id="f2424-251">Approche de la méthode lambda anonyme (suppression explicite non requise)</span><span class="sxs-lookup"><span data-stu-id="f2424-251">Anonymous lambda method approach (explicit disposal not required)</span></span>

  ```csharp
  private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
  {
      formInvalid = !editContext.Validate();
      StateHasChanged();
  }

  protected override void OnInitialized()
  {
      editContext = new EditContext(starship);
      editContext.OnFieldChanged += (s, e) => HandleFieldChanged((editContext)s, e);
  }
  ```

* <span data-ttu-id="f2424-252">Approche d’expression lambda anonyme (suppression explicite non requise)</span><span class="sxs-lookup"><span data-stu-id="f2424-252">Anonymous lambda expression approach (explicit disposal not required)</span></span>

  ```csharp
  private ValidationMessageStore messageStore;

  [CascadingParameter]
  private EditContext CurrentEditContext { get; set; }

  protected override void OnInitialized()
  {
      ...

      messageStore = new ValidationMessageStore(CurrentEditContext);

      CurrentEditContext.OnValidationRequested += (s, e) => messageStore.Clear();
      CurrentEditContext.OnFieldChanged += (s, e) => 
          messageStore.Clear(e.FieldIdentifier);
  }
  ```

  <span data-ttu-id="f2424-253">L’exemple complet du code précédent avec des expressions lambda anonymes s’affiche dans <xref:blazor/forms-validation#validator-components> .</span><span class="sxs-lookup"><span data-stu-id="f2424-253">The full example of the preceding code with anonymous lambda expressions appears in <xref:blazor/forms-validation#validator-components>.</span></span>

<span data-ttu-id="f2424-254">Pour plus d’informations, consultez [nettoyage des ressources non managées](/dotnet/standard/garbage-collection/unmanaged) et les rubriques qui le suivent sur l’implémentation des `Dispose` `DisposeAsync` méthodes et.</span><span class="sxs-lookup"><span data-stu-id="f2424-254">For more information, see [Cleaning up unmanaged resources](/dotnet/standard/garbage-collection/unmanaged) and the topics that follow it on implementing the `Dispose` and `DisposeAsync` methods.</span></span>

## <a name="cancelable-background-work"></a><span data-ttu-id="f2424-255">Travail en arrière-plan annulable</span><span class="sxs-lookup"><span data-stu-id="f2424-255">Cancelable background work</span></span>

<span data-ttu-id="f2424-256">Les composants effectuent souvent des tâches en arrière-plan de longue durée, telles que la création d’appels réseau ( <xref:System.Net.Http.HttpClient> ) et l’interaction avec les bases de données.</span><span class="sxs-lookup"><span data-stu-id="f2424-256">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="f2424-257">Il est souhaitable d’arrêter le travail en arrière-plan pour économiser les ressources système dans plusieurs situations.</span><span class="sxs-lookup"><span data-stu-id="f2424-257">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="f2424-258">Par exemple, les opérations asynchrones en arrière-plan ne s’arrêtent pas automatiquement lorsqu’un utilisateur quitte un composant.</span><span class="sxs-lookup"><span data-stu-id="f2424-258">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="f2424-259">Les autres raisons pour lesquelles les éléments de travail en arrière-plan peuvent nécessiter l’annulation sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="f2424-259">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="f2424-260">Une tâche en arrière-plan en cours d’exécution a été démarrée avec des données d’entrée ou des paramètres de traitement défectueux.</span><span class="sxs-lookup"><span data-stu-id="f2424-260">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="f2424-261">Le jeu actuel d’éléments de travail en arrière-plan doit être remplacé par un nouvel ensemble d’éléments de travail.</span><span class="sxs-lookup"><span data-stu-id="f2424-261">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="f2424-262">La priorité des tâches en cours d’exécution doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="f2424-262">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="f2424-263">L’application doit être arrêtée pour pouvoir être redéployée sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="f2424-263">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="f2424-264">Les ressources du serveur sont limitées, ce qui nécessite la replanification des éléments de travail en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="f2424-264">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="f2424-265">Pour implémenter un modèle de travail d’arrière-plan annulable dans un composant :</span><span class="sxs-lookup"><span data-stu-id="f2424-265">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="f2424-266">Utilisez un <xref:System.Threading.CancellationTokenSource> et un <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="f2424-266">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="f2424-267">Lors de [la suppression du composant](#component-disposal-with-idisposable) et à tout moment, l’annulation est souhaitée en annulant manuellement le jeton, puis appelez [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) pour signaler que le travail en arrière-plan doit être annulé.</span><span class="sxs-lookup"><span data-stu-id="f2424-267">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="f2424-268">Une fois l’appel asynchrone retourné, appelez <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> sur le jeton.</span><span class="sxs-lookup"><span data-stu-id="f2424-268">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="f2424-269">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="f2424-269">In the following example:</span></span>

* <span data-ttu-id="f2424-270">`await Task.Delay(5000, cts.Token);` représente le travail en arrière-plan asynchrone de longue durée.</span><span class="sxs-lookup"><span data-stu-id="f2424-270">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="f2424-271">`BackgroundResourceMethod` représente une méthode d’arrière-plan de longue durée qui ne doit pas démarrer si le `Resource` est supprimé avant l’appel de la méthode.</span><span class="sxs-lookup"><span data-stu-id="f2424-271">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```

## <a name="blazor-server-reconnection-events"></a><span data-ttu-id="f2424-272">Blazor Server événements de reconnexion</span><span class="sxs-lookup"><span data-stu-id="f2424-272">Blazor Server reconnection events</span></span>

<span data-ttu-id="f2424-273">Les événements de cycle de vie des composants traités dans cet article fonctionnent séparément des [ Blazor Server gestionnaires d’événements de reconnexion de](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui).</span><span class="sxs-lookup"><span data-stu-id="f2424-273">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="f2424-274">Quand une Blazor Server application perd sa SignalR connexion au client, seules les mises à jour de l’interface utilisateur sont interrompues.</span><span class="sxs-lookup"><span data-stu-id="f2424-274">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="f2424-275">Les mises à jour de l’interface utilisateur sont reprises lorsque la connexion est rétablie.</span><span class="sxs-lookup"><span data-stu-id="f2424-275">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="f2424-276">Pour plus d’informations sur la configuration et les événements du gestionnaire de circuit, consultez <xref:blazor/fundamentals/signalr> .</span><span class="sxs-lookup"><span data-stu-id="f2424-276">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/signalr>.</span></span>
