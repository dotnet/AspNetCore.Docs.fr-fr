---
title: Rendu des composants de ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur le Razor rendu des composants dans les Blazor applications ASP.net Core, notamment sur l’appel de StateHasChanged.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2021
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
uid: blazor/components/rendering
ms.openlocfilehash: 27701d175c86cdf4b74a9e6332b30b4d55806650
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107036"
---
# <a name="aspnet-core-blazor-component-rendering"></a><span data-ttu-id="6c8d1-103">Rendu des composants de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6c8d1-103">ASP.NET Core Blazor component rendering</span></span>

<span data-ttu-id="6c8d1-104">Par [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="6c8d1-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="6c8d1-105">Les composants *doivent* être rendus lorsqu’ils sont ajoutés pour la première fois à la hiérarchie des composants par leur composant parent.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-105">Components *must* render when they're first added to the component hierarchy by their parent component.</span></span> <span data-ttu-id="6c8d1-106">Il s’agit de la seule fois où un composant doit absolument être rendu.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-106">This is the only time that a component strictly must render.</span></span>

<span data-ttu-id="6c8d1-107">Les composants peuvent choisir d' *être* rendus à toute autre heure en fonction de leur propre logique et de leurs conventions.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-107">Components *may* choose to render at any other time according to their own logic and conventions.</span></span>

## <a name="conventions-for-componentbase"></a><span data-ttu-id="6c8d1-108">Conventions pour `ComponentBase`</span><span class="sxs-lookup"><span data-stu-id="6c8d1-108">Conventions for `ComponentBase`</span></span>

<span data-ttu-id="6c8d1-109">Par défaut, Razor les composants ( `.razor` ) héritent de la <xref:Microsoft.AspNetCore.Components.ComponentBase> classe de base, qui contient une logique pour déclencher le rerendu aux moments suivants :</span><span class="sxs-lookup"><span data-stu-id="6c8d1-109">By default, Razor components (`.razor`) inherit from the <xref:Microsoft.AspNetCore.Components.ComponentBase> base class, which contains logic to trigger rerendering at the following times:</span></span>

* <span data-ttu-id="6c8d1-110">Après l’application d’un jeu de paramètres mis à jour à partir d’un composant parent.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-110">After applying an updated set of parameters from a parent component.</span></span>
* <span data-ttu-id="6c8d1-111">Après l’application d’une valeur mise à jour pour un paramètre en cascade.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-111">After applying an updated value for a cascading parameter.</span></span>
* <span data-ttu-id="6c8d1-112">Après la notification d’un événement et l’appel de l’un de ses propres gestionnaires d’événements.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-112">After notification of an event and invoking one of its own event handlers.</span></span>
* <span data-ttu-id="6c8d1-113">Après un appel à sa propre <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> méthode.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-113">After a call to its own <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> method.</span></span>

<span data-ttu-id="6c8d1-114">Les composants hérités de <xref:Microsoft.AspNetCore.Components.ComponentBase> ignorent les réaffichages en raison de mises à jour de paramètres si l’une des conditions suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="6c8d1-114">Components inherited from <xref:Microsoft.AspNetCore.Components.ComponentBase> skip rerenders due to parameter updates if either of the following are true:</span></span>

* <span data-ttu-id="6c8d1-115">Toutes les valeurs de paramètre sont des types primitifs immuables connus (par exemple,, `int` `string` , `DateTime` ) et n’ont pas changé depuis la définition du jeu de paramètres précédent.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-115">All of the parameter values are of known immutable primitive types (for example, `int`, `string`, `DateTime`) and haven't changed since the previous set of parameters were set.</span></span>
* <span data-ttu-id="6c8d1-116">La méthode du composant <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> retourne `false` .</span><span class="sxs-lookup"><span data-stu-id="6c8d1-116">The component's <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> method returns `false`.</span></span>

<span data-ttu-id="6c8d1-117">Pour plus d'informations sur le <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, consultez <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-117">For more information on <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span></span>

## <a name="control-the-rendering-flow"></a><span data-ttu-id="6c8d1-118">Contrôler le workflow de rendu</span><span class="sxs-lookup"><span data-stu-id="6c8d1-118">Control the rendering flow</span></span>

<span data-ttu-id="6c8d1-119">Dans la plupart des cas, <xref:Microsoft.AspNetCore.Components.ComponentBase> les conventions génèrent un sous-ensemble correct de réaffichages de composants après l’exécution d’un événement.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-119">In most cases, <xref:Microsoft.AspNetCore.Components.ComponentBase> conventions result in the correct subset of component rerenders after an event occurs.</span></span> <span data-ttu-id="6c8d1-120">Les développeurs ne sont généralement pas tenus de fournir une logique manuelle pour indiquer à l’infrastructure les composants à rerestituer et à quel moment les régénérer.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-120">Developers aren't usually required to provide manual logic to tell the framework which components to rerender and when to rerender them.</span></span> <span data-ttu-id="6c8d1-121">L’effet global des conventions de l’infrastructure est que le composant recevant un événement se reproduit lui-même, ce qui déclenche de manière récursive le rerendu des composants descendants dont les valeurs de paramètres peuvent avoir changé.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-121">The overall effect of the framework's conventions is that the component receiving an event rerenders itself, which recursively triggers rerendering of descendant components whose parameter values may have changed.</span></span>

<span data-ttu-id="6c8d1-122">Pour plus d’informations sur les implications en termes de performances des conventions de l’infrastructure et sur la façon d’optimiser la hiérarchie des composants d’une application, consultez <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed> .</span><span class="sxs-lookup"><span data-stu-id="6c8d1-122">For more information on the performance implications of the framework's conventions and how to optimize an app's component hierarchy, see <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span></span>

## <a name="when-to-call-statehaschanged"></a><span data-ttu-id="6c8d1-123">Quand appeler `StateHasChanged`</span><span class="sxs-lookup"><span data-stu-id="6c8d1-123">When to call `StateHasChanged`</span></span>

<span data-ttu-id="6c8d1-124">La <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> méthode vous permet de déclencher un rendu à tout moment.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-124">The <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> method allows you to trigger a render at any time.</span></span> <span data-ttu-id="6c8d1-125">Toutefois, veillez à ne pas appeler <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> inutilement, ce qui est une erreur courante, car elle impose des coûts de rendu inutiles.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-125">However, be careful not to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> unnecessarily, which is a common mistake, because it imposes unnecessary rendering costs.</span></span>

<span data-ttu-id="6c8d1-126">Vous *n’avez pas* besoin d’appeler <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> lorsque :</span><span class="sxs-lookup"><span data-stu-id="6c8d1-126">You should *not* need to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when:</span></span>

* <span data-ttu-id="6c8d1-127">La gestion régulière des événements, qu’il s’agisse de manière synchrone ou asynchrone, depuis, <xref:Microsoft.AspNetCore.Components.ComponentBase> déclenche un rendu pour la plupart des gestionnaires d’événements de routine.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-127">Routinely handling events, whether synchronously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for most routine event handlers.</span></span>
* <span data-ttu-id="6c8d1-128">L’implémentation d’une logique de cycle de vie typique, telle que [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) ou [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) , qu’il s’agisse de synchonrously ou asynchrone, depuis <xref:Microsoft.AspNetCore.Components.ComponentBase> déclenche un rendu pour les événements de cycle de vie typiques.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-128">Implementing typical lifecycle logic, such as [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) or [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), whether synchonrously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for typical lifecycle events.</span></span>

<span data-ttu-id="6c8d1-129">Toutefois, il peut être judicieux d’appeler <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> dans les cas décrits dans les sections suivantes :</span><span class="sxs-lookup"><span data-stu-id="6c8d1-129">However, it might make sense to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in the cases described by the following sections:</span></span>

* [<span data-ttu-id="6c8d1-130">Un gestionnaire asynchrone implique plusieurs phases asynchrones</span><span class="sxs-lookup"><span data-stu-id="6c8d1-130">An asynchronous handler involves multiple asynchronous phases</span></span>](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [<span data-ttu-id="6c8d1-131">Réception d’un appel d’un événement externe au Blazor système de gestion des événements et du rendu</span><span class="sxs-lookup"><span data-stu-id="6c8d1-131">Receiving a call from something external to the Blazor rendering and event handling system</span></span>](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [<span data-ttu-id="6c8d1-132">Pour afficher un composant en dehors de la sous-arborescence qui est rerendue par un événement particulier</span><span class="sxs-lookup"><span data-stu-id="6c8d1-132">To render component outside the subtree that is rerendered by a particular event</span></span>](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a><span data-ttu-id="6c8d1-133">Un gestionnaire asynchrone implique plusieurs phases asynchrones</span><span class="sxs-lookup"><span data-stu-id="6c8d1-133">An asynchronous handler involves multiple asynchronous phases</span></span>

<span data-ttu-id="6c8d1-134">Prenons le `Counter` composant suivant, qui met à jour le nombre quatre fois à chaque clic.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-134">Consider the following `Counter` component, which updates the count four times on each click.</span></span>

<span data-ttu-id="6c8d1-135">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="6c8d1-135">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private async Task IncrementCount()
    {
        currentCount++;
        // Renders here automatically

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        // Renders here automatically
    }
}
```

<span data-ttu-id="6c8d1-136">En raison de la façon dont les tâches sont définies dans .NET, un destinataire d’un <xref:System.Threading.Tasks.Task> peut uniquement observer son achèvement final, et non les États asynchrones intermédiaires.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-136">Due to the way that tasks are defined in .NET, a receiver of a <xref:System.Threading.Tasks.Task> can only observe its final completion, not intermediate asynchronous states.</span></span> <span data-ttu-id="6c8d1-137">Par conséquent, <xref:Microsoft.AspNetCore.Components.ComponentBase> peut déclencher le rerendu uniquement lorsque <xref:System.Threading.Tasks.Task> est retourné pour la première fois et lorsque <xref:System.Threading.Tasks.Task> finally se termine.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-137">Therefore, <xref:Microsoft.AspNetCore.Components.ComponentBase> can only trigger rerendering when the <xref:System.Threading.Tasks.Task> is first returned and when the <xref:System.Threading.Tasks.Task> finally completes.</span></span> <span data-ttu-id="6c8d1-138">Il ne peut pas savoir s’afficher à un autre point intermédiaire.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-138">It can't know to rerender at other intermediate points.</span></span> <span data-ttu-id="6c8d1-139">Si vous souhaitez effectuer un rerendu à des points intermédiaires, utilisez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .</span><span class="sxs-lookup"><span data-stu-id="6c8d1-139">If you want to rerender at intermediate points, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a><span data-ttu-id="6c8d1-140">Réception d’un appel d’un événement externe au Blazor système de gestion des événements et du rendu</span><span class="sxs-lookup"><span data-stu-id="6c8d1-140">Receiving a call from something external to the Blazor rendering and event handling system</span></span>

<span data-ttu-id="6c8d1-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> connaît uniquement ses propres méthodes de cycle de vie et les Blazor événements déclenchés.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> only knows about its own lifecycle methods and Blazor-triggered events.</span></span> <span data-ttu-id="6c8d1-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> ne connaît pas les autres événements qui peuvent se produire dans votre code.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> doesn't know about other events that may occur in your code.</span></span> <span data-ttu-id="6c8d1-143">Par exemple, les événements C# déclenchés par un magasin de données personnalisé sont inconnus de Blazor .</span><span class="sxs-lookup"><span data-stu-id="6c8d1-143">For example, any C# events raised by a custom data store are unknown to Blazor.</span></span> <span data-ttu-id="6c8d1-144">Pour que ces événements déclenchent le rerendu pour afficher les valeurs mises à jour dans l’interface utilisateur, utilisez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .</span><span class="sxs-lookup"><span data-stu-id="6c8d1-144">In order for such events to trigger rerendering to display updated values in the UI, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

<span data-ttu-id="6c8d1-145">Dans un autre cas d’utilisation, considérez le `Counter` composant suivant qui utilise <xref:System.Timers.Timer?displayProperty=fullName> pour mettre à jour le nombre à intervalles réguliers et appelle <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> pour mettre à jour l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-145">In another use case, consider the following `Counter` component that uses <xref:System.Timers.Timer?displayProperty=fullName> to update the count at a regular interval and calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to update the UI.</span></span>

<span data-ttu-id="6c8d1-146">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="6c8d1-146">`Pages/CounterWithTimerDisposal.razor`:</span></span>

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

<span data-ttu-id="6c8d1-147">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="6c8d1-147">In the preceding example:</span></span>

* <span data-ttu-id="6c8d1-148">`OnTimerCallback` doit appeler <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> car Blazor n’est pas conscient des modifications apportées à `currentCount` dans le rappel.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-148">`OnTimerCallback` must call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> because Blazor isn't aware of the changes to `currentCount` in the callback.</span></span> <span data-ttu-id="6c8d1-149">`OnTimerCallback` s’exécute en dehors de tout Blazor Workflow de rendu managé ou notification d’événement.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-149">`OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification.</span></span>
* <span data-ttu-id="6c8d1-150">Le composant implémente <xref:System.IDisposable> , où <xref:System.Timers.Timer> est supprimé lorsque l’infrastructure appelle la `Dispose` méthode.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-150">The component implements <xref:System.IDisposable>, where the <xref:System.Timers.Timer> is disposed when the framework calls the `Dispose` method.</span></span> <span data-ttu-id="6c8d1-151">Pour plus d’informations, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-151">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<span data-ttu-id="6c8d1-152">De même, étant donné que le rappel est appelé en dehors du Blazor contexte de synchronisation de, il est nécessaire d’encapsuler la logique dans <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> pour la déplacer vers le contexte de synchronisation du convertisseur.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-152">Similarly, because the callback is invoked outside Blazor's synchronization context, it's necessary to wrap the logic in <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> to move it onto the renderer's synchronization context.</span></span> <span data-ttu-id="6c8d1-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> peut uniquement être appelé à partir du contexte de synchronisation du convertisseur et lève une exception dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> can only be called from the renderer's synchronization context and throws an exception otherwise.</span></span> <span data-ttu-id="6c8d1-154">Cela équivaut au marshaling du thread d’interface utilisateur dans d’autres infrastructures d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-154">This is equivalent to marshalling to the UI thread in other UI frameworks.</span></span>

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a><span data-ttu-id="6c8d1-155">Pour restituer un composant à l’extérieur de la sous-arborescence qui est rerendue par un événement particulier</span><span class="sxs-lookup"><span data-stu-id="6c8d1-155">To render a component outside the subtree that's rerendered by a particular event</span></span>

<span data-ttu-id="6c8d1-156">Votre interface utilisateur peut impliquer la distribution d’un événement à un composant, la modification d’un État et la nécessité de rerestituer un composant complètement différent qui n’est pas un descendant de celui recevant l’événement.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-156">Your UI might involve dispatching an event to one component, changing some state, and needing to rerender a completely different component that isn't a descendant of the one receiving the event.</span></span>

<span data-ttu-id="6c8d1-157">L’une des façons de traiter ce scénario est d’avoir une classe de *gestion d’État* , peut-être comme un service di, injectée dans plusieurs composants.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-157">One way to deal with this scenario is to have a *state management* class, perhaps as a DI service, injected into multiple components.</span></span> <span data-ttu-id="6c8d1-158">Quand un composant appelle une méthode sur le gestionnaire d’État, le gestionnaire d’État peut déclencher un événement C# qui est ensuite reçu par un composant indépendant.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-158">When one component calls a method on the state manager, the state manager can raise a C# event that's then received by an independent component.</span></span>

<span data-ttu-id="6c8d1-159">Étant donné que ces événements C# sont en dehors du Blazor pipeline de rendu, appelez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> sur d’autres composants que vous souhaitez afficher en réponse aux événements du gestionnaire d’État.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-159">Since these C# events are outside the Blazor rendering pipeline, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on other components you wish to render in response to the state manager's events.</span></span>

<span data-ttu-id="6c8d1-160">Cela est similaire au cas antérieur à <xref:System.Timers.Timer?displayProperty=fullName> dans la section de la [section précédente](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) .</span><span class="sxs-lookup"><span data-stu-id="6c8d1-160">This is similar to the earlier case with <xref:System.Timers.Timer?displayProperty=fullName> in the [previous section](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) section.</span></span> <span data-ttu-id="6c8d1-161">Étant donné que la pile des appels d’exécution reste généralement dans le contexte de synchronisation du convertisseur, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> n’est normalement pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="6c8d1-161">Since the execution call stack typically remains on the renderer's synchronization context, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> isn't normally required.</span></span> <span data-ttu-id="6c8d1-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> est requis uniquement si la logique échappe au contexte de synchronisation, par exemple en appelant <xref:System.Threading.Tasks.Task.ContinueWith%2A> sur un <xref:System.Threading.Tasks.Task> ou en attente d’un <xref:System.Threading.Tasks.Task> avec [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) .</span><span class="sxs-lookup"><span data-stu-id="6c8d1-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> is only required if the logic escapes the synchronization context, such as calling <xref:System.Threading.Tasks.Task.ContinueWith%2A> on a <xref:System.Threading.Tasks.Task> or awaiting a <xref:System.Threading.Tasks.Task> with [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span></span>
