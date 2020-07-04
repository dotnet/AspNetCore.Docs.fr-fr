---
title: BlazorCycle de vie ASP.net Core
author: guardrex
description: Découvrez comment utiliser les Razor méthodes de cycle de vie des composants dans les Blazor applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/lifecycle
ms.openlocfilehash: 312a265dd251eadf876b4252e3d9f9858adcde1b
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400984"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="f8388-103">BlazorCycle de vie ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f8388-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="f8388-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f8388-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="f8388-105">L' Blazor infrastructure comprend des méthodes de cycle de vie synchrones et asynchrones.</span><span class="sxs-lookup"><span data-stu-id="f8388-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="f8388-106">Substituez les méthodes de cycle de vie pour effectuer des opérations supplémentaires sur les composants lors de l’initialisation et du rendu des composants.</span><span class="sxs-lookup"><span data-stu-id="f8388-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="f8388-107">Méthodes de cycle de vie</span><span class="sxs-lookup"><span data-stu-id="f8388-107">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="f8388-108">Les paramètres Before sont définis</span><span class="sxs-lookup"><span data-stu-id="f8388-108">Before parameters are set</span></span>

<span data-ttu-id="f8388-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>définit les paramètres fournis par le parent du composant dans l’arborescence de rendu :</span><span class="sxs-lookup"><span data-stu-id="f8388-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="f8388-110"><xref:Microsoft.AspNetCore.Components.ParameterView>contient l’ensemble des valeurs de paramètre à chaque fois que <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> est appelé.</span><span class="sxs-lookup"><span data-stu-id="f8388-110"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="f8388-111">L’implémentation par défaut de <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> définit la valeur de chaque propriété avec l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribut ou ayant [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) une valeur correspondante dans le <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="f8388-111">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="f8388-112">Les paramètres qui n’ont pas de valeur correspondante dans <xref:Microsoft.AspNetCore.Components.ParameterView> ne sont pas modifiés.</span><span class="sxs-lookup"><span data-stu-id="f8388-112">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="f8388-113">Si [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) n’est pas appelé, le code personnalisé peut interpréter la valeur des paramètres entrants de la façon requise.</span><span class="sxs-lookup"><span data-stu-id="f8388-113">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="f8388-114">Par exemple, il n’est pas nécessaire d’assigner les paramètres entrants aux propriétés de la classe.</span><span class="sxs-lookup"><span data-stu-id="f8388-114">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="f8388-115">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="f8388-115">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="f8388-116">Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="f8388-116">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="f8388-117">Méthodes d’initialisation de composant</span><span class="sxs-lookup"><span data-stu-id="f8388-117">Component initialization methods</span></span>

<span data-ttu-id="f8388-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> sont appelés lorsque le composant est initialisé après avoir reçu ses paramètres initiaux de son composant parent dans <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="f8388-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="f8388-119">Utilisez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> lorsque le composant exécute une opération asynchrone et doit être actualisé lorsque l’opération est terminée.</span><span class="sxs-lookup"><span data-stu-id="f8388-119">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="f8388-120">Pour une opération synchrone, remplacez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="f8388-120">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="f8388-121">Pour effectuer une opération asynchrone, substituez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> et utilisez l' [`await`](/dotnet/csharp/language-reference/operators/await) opérateur sur l’opération :</span><span class="sxs-lookup"><span data-stu-id="f8388-121">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor Server<span data-ttu-id="f8388-122">applications qui [préaffichent le contenu](xref:blazor/fundamentals/additional-scenarios#render-mode) de l’appel à <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_deux reprises_**:</span><span class="sxs-lookup"><span data-stu-id="f8388-122"> apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="f8388-123">Une fois lorsque le composant est initialement restitué de manière statique dans le cadre de la page.</span><span class="sxs-lookup"><span data-stu-id="f8388-123">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="f8388-124">Une deuxième fois lorsque le navigateur établit une connexion au serveur.</span><span class="sxs-lookup"><span data-stu-id="f8388-124">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="f8388-125">Pour empêcher <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> l’exécution à deux reprises du code du développeur, consultez la section [reconnexion avec état après le prérendu](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="f8388-125">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="f8388-126">Pendant Blazor Server le prérendu d’une application, certaines actions, telles que l’appel de JavaScript, ne sont pas possibles, car une connexion avec le navigateur n’a pas été établie.</span><span class="sxs-lookup"><span data-stu-id="f8388-126">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="f8388-127">Les composants peuvent avoir besoin d’être restitués différemment lorsqu’ils sont prérendus.</span><span class="sxs-lookup"><span data-stu-id="f8388-127">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="f8388-128">Pour plus d’informations, consultez la section [détecter quand l’application est un prérendu](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="f8388-128">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="f8388-129">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="f8388-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="f8388-130">Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="f8388-130">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="f8388-131">Une fois les paramètres définis</span><span class="sxs-lookup"><span data-stu-id="f8388-131">After parameters are set</span></span>

<span data-ttu-id="f8388-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>ou <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> sont appelés :</span><span class="sxs-lookup"><span data-stu-id="f8388-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="f8388-133">Une fois le composant initialisé dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ou <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> .</span><span class="sxs-lookup"><span data-stu-id="f8388-133">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>.</span></span>
* <span data-ttu-id="f8388-134">Lorsque le composant parent est à nouveau rendu et fournit :</span><span class="sxs-lookup"><span data-stu-id="f8388-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="f8388-135">Seuls les types immuables primitifs connus dont au moins un paramètre a été modifié.</span><span class="sxs-lookup"><span data-stu-id="f8388-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="f8388-136">Tous les paramètres de type complexe.</span><span class="sxs-lookup"><span data-stu-id="f8388-136">Any complex-typed parameters.</span></span> <span data-ttu-id="f8388-137">L’infrastructure ne peut pas savoir si les valeurs d’un paramètre de type complexe ont muté en interne, donc elle traite le jeu de paramètres comme étant modifié.</span><span class="sxs-lookup"><span data-stu-id="f8388-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="f8388-138">Un travail asynchrone lors de l’application de paramètres et de valeurs de propriété doit se produire pendant l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> événement de cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="f8388-138">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="f8388-139">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="f8388-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="f8388-140">Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="f8388-140">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="f8388-141">Après le rendu du composant</span><span class="sxs-lookup"><span data-stu-id="f8388-141">After component render</span></span>

<span data-ttu-id="f8388-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sont appelées après la fin d’un rendu d’un composant.</span><span class="sxs-lookup"><span data-stu-id="f8388-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="f8388-143">Les références d’élément et de composant sont remplies à ce stade.</span><span class="sxs-lookup"><span data-stu-id="f8388-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="f8388-144">Utilisez cette étape pour effectuer des étapes d’initialisation supplémentaires à l’aide du contenu rendu, par exemple l’activation de bibliothèques JavaScript tierces qui opèrent sur les éléments DOM rendus.</span><span class="sxs-lookup"><span data-stu-id="f8388-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="f8388-145">Le `firstRender` paramètre pour <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="f8388-145">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="f8388-146">A la valeur `true` la première fois que l’instance de composant est restituée.</span><span class="sxs-lookup"><span data-stu-id="f8388-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="f8388-147">Peut être utilisé pour s’assurer que le travail d’initialisation n’est effectué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="f8388-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="f8388-148">Le travail asynchrone immédiatement après le rendu doit se produire pendant l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> événement de cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="f8388-148">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="f8388-149">Même si vous retournez un <xref:System.Threading.Tasks.Task> à partir de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , l’infrastructure ne planifie pas un cycle de rendu supplémentaire pour votre composant une fois cette tâche terminée.</span><span class="sxs-lookup"><span data-stu-id="f8388-149">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="f8388-150">Cela permet d’éviter une boucle de rendu infinie.</span><span class="sxs-lookup"><span data-stu-id="f8388-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="f8388-151">Elle est différente des autres méthodes de cycle de vie, qui planifient un cycle de rendu supplémentaire une fois que la tâche retournée est terminée.</span><span class="sxs-lookup"><span data-stu-id="f8388-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="f8388-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *ne sont pas appelés lors du prérendu sur le serveur.*</span><span class="sxs-lookup"><span data-stu-id="f8388-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="f8388-153">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="f8388-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="f8388-154">Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="f8388-154">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="f8388-155">Supprimer l’actualisation de l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="f8388-155">Suppress UI refreshing</span></span>

<span data-ttu-id="f8388-156">Substituez <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pour supprimer l’actualisation de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f8388-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="f8388-157">Si l’implémentation retourne `true` , l’interface utilisateur est actualisée :</span><span class="sxs-lookup"><span data-stu-id="f8388-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="f8388-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>est appelé chaque fois que le composant est restitué.</span><span class="sxs-lookup"><span data-stu-id="f8388-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="f8388-159">Même si <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> est substitué, le composant est toujours restitué initialement.</span><span class="sxs-lookup"><span data-stu-id="f8388-159">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="f8388-160">Pour plus d’informations, consultez <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="f8388-160">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="f8388-161">Changements d'état</span><span class="sxs-lookup"><span data-stu-id="f8388-161">State changes</span></span>

<span data-ttu-id="f8388-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>notifie le composant que son état a changé.</span><span class="sxs-lookup"><span data-stu-id="f8388-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="f8388-163">Le cas échéant, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> l’appel de entraîne le rerendu du composant.</span><span class="sxs-lookup"><span data-stu-id="f8388-163">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="f8388-164">Gérer les actions asynchrones incomplètes au rendu</span><span class="sxs-lookup"><span data-stu-id="f8388-164">Handle incomplete async actions at render</span></span>

<span data-ttu-id="f8388-165">Les actions asynchrones exécutées dans des événements de cycle de vie peuvent ne pas être terminées avant le rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="f8388-165">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="f8388-166">Les objets peuvent être `null` ou être remplis de façon incomplète avec des données pendant l’exécution de la méthode Lifecycle.</span><span class="sxs-lookup"><span data-stu-id="f8388-166">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="f8388-167">Fournissez une logique de rendu pour confirmer que les objets sont initialisés.</span><span class="sxs-lookup"><span data-stu-id="f8388-167">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="f8388-168">Affichez les éléments d’interface utilisateur d’espace réservé (par exemple, un message de chargement) tandis que les objets sont `null` .</span><span class="sxs-lookup"><span data-stu-id="f8388-168">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="f8388-169">Dans le `FetchData` composant des Blazor modèles, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> est remplacé par asynchrone recevoir les données de prévision ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="f8388-169">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="f8388-170">Lorsque `forecasts` a `null` la valeur, un message de chargement est affiché à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f8388-170">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="f8388-171">Une fois la `Task` retournée <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> terminée, le composant est rerendu avec l’État mis à jour.</span><span class="sxs-lookup"><span data-stu-id="f8388-171">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="f8388-172">`Pages/FetchData.razor`dans le Blazor Server modèle :</span><span class="sxs-lookup"><span data-stu-id="f8388-172">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="f8388-173">Suppression de composant avec IDisposable</span><span class="sxs-lookup"><span data-stu-id="f8388-173">Component disposal with IDisposable</span></span>

<span data-ttu-id="f8388-174">Si un composant implémente <xref:System.IDisposable> , la [ `Dispose` méthode](/dotnet/standard/garbage-collection/implementing-dispose) est appelée lorsque le composant est supprimé de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f8388-174">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="f8388-175">Le composant suivant utilise `@implements IDisposable` et la `Dispose` méthode :</span><span class="sxs-lookup"><span data-stu-id="f8388-175">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

> [!NOTE]
> <span data-ttu-id="f8388-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>L’appel de dans `Dispose` n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="f8388-176">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="f8388-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>peut être appelé dans le cadre du détachement du convertisseur, donc demander des mises à jour de l’interface utilisateur à ce stade n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="f8388-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="f8388-178">Annule l’abonnement des gestionnaires d’événements des événements .NET.</span><span class="sxs-lookup"><span data-stu-id="f8388-178">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="f8388-179">Les exemples de [ Blazor formulaires](xref:blazor/forms-validation) suivants montrent comment décrocher un gestionnaire d’événements dans la `Dispose` méthode :</span><span class="sxs-lookup"><span data-stu-id="f8388-179">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="f8388-180">Approche de champ privé et lambda</span><span class="sxs-lookup"><span data-stu-id="f8388-180">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="f8388-181">Approche de la méthode privée</span><span class="sxs-lookup"><span data-stu-id="f8388-181">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="f8388-182">Gérer les erreurs</span><span class="sxs-lookup"><span data-stu-id="f8388-182">Handle errors</span></span>

<span data-ttu-id="f8388-183">Pour plus d’informations sur la gestion des erreurs lors de l’exécution de la méthode de cycle de vie, consultez <xref:blazor/fundamentals/handle-errors#lifecycle-methods> .</span><span class="sxs-lookup"><span data-stu-id="f8388-183">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="f8388-184">Reconnexion avec état après le prérendu</span><span class="sxs-lookup"><span data-stu-id="f8388-184">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="f8388-185">Dans une Blazor Server application quand <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> est <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , le composant est initialement restitué de manière statique dans le cadre de la page.</span><span class="sxs-lookup"><span data-stu-id="f8388-185">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="f8388-186">Une fois que le navigateur a établi une connexion au serveur, le composant est *à nouveau*rendu et le composant est maintenant interactif.</span><span class="sxs-lookup"><span data-stu-id="f8388-186">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="f8388-187">Si la [`OnInitialized{Async}`](#component-initialization-methods) méthode de cycle de vie pour l’initialisation du composant est présente, la méthode est exécutée *deux fois*:</span><span class="sxs-lookup"><span data-stu-id="f8388-187">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="f8388-188">Lorsque le composant est prérendu statiquement.</span><span class="sxs-lookup"><span data-stu-id="f8388-188">When the component is prerendered statically.</span></span>
* <span data-ttu-id="f8388-189">Après l’établissement de la connexion au serveur.</span><span class="sxs-lookup"><span data-stu-id="f8388-189">After the server connection has been established.</span></span>

<span data-ttu-id="f8388-190">Cela peut entraîner une modification notable des données affichées dans l’interface utilisateur lorsque le composant est finalement restitué.</span><span class="sxs-lookup"><span data-stu-id="f8388-190">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="f8388-191">Pour éviter le double-rendu du scénario dans une Blazor Server application :</span><span class="sxs-lookup"><span data-stu-id="f8388-191">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="f8388-192">Transmettez un identificateur qui peut être utilisé pour mettre en cache l’État pendant le prérendu et pour récupérer l’état après le redémarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="f8388-192">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="f8388-193">Utilisez l’identificateur pendant le prérendu pour enregistrer l’état du composant.</span><span class="sxs-lookup"><span data-stu-id="f8388-193">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="f8388-194">Utilisez l’identificateur après le prérendu pour récupérer l’État mis en cache.</span><span class="sxs-lookup"><span data-stu-id="f8388-194">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="f8388-195">Le code suivant illustre une mise à jour `WeatherForecastService` dans une application basée sur un modèle Blazor Server qui évite le double rendu :</span><span class="sxs-lookup"><span data-stu-id="f8388-195">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="f8388-196">Pour plus d’informations sur le <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , consultez <xref:blazor/fundamentals/additional-scenarios#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="f8388-196">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="f8388-197">Détecter quand l’application est prérendu</span><span class="sxs-lookup"><span data-stu-id="f8388-197">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="f8388-198">Travail en arrière-plan annulable</span><span class="sxs-lookup"><span data-stu-id="f8388-198">Cancelable background work</span></span>

<span data-ttu-id="f8388-199">Les composants effectuent souvent des tâches en arrière-plan de longue durée, telles que la création d’appels réseau ( <xref:System.Net.Http.HttpClient> ) et l’interaction avec les bases de données.</span><span class="sxs-lookup"><span data-stu-id="f8388-199">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="f8388-200">Il est souhaitable d’arrêter le travail en arrière-plan pour économiser les ressources système dans plusieurs situations.</span><span class="sxs-lookup"><span data-stu-id="f8388-200">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="f8388-201">Par exemple, les opérations asynchrones en arrière-plan ne s’arrêtent pas automatiquement lorsqu’un utilisateur quitte un composant.</span><span class="sxs-lookup"><span data-stu-id="f8388-201">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="f8388-202">Les autres raisons pour lesquelles les éléments de travail en arrière-plan peuvent nécessiter l’annulation sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="f8388-202">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="f8388-203">Une tâche en arrière-plan en cours d’exécution a été démarrée avec des données d’entrée ou des paramètres de traitement défectueux.</span><span class="sxs-lookup"><span data-stu-id="f8388-203">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="f8388-204">Le jeu actuel d’éléments de travail en arrière-plan doit être remplacé par un nouvel ensemble d’éléments de travail.</span><span class="sxs-lookup"><span data-stu-id="f8388-204">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="f8388-205">La priorité des tâches en cours d’exécution doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="f8388-205">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="f8388-206">L’application doit être arrêtée pour pouvoir être redéployée sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="f8388-206">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="f8388-207">Les ressources du serveur sont limitées, ce qui nécessite la replanification des éléments de travail en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="f8388-207">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="f8388-208">Pour implémenter un modèle de travail d’arrière-plan annulable dans un composant :</span><span class="sxs-lookup"><span data-stu-id="f8388-208">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="f8388-209">Utilisez un <xref:System.Threading.CancellationTokenSource> et un <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="f8388-209">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="f8388-210">Lors de [la suppression du composant](#component-disposal-with-idisposable) et à tout moment, l’annulation est souhaitée en annulant manuellement le jeton, puis appelez [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) pour signaler que le travail en arrière-plan doit être annulé.</span><span class="sxs-lookup"><span data-stu-id="f8388-210">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="f8388-211">Une fois l’appel asynchrone retourné, appelez <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> sur le jeton.</span><span class="sxs-lookup"><span data-stu-id="f8388-211">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="f8388-212">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="f8388-212">In the following example:</span></span>

* <span data-ttu-id="f8388-213">`await Task.Delay(5000, cts.Token);`représente le travail en arrière-plan asynchrone de longue durée.</span><span class="sxs-lookup"><span data-stu-id="f8388-213">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="f8388-214">`BackgroundResourceMethod`représente une méthode d’arrière-plan de longue durée qui ne doit pas démarrer si le `Resource` est supprimé avant l’appel de la méthode.</span><span class="sxs-lookup"><span data-stu-id="f8388-214">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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