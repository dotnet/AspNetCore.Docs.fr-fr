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
ms.openlocfilehash: 3591ba18351b89e2d5dfaef796777273c97ce98b
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751623"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="34ff9-103">BlazorCycle de vie ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="34ff9-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="34ff9-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="34ff9-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="34ff9-105">L' Blazor infrastructure comprend des méthodes de cycle de vie synchrones et asynchrones.</span><span class="sxs-lookup"><span data-stu-id="34ff9-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="34ff9-106">Substituez les méthodes de cycle de vie pour effectuer des opérations supplémentaires sur les composants lors de l’initialisation et du rendu des composants.</span><span class="sxs-lookup"><span data-stu-id="34ff9-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="34ff9-107">Les diagrammes suivants illustrent le Blazor cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="34ff9-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="34ff9-108">Les méthodes de cycle de vie sont définies avec des exemples dans les sections suivantes de cet article.</span><span class="sxs-lookup"><span data-stu-id="34ff9-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="34ff9-109">Événements de cycle de vie des composants :</span><span class="sxs-lookup"><span data-stu-id="34ff9-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="34ff9-110">Si le composant est rendu pour la première fois sur une demande :</span><span class="sxs-lookup"><span data-stu-id="34ff9-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="34ff9-111">Créez l’instance du composant.</span><span class="sxs-lookup"><span data-stu-id="34ff9-111">Create the component's instance.</span></span>
   * <span data-ttu-id="34ff9-112">Effectuez l’injection de propriété.</span><span class="sxs-lookup"><span data-stu-id="34ff9-112">Perform property injection.</span></span> <span data-ttu-id="34ff9-113">Exécutez [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="34ff9-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="34ff9-114">Appelez [`OnInitialized{Async}`](#component-initialization-methods) .</span><span class="sxs-lookup"><span data-stu-id="34ff9-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="34ff9-115">Si un <xref:System.Threading.Tasks.Task> est retourné, <xref:System.Threading.Tasks.Task> est attendu et le composant est rendu.</span><span class="sxs-lookup"><span data-stu-id="34ff9-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="34ff9-116">Si un <xref:System.Threading.Tasks.Task> n’est pas retourné, le composant est rendu.</span><span class="sxs-lookup"><span data-stu-id="34ff9-116">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="34ff9-117">Appelez [`OnParametersSet{Async}`](#after-parameters-are-set) et affichez le composant.</span><span class="sxs-lookup"><span data-stu-id="34ff9-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="34ff9-118">Si un <xref:System.Threading.Tasks.Task> est retourné à partir de `OnParametersSetAsync` , <xref:System.Threading.Tasks.Task> est attendu, puis le composant est restitué à un rendu.</span><span class="sxs-lookup"><span data-stu-id="34ff9-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Événements de cycle de vie d’un composant ::: No-Loc (Razor) ::: Component dans ::: No-Loc (éblouissant) :::](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="34ff9-120">Traitement des événements Document Object Model (DOM) :</span><span class="sxs-lookup"><span data-stu-id="34ff9-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="34ff9-121">Le gestionnaire d’événements est exécuté.</span><span class="sxs-lookup"><span data-stu-id="34ff9-121">The event handler is run.</span></span>
1. <span data-ttu-id="34ff9-122">Si un <xref:System.Threading.Tasks.Task> est retourné, <xref:System.Threading.Tasks.Task> est attendu, puis le composant est rendu.</span><span class="sxs-lookup"><span data-stu-id="34ff9-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="34ff9-123">Si un <xref:System.Threading.Tasks.Task> n’est pas retourné, le composant est rendu.</span><span class="sxs-lookup"><span data-stu-id="34ff9-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Traitement des événements Document Object Model (DOM)](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="34ff9-125">Le `Render` cycle de vie :</span><span class="sxs-lookup"><span data-stu-id="34ff9-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="34ff9-126">Évitez les opérations de rendu supplémentaires sur le composant :</span><span class="sxs-lookup"><span data-stu-id="34ff9-126">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="34ff9-127">Après le premier rendu.</span><span class="sxs-lookup"><span data-stu-id="34ff9-127">After the first render.</span></span>
   * <span data-ttu-id="34ff9-128">Lorsque [`ShouldRender`](#suppress-ui-refreshing) est `false` .</span><span class="sxs-lookup"><span data-stu-id="34ff9-128">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="34ff9-129">Générez la diffation de l’arborescence de rendu (différence) et le rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="34ff9-129">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="34ff9-130">Attendez le DOM à mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="34ff9-130">Await the DOM to update.</span></span>
1. <span data-ttu-id="34ff9-131">Appelez [`OnAfterRender{Async}`](#after-component-render) .</span><span class="sxs-lookup"><span data-stu-id="34ff9-131">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Cycle de vie du rendu](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="34ff9-133">Les développeurs appellent pour [`StateHasChanged`](#state-changes) générer un rendu.</span><span class="sxs-lookup"><span data-stu-id="34ff9-133">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="34ff9-134">Pour plus d’informations, consultez <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="34ff9-134">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="34ff9-135">Méthodes de cycle de vie</span><span class="sxs-lookup"><span data-stu-id="34ff9-135">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="34ff9-136">Les paramètres Before sont définis</span><span class="sxs-lookup"><span data-stu-id="34ff9-136">Before parameters are set</span></span>

<span data-ttu-id="34ff9-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> définit les paramètres fournis par le parent du composant dans l’arborescence de rendu ou à partir des paramètres de routage.</span><span class="sxs-lookup"><span data-stu-id="34ff9-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree or from route parameters.</span></span> <span data-ttu-id="34ff9-138">En remplaçant la méthode, le code de développeur peut interagir directement avec les <xref:Microsoft.AspNetCore.Components.ParameterView> paramètres de.</span><span class="sxs-lookup"><span data-stu-id="34ff9-138">By overriding the method, developer code can interact directly with the <xref:Microsoft.AspNetCore.Components.ParameterView>'s parameters.</span></span>

<span data-ttu-id="34ff9-139">Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> affecte la `Param` valeur du paramètre à `value` si l’analyse d’un paramètre d’itinéraire pour `Param` est réussie.</span><span class="sxs-lookup"><span data-stu-id="34ff9-139">In the following example, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> assigns the `Param` parameter's value to `value` if parsing a route parameter for `Param` is successful.</span></span> <span data-ttu-id="34ff9-140">Dans le cas `value` contraire `null` , la valeur est affichée par le `SetParametersAsyncExample` composant.</span><span class="sxs-lookup"><span data-stu-id="34ff9-140">When `value` isn't `null`, the value is displayed by the `SetParametersAsyncExample` component.</span></span>

<span data-ttu-id="34ff9-141">`Pages/SetParametersAsyncExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="34ff9-141">`Pages/SetParametersAsyncExample.razor`:</span></span>

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
            message = $"The value of 'Param' is {value}.";
        }
        else 
        {
            message = "The value of 'Param' is null.";
        }

        await base.SetParametersAsync(parameters);
    }
}
```

<span data-ttu-id="34ff9-142"><xref:Microsoft.AspNetCore.Components.ParameterView> contient l’ensemble des valeurs de paramètre pour le composant chaque fois que <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> est appelé.</span><span class="sxs-lookup"><span data-stu-id="34ff9-142"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="34ff9-143">L’implémentation par défaut de <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> définit la valeur de chaque propriété avec l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribut ou ayant [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) une valeur correspondante dans le <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="34ff9-143">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="34ff9-144">Les paramètres qui n’ont pas de valeur correspondante dans <xref:Microsoft.AspNetCore.Components.ParameterView> ne sont pas modifiés.</span><span class="sxs-lookup"><span data-stu-id="34ff9-144">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="34ff9-145">Si [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) n’est pas appelé, le code personnalisé peut interpréter la valeur des paramètres entrants de la façon requise.</span><span class="sxs-lookup"><span data-stu-id="34ff9-145">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="34ff9-146">Par exemple, il n’est pas nécessaire d’assigner les paramètres entrants aux propriétés de la classe.</span><span class="sxs-lookup"><span data-stu-id="34ff9-146">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="34ff9-147">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="34ff9-147">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="34ff9-148">Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="34ff9-148">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="34ff9-149">Méthodes d’initialisation de composant</span><span class="sxs-lookup"><span data-stu-id="34ff9-149">Component initialization methods</span></span>

<span data-ttu-id="34ff9-150"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> sont appelés lorsque le composant est initialisé après avoir reçu ses paramètres initiaux de son composant parent dans <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="34ff9-150"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="34ff9-151">Utilisez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> lorsque le composant exécute une opération asynchrone et doit être actualisé lorsque l’opération est terminée.</span><span class="sxs-lookup"><span data-stu-id="34ff9-151">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="34ff9-152">Pour une opération synchrone, remplacez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="34ff9-152">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="34ff9-153">Pour effectuer une opération asynchrone, substituez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> et utilisez l' [`await`](/dotnet/csharp/language-reference/operators/await) opérateur sur l’opération :</span><span class="sxs-lookup"><span data-stu-id="34ff9-153">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="34ff9-154">Blazor Server applications qui [préaffichent le contenu](xref:blazor/fundamentals/additional-scenarios#render-mode) de l’appel à <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *deux reprises*:</span><span class="sxs-lookup"><span data-stu-id="34ff9-154">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="34ff9-155">Une fois lorsque le composant est initialement restitué de manière statique dans le cadre de la page.</span><span class="sxs-lookup"><span data-stu-id="34ff9-155">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="34ff9-156">Une deuxième fois lorsque le navigateur établit une connexion au serveur.</span><span class="sxs-lookup"><span data-stu-id="34ff9-156">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="34ff9-157">Pour empêcher <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> l’exécution à deux reprises du code du développeur, consultez la section [reconnexion avec état après le prérendu](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="34ff9-157">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="34ff9-158">Pendant Blazor Server le prérendu d’une application, certaines actions, telles que l’appel de JavaScript, ne sont pas possibles, car une connexion avec le navigateur n’a pas été établie.</span><span class="sxs-lookup"><span data-stu-id="34ff9-158">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="34ff9-159">Les composants peuvent avoir besoin d’être restitués différemment lorsqu’ils sont prérendus.</span><span class="sxs-lookup"><span data-stu-id="34ff9-159">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="34ff9-160">Pour plus d’informations, consultez la section [détecter quand l’application est un prérendu](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="34ff9-160">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="34ff9-161">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="34ff9-161">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="34ff9-162">Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="34ff9-162">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="34ff9-163">Une fois les paramètres définis</span><span class="sxs-lookup"><span data-stu-id="34ff9-163">After parameters are set</span></span>

<span data-ttu-id="34ff9-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ou <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> sont appelés :</span><span class="sxs-lookup"><span data-stu-id="34ff9-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="34ff9-165">Une fois le composant initialisé dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> ou <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="34ff9-165">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="34ff9-166">Lorsque le composant parent est à nouveau rendu et fournit :</span><span class="sxs-lookup"><span data-stu-id="34ff9-166">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="34ff9-167">Seuls les types immuables primitifs connus dont au moins un paramètre a été modifié.</span><span class="sxs-lookup"><span data-stu-id="34ff9-167">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="34ff9-168">Tous les paramètres de type complexe.</span><span class="sxs-lookup"><span data-stu-id="34ff9-168">Any complex-typed parameters.</span></span> <span data-ttu-id="34ff9-169">L’infrastructure ne peut pas savoir si les valeurs d’un paramètre de type complexe ont muté en interne, donc elle traite le jeu de paramètres comme étant modifié.</span><span class="sxs-lookup"><span data-stu-id="34ff9-169">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="34ff9-170">Un travail asynchrone lors de l’application de paramètres et de valeurs de propriété doit se produire pendant l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> événement de cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="34ff9-170">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="34ff9-171">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="34ff9-171">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="34ff9-172">Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="34ff9-172">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="34ff9-173">Après le rendu du composant</span><span class="sxs-lookup"><span data-stu-id="34ff9-173">After component render</span></span>

<span data-ttu-id="34ff9-174"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sont appelées après la fin d’un rendu d’un composant.</span><span class="sxs-lookup"><span data-stu-id="34ff9-174"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="34ff9-175">Les références d’élément et de composant sont remplies à ce stade.</span><span class="sxs-lookup"><span data-stu-id="34ff9-175">Element and component references are populated at this point.</span></span> <span data-ttu-id="34ff9-176">Utilisez cette étape pour effectuer des étapes d’initialisation supplémentaires à l’aide du contenu rendu, par exemple l’activation de bibliothèques JavaScript tierces qui opèrent sur les éléments DOM rendus.</span><span class="sxs-lookup"><span data-stu-id="34ff9-176">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="34ff9-177">Le `firstRender` paramètre pour <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="34ff9-177">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="34ff9-178">A la valeur `true` la première fois que l’instance de composant est restituée.</span><span class="sxs-lookup"><span data-stu-id="34ff9-178">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="34ff9-179">Peut être utilisé pour s’assurer que le travail d’initialisation n’est effectué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="34ff9-179">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="34ff9-180">Le travail asynchrone immédiatement après le rendu doit se produire pendant l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> événement de cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="34ff9-180">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="34ff9-181">Même si vous retournez un <xref:System.Threading.Tasks.Task> à partir de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , l’infrastructure ne planifie pas un cycle de rendu supplémentaire pour votre composant une fois cette tâche terminée.</span><span class="sxs-lookup"><span data-stu-id="34ff9-181">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="34ff9-182">Cela permet d’éviter une boucle de rendu infinie.</span><span class="sxs-lookup"><span data-stu-id="34ff9-182">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="34ff9-183">Elle est différente des autres méthodes de cycle de vie, qui planifient un cycle de rendu supplémentaire une fois que la tâche retournée est terminée.</span><span class="sxs-lookup"><span data-stu-id="34ff9-183">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="34ff9-184"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *ne sont pas appelées pendant le processus de prérendu sur le serveur*.</span><span class="sxs-lookup"><span data-stu-id="34ff9-184"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="34ff9-185">Les méthodes sont appelées lorsque le composant est rendu de manière interactive une fois le prérendu terminé.</span><span class="sxs-lookup"><span data-stu-id="34ff9-185">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="34ff9-186">Lors du prérendu de l’application :</span><span class="sxs-lookup"><span data-stu-id="34ff9-186">When the app prerenders:</span></span>

1. <span data-ttu-id="34ff9-187">Le composant s’exécute sur le serveur pour produire un balisage HTML statique dans la réponse HTTP.</span><span class="sxs-lookup"><span data-stu-id="34ff9-187">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="34ff9-188">Pendant cette phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ne sont pas appelés.</span><span class="sxs-lookup"><span data-stu-id="34ff9-188">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="34ff9-189">Quand `blazor.server.js` ou `blazor.webassembly.js` Démarrer dans le navigateur, le composant est redémarré en mode de rendu interactif.</span><span class="sxs-lookup"><span data-stu-id="34ff9-189">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="34ff9-190">Après le redémarrage d’un composant, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **sont** appelés parce que l’application n’est plus à l’intérieur de la phase de prérendu.</span><span class="sxs-lookup"><span data-stu-id="34ff9-190">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="34ff9-191">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="34ff9-191">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="34ff9-192">Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="34ff9-192">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="34ff9-193">Supprimer l’actualisation de l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="34ff9-193">Suppress UI refreshing</span></span>

<span data-ttu-id="34ff9-194">Substituez <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pour supprimer l’actualisation de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="34ff9-194">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="34ff9-195">Si l’implémentation retourne `true` , l’interface utilisateur est actualisée :</span><span class="sxs-lookup"><span data-stu-id="34ff9-195">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="34ff9-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> est appelé chaque fois que le composant est restitué.</span><span class="sxs-lookup"><span data-stu-id="34ff9-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="34ff9-197">Même si <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> est substitué, le composant est toujours restitué initialement.</span><span class="sxs-lookup"><span data-stu-id="34ff9-197">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="34ff9-198">Pour plus d’informations, consultez <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span><span class="sxs-lookup"><span data-stu-id="34ff9-198">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="34ff9-199">Modifications d'état</span><span class="sxs-lookup"><span data-stu-id="34ff9-199">State changes</span></span>

<span data-ttu-id="34ff9-200"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifie le composant que son état a changé.</span><span class="sxs-lookup"><span data-stu-id="34ff9-200"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="34ff9-201">Le cas échéant, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> l’appel de entraîne le rerendu du composant.</span><span class="sxs-lookup"><span data-stu-id="34ff9-201">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="34ff9-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> est appelé automatiquement pour les <xref:Microsoft.AspNetCore.Components.EventCallback> méthodes.</span><span class="sxs-lookup"><span data-stu-id="34ff9-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="34ff9-203">Pour plus d’informations, consultez <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="34ff9-203">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="34ff9-204">Pour plus d’informations, consultez <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="34ff9-204">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="34ff9-205">Gérer les actions asynchrones incomplètes au rendu</span><span class="sxs-lookup"><span data-stu-id="34ff9-205">Handle incomplete async actions at render</span></span>

<span data-ttu-id="34ff9-206">Les actions asynchrones exécutées dans des événements de cycle de vie peuvent ne pas être terminées avant le rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="34ff9-206">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="34ff9-207">Les objets peuvent être `null` ou être remplis de façon incomplète avec des données pendant l’exécution de la méthode Lifecycle.</span><span class="sxs-lookup"><span data-stu-id="34ff9-207">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="34ff9-208">Fournissez une logique de rendu pour confirmer que les objets sont initialisés.</span><span class="sxs-lookup"><span data-stu-id="34ff9-208">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="34ff9-209">Affichez les éléments d’interface utilisateur d’espace réservé (par exemple, un message de chargement) tandis que les objets sont `null` .</span><span class="sxs-lookup"><span data-stu-id="34ff9-209">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="34ff9-210">Dans le `FetchData` composant des Blazor modèles, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> est substitué pour recevoir des données de prévision de manière asynchrone ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="34ff9-210">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="34ff9-211">Lorsque `forecasts` a `null` la valeur, un message de chargement est affiché à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="34ff9-211">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="34ff9-212">Une fois la `Task` retournée <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> terminée, le composant est rerendu avec l’État mis à jour.</span><span class="sxs-lookup"><span data-stu-id="34ff9-212">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="34ff9-213">`Pages/FetchData.razor` dans le Blazor Server modèle :</span><span class="sxs-lookup"><span data-stu-id="34ff9-213">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="34ff9-214">Gérer les erreurs</span><span class="sxs-lookup"><span data-stu-id="34ff9-214">Handle errors</span></span>

<span data-ttu-id="34ff9-215">Pour plus d’informations sur la gestion des erreurs lors de l’exécution de la méthode de cycle de vie, consultez <xref:blazor/fundamentals/handle-errors#lifecycle-methods> .</span><span class="sxs-lookup"><span data-stu-id="34ff9-215">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="34ff9-216">Reconnexion avec état après le prérendu</span><span class="sxs-lookup"><span data-stu-id="34ff9-216">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="34ff9-217">Dans une Blazor Server application quand <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> est <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , le composant est initialement restitué de manière statique dans le cadre de la page.</span><span class="sxs-lookup"><span data-stu-id="34ff9-217">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="34ff9-218">Une fois que le navigateur a établi une connexion au serveur, le composant est *à nouveau* rendu et le composant est maintenant interactif.</span><span class="sxs-lookup"><span data-stu-id="34ff9-218">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="34ff9-219">Si la [`OnInitialized{Async}`](#component-initialization-methods) méthode de cycle de vie pour l’initialisation du composant est présente, la méthode est exécutée *deux fois*:</span><span class="sxs-lookup"><span data-stu-id="34ff9-219">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="34ff9-220">Lorsque le composant est prérendu statiquement.</span><span class="sxs-lookup"><span data-stu-id="34ff9-220">When the component is prerendered statically.</span></span>
* <span data-ttu-id="34ff9-221">Après l’établissement de la connexion au serveur.</span><span class="sxs-lookup"><span data-stu-id="34ff9-221">After the server connection has been established.</span></span>

<span data-ttu-id="34ff9-222">Cela peut entraîner une modification notable des données affichées dans l’interface utilisateur lorsque le composant est finalement restitué.</span><span class="sxs-lookup"><span data-stu-id="34ff9-222">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="34ff9-223">Pour éviter le double-rendu du scénario dans une Blazor Server application :</span><span class="sxs-lookup"><span data-stu-id="34ff9-223">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="34ff9-224">Transmettez un identificateur qui peut être utilisé pour mettre en cache l’État pendant le prérendu et pour récupérer l’état après le redémarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="34ff9-224">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="34ff9-225">Utilisez l’identificateur pendant le prérendu pour enregistrer l’état du composant.</span><span class="sxs-lookup"><span data-stu-id="34ff9-225">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="34ff9-226">Utilisez l’identificateur après le prérendu pour récupérer l’État mis en cache.</span><span class="sxs-lookup"><span data-stu-id="34ff9-226">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="34ff9-227">Le code suivant illustre une mise à jour `WeatherForecastService` dans une application basée sur un modèle Blazor Server qui évite le double rendu :</span><span class="sxs-lookup"><span data-stu-id="34ff9-227">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="34ff9-228">Pour plus d’informations sur le <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , consultez <xref:blazor/fundamentals/additional-scenarios#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="34ff9-228">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="34ff9-229">Détecter quand l’application est prérendu</span><span class="sxs-lookup"><span data-stu-id="34ff9-229">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="34ff9-230">Élimination des composants avec `IDisposable`</span><span class="sxs-lookup"><span data-stu-id="34ff9-230">Component disposal with `IDisposable`</span></span>

<span data-ttu-id="34ff9-231">Si un composant implémente <xref:System.IDisposable> , l’infrastructure appelle la [méthode de suppression](/dotnet/standard/garbage-collection/implementing-dispose) lorsque le composant est supprimé de l’interface utilisateur, où les ressources non managées peuvent être libérées.</span><span class="sxs-lookup"><span data-stu-id="34ff9-231">If a component implements <xref:System.IDisposable>, the framework calls the [disposal method](/dotnet/standard/garbage-collection/implementing-dispose) when the component is removed from the UI, where unmanaged resources can be released.</span></span> <span data-ttu-id="34ff9-232">La suppression peut avoir lieu à tout moment, y compris lors de [l’initialisation du composant](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="34ff9-232">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="34ff9-233">Le composant suivant implémente <xref:System.IDisposable> avec la [`@implements`](xref:mvc/views/razor#implements) Razor directive :</span><span class="sxs-lookup"><span data-stu-id="34ff9-233">The following component implements <xref:System.IDisposable> with the [`@implements`](xref:mvc/views/razor#implements) Razor directive:</span></span>

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

<span data-ttu-id="34ff9-234">Pour les tâches de suppression asynchrones, utilisez `DisposeAsync` au lieu de `Dispose` dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="34ff9-234">For asynchronous disposal tasks, use `DisposeAsync` instead of `Dispose` in the preceding example:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> <span data-ttu-id="34ff9-235"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>L’appel de dans `Dispose` n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="34ff9-235">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="34ff9-236"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> peut être appelé dans le cadre du détachement du convertisseur, donc demander des mises à jour de l’interface utilisateur à ce stade n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="34ff9-236"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="34ff9-237">Annule l’abonnement des gestionnaires d’événements des événements .NET.</span><span class="sxs-lookup"><span data-stu-id="34ff9-237">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="34ff9-238">Les exemples de [ Blazor formulaires](xref:blazor/forms-validation) suivants montrent comment décrocher un gestionnaire d’événements dans la `Dispose` méthode :</span><span class="sxs-lookup"><span data-stu-id="34ff9-238">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="34ff9-239">Approche de champ privé et lambda</span><span class="sxs-lookup"><span data-stu-id="34ff9-239">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="34ff9-240">Approche de la méthode privée</span><span class="sxs-lookup"><span data-stu-id="34ff9-240">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]
  
<span data-ttu-id="34ff9-241">Pour plus d’informations, consultez [nettoyage des ressources non managées](/dotnet/standard/garbage-collection/unmanaged) et les rubriques qui le suivent sur l’implémentation des `Dispose` `DisposeAsync` méthodes et.</span><span class="sxs-lookup"><span data-stu-id="34ff9-241">For more information, see [Cleaning up unmanaged resources](/dotnet/standard/garbage-collection/unmanaged) and the topics that follow it on implementing the `Dispose` and `DisposeAsync` methods.</span></span>

## <a name="cancelable-background-work"></a><span data-ttu-id="34ff9-242">Travail en arrière-plan annulable</span><span class="sxs-lookup"><span data-stu-id="34ff9-242">Cancelable background work</span></span>

<span data-ttu-id="34ff9-243">Les composants effectuent souvent des tâches en arrière-plan de longue durée, telles que la création d’appels réseau ( <xref:System.Net.Http.HttpClient> ) et l’interaction avec les bases de données.</span><span class="sxs-lookup"><span data-stu-id="34ff9-243">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="34ff9-244">Il est souhaitable d’arrêter le travail en arrière-plan pour économiser les ressources système dans plusieurs situations.</span><span class="sxs-lookup"><span data-stu-id="34ff9-244">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="34ff9-245">Par exemple, les opérations asynchrones en arrière-plan ne s’arrêtent pas automatiquement lorsqu’un utilisateur quitte un composant.</span><span class="sxs-lookup"><span data-stu-id="34ff9-245">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="34ff9-246">Les autres raisons pour lesquelles les éléments de travail en arrière-plan peuvent nécessiter l’annulation sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="34ff9-246">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="34ff9-247">Une tâche en arrière-plan en cours d’exécution a été démarrée avec des données d’entrée ou des paramètres de traitement défectueux.</span><span class="sxs-lookup"><span data-stu-id="34ff9-247">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="34ff9-248">Le jeu actuel d’éléments de travail en arrière-plan doit être remplacé par un nouvel ensemble d’éléments de travail.</span><span class="sxs-lookup"><span data-stu-id="34ff9-248">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="34ff9-249">La priorité des tâches en cours d’exécution doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="34ff9-249">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="34ff9-250">L’application doit être arrêtée pour pouvoir être redéployée sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="34ff9-250">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="34ff9-251">Les ressources du serveur sont limitées, ce qui nécessite la replanification des éléments de travail en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="34ff9-251">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="34ff9-252">Pour implémenter un modèle de travail d’arrière-plan annulable dans un composant :</span><span class="sxs-lookup"><span data-stu-id="34ff9-252">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="34ff9-253">Utilisez un <xref:System.Threading.CancellationTokenSource> et un <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="34ff9-253">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="34ff9-254">Lors de [la suppression du composant](#component-disposal-with-idisposable) et à tout moment, l’annulation est souhaitée en annulant manuellement le jeton, puis appelez [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) pour signaler que le travail en arrière-plan doit être annulé.</span><span class="sxs-lookup"><span data-stu-id="34ff9-254">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="34ff9-255">Une fois l’appel asynchrone retourné, appelez <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> sur le jeton.</span><span class="sxs-lookup"><span data-stu-id="34ff9-255">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="34ff9-256">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="34ff9-256">In the following example:</span></span>

* <span data-ttu-id="34ff9-257">`await Task.Delay(5000, cts.Token);` représente le travail en arrière-plan asynchrone de longue durée.</span><span class="sxs-lookup"><span data-stu-id="34ff9-257">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="34ff9-258">`BackgroundResourceMethod` représente une méthode d’arrière-plan de longue durée qui ne doit pas démarrer si le `Resource` est supprimé avant l’appel de la méthode.</span><span class="sxs-lookup"><span data-stu-id="34ff9-258">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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

## <a name="no-locblazor-server-reconnection-events"></a><span data-ttu-id="34ff9-259">Blazor Server événements de reconnexion</span><span class="sxs-lookup"><span data-stu-id="34ff9-259">Blazor Server reconnection events</span></span>

<span data-ttu-id="34ff9-260">Les événements de cycle de vie des composants traités dans cet article fonctionnent séparément des [ Blazor Server gestionnaires d’événements de reconnexion de](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span><span class="sxs-lookup"><span data-stu-id="34ff9-260">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="34ff9-261">Quand une Blazor Server application perd sa SignalR connexion au client, seules les mises à jour de l’interface utilisateur sont interrompues.</span><span class="sxs-lookup"><span data-stu-id="34ff9-261">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="34ff9-262">Les mises à jour de l’interface utilisateur sont reprises lorsque la connexion est rétablie.</span><span class="sxs-lookup"><span data-stu-id="34ff9-262">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="34ff9-263">Pour plus d’informations sur la configuration et les événements du gestionnaire de circuit, consultez <xref:blazor/fundamentals/additional-scenarios> .</span><span class="sxs-lookup"><span data-stu-id="34ff9-263">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/additional-scenarios>.</span></span>
