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
# <a name="aspnet-core-blazor-component-rendering"></a>Rendu des composants de ASP.NET Core Blazor

Par [Steve Sanderson](https://github.com/SteveSandersonMS)

Les composants *doivent* être rendus lorsqu’ils sont ajoutés pour la première fois à la hiérarchie des composants par leur composant parent. Il s’agit de la seule fois où un composant doit absolument être rendu.

Les composants peuvent choisir d' *être* rendus à toute autre heure en fonction de leur propre logique et de leurs conventions.

## <a name="conventions-for-componentbase"></a>Conventions pour `ComponentBase`

Par défaut, Razor les composants ( `.razor` ) héritent de la <xref:Microsoft.AspNetCore.Components.ComponentBase> classe de base, qui contient une logique pour déclencher le rerendu aux moments suivants :

* Après l’application d’un jeu de paramètres mis à jour à partir d’un composant parent.
* Après l’application d’une valeur mise à jour pour un paramètre en cascade.
* Après la notification d’un événement et l’appel de l’un de ses propres gestionnaires d’événements.
* Après un appel à sa propre <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> méthode.

Les composants hérités de <xref:Microsoft.AspNetCore.Components.ComponentBase> ignorent les réaffichages en raison de mises à jour de paramètres si l’une des conditions suivantes est vraie :

* Toutes les valeurs de paramètre sont des types primitifs immuables connus (par exemple,, `int` `string` , `DateTime` ) et n’ont pas changé depuis la définition du jeu de paramètres précédent.
* La méthode du composant <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> retourne `false` .

Pour plus d'informations sur le <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, consultez <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.

## <a name="control-the-rendering-flow"></a>Contrôler le workflow de rendu

Dans la plupart des cas, <xref:Microsoft.AspNetCore.Components.ComponentBase> les conventions génèrent un sous-ensemble correct de réaffichages de composants après l’exécution d’un événement. Les développeurs ne sont généralement pas tenus de fournir une logique manuelle pour indiquer à l’infrastructure les composants à rerestituer et à quel moment les régénérer. L’effet global des conventions de l’infrastructure est que le composant recevant un événement se reproduit lui-même, ce qui déclenche de manière récursive le rerendu des composants descendants dont les valeurs de paramètres peuvent avoir changé.

Pour plus d’informations sur les implications en termes de performances des conventions de l’infrastructure et sur la façon d’optimiser la hiérarchie des composants d’une application, consultez <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed> .

## <a name="when-to-call-statehaschanged"></a>Quand appeler `StateHasChanged`

La <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> méthode vous permet de déclencher un rendu à tout moment. Toutefois, veillez à ne pas appeler <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> inutilement, ce qui est une erreur courante, car elle impose des coûts de rendu inutiles.

Vous *n’avez pas* besoin d’appeler <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> lorsque :

* La gestion régulière des événements, qu’il s’agisse de manière synchrone ou asynchrone, depuis, <xref:Microsoft.AspNetCore.Components.ComponentBase> déclenche un rendu pour la plupart des gestionnaires d’événements de routine.
* L’implémentation d’une logique de cycle de vie typique, telle que [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) ou [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) , qu’il s’agisse de synchonrously ou asynchrone, depuis <xref:Microsoft.AspNetCore.Components.ComponentBase> déclenche un rendu pour les événements de cycle de vie typiques.

Toutefois, il peut être judicieux d’appeler <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> dans les cas décrits dans les sections suivantes :

* [Un gestionnaire asynchrone implique plusieurs phases asynchrones](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [Réception d’un appel d’un événement externe au Blazor système de gestion des événements et du rendu](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [Pour afficher un composant en dehors de la sous-arborescence qui est rerendue par un événement particulier](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a>Un gestionnaire asynchrone implique plusieurs phases asynchrones

Prenons le `Counter` composant suivant, qui met à jour le nombre quatre fois à chaque clic.

`Pages/Counter.razor`:

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

En raison de la façon dont les tâches sont définies dans .NET, un destinataire d’un <xref:System.Threading.Tasks.Task> peut uniquement observer son achèvement final, et non les États asynchrones intermédiaires. Par conséquent, <xref:Microsoft.AspNetCore.Components.ComponentBase> peut déclencher le rerendu uniquement lorsque <xref:System.Threading.Tasks.Task> est retourné pour la première fois et lorsque <xref:System.Threading.Tasks.Task> finally se termine. Il ne peut pas savoir s’afficher à un autre point intermédiaire. Si vous souhaitez effectuer un rerendu à des points intermédiaires, utilisez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a>Réception d’un appel d’un événement externe au Blazor système de gestion des événements et du rendu

<xref:Microsoft.AspNetCore.Components.ComponentBase> connaît uniquement ses propres méthodes de cycle de vie et les Blazor événements déclenchés. <xref:Microsoft.AspNetCore.Components.ComponentBase> ne connaît pas les autres événements qui peuvent se produire dans votre code. Par exemple, les événements C# déclenchés par un magasin de données personnalisé sont inconnus de Blazor . Pour que ces événements déclenchent le rerendu pour afficher les valeurs mises à jour dans l’interface utilisateur, utilisez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .

Dans un autre cas d’utilisation, considérez le `Counter` composant suivant qui utilise <xref:System.Timers.Timer?displayProperty=fullName> pour mettre à jour le nombre à intervalles réguliers et appelle <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> pour mettre à jour l’interface utilisateur.

`Pages/CounterWithTimerDisposal.razor`:

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

Dans l’exemple précédent :

* `OnTimerCallback` doit appeler <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> car Blazor n’est pas conscient des modifications apportées à `currentCount` dans le rappel. `OnTimerCallback` s’exécute en dehors de tout Blazor Workflow de rendu managé ou notification d’événement.
* Le composant implémente <xref:System.IDisposable> , où <xref:System.Timers.Timer> est supprimé lorsque l’infrastructure appelle la `Dispose` méthode. Pour plus d’informations, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

De même, étant donné que le rappel est appelé en dehors du Blazor contexte de synchronisation de, il est nécessaire d’encapsuler la logique dans <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> pour la déplacer vers le contexte de synchronisation du convertisseur. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> peut uniquement être appelé à partir du contexte de synchronisation du convertisseur et lève une exception dans le cas contraire. Cela équivaut au marshaling du thread d’interface utilisateur dans d’autres infrastructures d’interface utilisateur.

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a>Pour restituer un composant à l’extérieur de la sous-arborescence qui est rerendue par un événement particulier

Votre interface utilisateur peut impliquer la distribution d’un événement à un composant, la modification d’un État et la nécessité de rerestituer un composant complètement différent qui n’est pas un descendant de celui recevant l’événement.

L’une des façons de traiter ce scénario est d’avoir une classe de *gestion d’État* , peut-être comme un service di, injectée dans plusieurs composants. Quand un composant appelle une méthode sur le gestionnaire d’État, le gestionnaire d’État peut déclencher un événement C# qui est ensuite reçu par un composant indépendant.

Étant donné que ces événements C# sont en dehors du Blazor pipeline de rendu, appelez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> sur d’autres composants que vous souhaitez afficher en réponse aux événements du gestionnaire d’État.

Cela est similaire au cas antérieur à <xref:System.Timers.Timer?displayProperty=fullName> dans la section de la [section précédente](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) . Étant donné que la pile des appels d’exécution reste généralement dans le contexte de synchronisation du convertisseur, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> n’est normalement pas obligatoire. <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> est requis uniquement si la logique échappe au contexte de synchronisation, par exemple en appelant <xref:System.Threading.Tasks.Task.ContinueWith%2A> sur un <xref:System.Threading.Tasks.Task> ou en attente d’un <xref:System.Threading.Tasks.Task> avec [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) .
