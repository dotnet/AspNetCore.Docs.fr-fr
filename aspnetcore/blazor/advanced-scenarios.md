---
title: ASP.NET Core des Blazor scénarios avancés
author: guardrex
description: En savoir plus sur les scénarios avancés dans Blazor , y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: 95714b3c0d21d3b348a9a8a984e2a42e7708499e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056554"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a><span data-ttu-id="3e3ca-103">ASP.NET Core des Blazor scénarios avancés</span><span class="sxs-lookup"><span data-stu-id="3e3ca-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="3e3ca-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3e3ca-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="no-locblazor-server-circuit-handler"></a><span data-ttu-id="3e3ca-105">Blazor Server Gestionnaire de circuit</span><span class="sxs-lookup"><span data-stu-id="3e3ca-105">Blazor Server circuit handler</span></span>

<span data-ttu-id="3e3ca-106">Blazor Server permet au code de définir un *Gestionnaire de circuit* qui permet d’exécuter du code sur les modifications de l’état du circuit d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-106">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="3e3ca-107">Un gestionnaire de circuit est implémenté en dérivant de `CircuitHandler` et en inscrivant la classe dans le conteneur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="3e3ca-108">L’exemple suivant d’un gestionnaire de circuit effectue le suivi des SignalR connexions ouvertes :</span><span class="sxs-lookup"><span data-stu-id="3e3ca-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="3e3ca-109">Les gestionnaires de circuits sont inscrits à l’aide de DI.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="3e3ca-110">Les instances délimitées sont créées par instance d’un circuit.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="3e3ca-111">À l’aide `TrackingCircuitHandler` de dans l’exemple précédent, un service Singleton est créé car l’état de tous les circuits doit être suivi :</span><span class="sxs-lookup"><span data-stu-id="3e3ca-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="3e3ca-112">Si les méthodes d’un gestionnaire de circuit personnalisé lèvent une exception non gérée, l’exception est irrécupérable pour le Blazor Server circuit.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="3e3ca-113">Pour tolérer des exceptions dans le code d’un gestionnaire ou les méthodes appelées, encapsulez le code dans une ou plusieurs [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instructions avec la gestion des erreurs et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="3e3ca-114">Lorsqu’un circuit se termine parce qu’un utilisateur s’est déconnecté et que l’infrastructure nettoie l’état du circuit, le Framework supprime l’étendue de l’injection de port.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="3e3ca-115">La suppression de l’étendue supprime tous les services d’étendue de circuit qui implémentent <xref:System.IDisposable?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="3e3ca-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="3e3ca-116">Si un service d’injection de services lève une exception non gérée pendant la suppression, le Framework journalise l’exception.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="3e3ca-117">Logique RenderTreeBuilder manuelle</span><span class="sxs-lookup"><span data-stu-id="3e3ca-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="3e3ca-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> fournit des méthodes pour manipuler des composants et des éléments, notamment la génération manuelle de composants dans du code C#.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="3e3ca-119">L’utilisation de <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> pour créer des composants est un scénario avancé.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-119">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="3e3ca-120">Un composant mal formé (par exemple, une balise de balisage non fermée) peut entraîner un comportement indéfini.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="3e3ca-121">Prenons le `PetDetails` composant suivant, qui peut être intégré manuellement à un autre composant :</span><span class="sxs-lookup"><span data-stu-id="3e3ca-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="3e3ca-122">Dans l’exemple suivant, la boucle de la `CreateComponent` méthode génère trois `PetDetails` composants.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="3e3ca-123">Dans <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> les méthodes avec un numéro de séquence, les numéros de séquence sont les numéros de ligne de code source.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-123">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="3e3ca-124">L' Blazor algorithme de différence s’appuie sur les numéros de séquence correspondant à des lignes de code distinctes, et non sur des appels d’appel distincts.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="3e3ca-125">Lors de la création d’un composant avec des <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> méthodes, coder en dur les arguments pour les numéros de séquence.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-125">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="3e3ca-126">**L’utilisation d’un calcul ou d’un compteur pour générer le numéro de séquence peut entraîner des performances médiocres.**</span><span class="sxs-lookup"><span data-stu-id="3e3ca-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="3e3ca-127">Pour plus d’informations, consultez la section [numéros de séquence liés à des numéros de ligne de code et non à un ordre d’exécution](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="3e3ca-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="3e3ca-128">`BuiltContent` -</span><span class="sxs-lookup"><span data-stu-id="3e3ca-128">`BuiltContent` component:</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <span data-ttu-id="3e3ca-129">Les types dans <xref:Microsoft.AspNetCore.Components.RenderTree> autorisent le traitement des *résultats* des opérations de rendu.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-129">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="3e3ca-130">Il s’agit des détails internes de l' Blazor implémentation du Framework.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="3e3ca-131">Ces types doivent être considérés comme *instables* et susceptibles d’être modifiés dans les versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="3e3ca-132">Les numéros de séquence sont liés aux numéros de ligne de code et non à l’ordre d’exécution</span><span class="sxs-lookup"><span data-stu-id="3e3ca-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="3e3ca-133">Razor les fichiers de composants ( `.razor` ) sont toujours compilés.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-133">Razor component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="3e3ca-134">La compilation est un avantage potentiel de l’interprétation du code, car l’étape de compilation peut être utilisée pour injecter des informations qui améliorent les performances de l’application au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="3e3ca-135">Un exemple clé de ces améliorations concerne les *numéros de séquence*.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="3e3ca-136">Les numéros de séquence indiquent au runtime que les sorties proviennent de lignes de code distinctes et ordonnées.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="3e3ca-137">Le runtime utilise ces informations pour générer des différences d’arborescence efficaces en temps linéaire, ce qui est beaucoup plus rapide qu’un algorithme de comparaison d’arborescence générale.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="3e3ca-138">Prenons le Razor fichier composant ( `.razor` ) suivant :</span><span class="sxs-lookup"><span data-stu-id="3e3ca-138">Consider the following Razor component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="3e3ca-139">Le code précédent se compile comme suit :</span><span class="sxs-lookup"><span data-stu-id="3e3ca-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="3e3ca-140">Lorsque le code s’exécute pour la première fois, si `someFlag` est `true` , le générateur reçoit :</span><span class="sxs-lookup"><span data-stu-id="3e3ca-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="3e3ca-141">Séquence</span><span class="sxs-lookup"><span data-stu-id="3e3ca-141">Sequence</span></span> | <span data-ttu-id="3e3ca-142">Type</span><span class="sxs-lookup"><span data-stu-id="3e3ca-142">Type</span></span>      | <span data-ttu-id="3e3ca-143">Données</span><span class="sxs-lookup"><span data-stu-id="3e3ca-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="3e3ca-144">0</span><span class="sxs-lookup"><span data-stu-id="3e3ca-144">0</span></span>        | <span data-ttu-id="3e3ca-145">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="3e3ca-145">Text node</span></span> | <span data-ttu-id="3e3ca-146">Premier</span><span class="sxs-lookup"><span data-stu-id="3e3ca-146">First</span></span>  |
| <span data-ttu-id="3e3ca-147">1</span><span class="sxs-lookup"><span data-stu-id="3e3ca-147">1</span></span>        | <span data-ttu-id="3e3ca-148">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="3e3ca-148">Text node</span></span> | <span data-ttu-id="3e3ca-149">Seconde</span><span class="sxs-lookup"><span data-stu-id="3e3ca-149">Second</span></span> |

<span data-ttu-id="3e3ca-150">Imaginez que `someFlag` devient `false` et le balisage est de nouveau restitué.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="3e3ca-151">Cette fois-ci, le générateur reçoit :</span><span class="sxs-lookup"><span data-stu-id="3e3ca-151">This time, the builder receives:</span></span>

| <span data-ttu-id="3e3ca-152">Séquence</span><span class="sxs-lookup"><span data-stu-id="3e3ca-152">Sequence</span></span> | <span data-ttu-id="3e3ca-153">Type</span><span class="sxs-lookup"><span data-stu-id="3e3ca-153">Type</span></span>       | <span data-ttu-id="3e3ca-154">Données</span><span class="sxs-lookup"><span data-stu-id="3e3ca-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="3e3ca-155">1</span><span class="sxs-lookup"><span data-stu-id="3e3ca-155">1</span></span>        | <span data-ttu-id="3e3ca-156">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="3e3ca-156">Text node</span></span>  | <span data-ttu-id="3e3ca-157">Seconde</span><span class="sxs-lookup"><span data-stu-id="3e3ca-157">Second</span></span> |

<span data-ttu-id="3e3ca-158">Quand le runtime effectue une comparaison, il constate que l’élément au niveau de la séquence `0` a été supprimé. il génère donc le *script d’édition* trivial suivant :</span><span class="sxs-lookup"><span data-stu-id="3e3ca-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="3e3ca-159">Supprimez le premier nœud de texte.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="3e3ca-160">Le problème de la génération par programmation des numéros de séquence</span><span class="sxs-lookup"><span data-stu-id="3e3ca-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="3e3ca-161">Imaginez plutôt que vous avez écrit la logique de générateur d’arborescence de rendu suivante :</span><span class="sxs-lookup"><span data-stu-id="3e3ca-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="3e3ca-162">La première sortie est désormais :</span><span class="sxs-lookup"><span data-stu-id="3e3ca-162">Now, the first output is:</span></span>

| <span data-ttu-id="3e3ca-163">Séquence</span><span class="sxs-lookup"><span data-stu-id="3e3ca-163">Sequence</span></span> | <span data-ttu-id="3e3ca-164">Type</span><span class="sxs-lookup"><span data-stu-id="3e3ca-164">Type</span></span>      | <span data-ttu-id="3e3ca-165">Données</span><span class="sxs-lookup"><span data-stu-id="3e3ca-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="3e3ca-166">0</span><span class="sxs-lookup"><span data-stu-id="3e3ca-166">0</span></span>        | <span data-ttu-id="3e3ca-167">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="3e3ca-167">Text node</span></span> | <span data-ttu-id="3e3ca-168">Premier</span><span class="sxs-lookup"><span data-stu-id="3e3ca-168">First</span></span>  |
| <span data-ttu-id="3e3ca-169">1</span><span class="sxs-lookup"><span data-stu-id="3e3ca-169">1</span></span>        | <span data-ttu-id="3e3ca-170">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="3e3ca-170">Text node</span></span> | <span data-ttu-id="3e3ca-171">Seconde</span><span class="sxs-lookup"><span data-stu-id="3e3ca-171">Second</span></span> |

<span data-ttu-id="3e3ca-172">Ce résultat est identique au cas précédent, donc aucun problème négatif n’existe.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="3e3ca-173">`someFlag` se trouve `false` sur le deuxième rendu et la sortie est :</span><span class="sxs-lookup"><span data-stu-id="3e3ca-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="3e3ca-174">Séquence</span><span class="sxs-lookup"><span data-stu-id="3e3ca-174">Sequence</span></span> | <span data-ttu-id="3e3ca-175">Type</span><span class="sxs-lookup"><span data-stu-id="3e3ca-175">Type</span></span>      | <span data-ttu-id="3e3ca-176">Données</span><span class="sxs-lookup"><span data-stu-id="3e3ca-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="3e3ca-177">0</span><span class="sxs-lookup"><span data-stu-id="3e3ca-177">0</span></span>        | <span data-ttu-id="3e3ca-178">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="3e3ca-178">Text node</span></span> | <span data-ttu-id="3e3ca-179">Seconde</span><span class="sxs-lookup"><span data-stu-id="3e3ca-179">Second</span></span> |

<span data-ttu-id="3e3ca-180">Cette fois-ci, l’algorithme diff constate que *deux* modifications ont été apportées, et l’algorithme génère le script Edit suivant :</span><span class="sxs-lookup"><span data-stu-id="3e3ca-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="3e3ca-181">Remplacez la valeur du premier nœud de texte par `Second` .</span><span class="sxs-lookup"><span data-stu-id="3e3ca-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="3e3ca-182">Supprimez le deuxième nœud de texte.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-182">Remove the second text node.</span></span>

<span data-ttu-id="3e3ca-183">La génération des numéros de séquence a perdu toutes les informations utiles sur l’emplacement où les `if/else` branches et les boucles étaient présentes dans le code d’origine.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="3e3ca-184">Il en résulte une comparaison de **deux fois plus longtemps** qu’auparavant.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="3e3ca-185">Il s’agit d’un exemple trivial.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-185">This is a trivial example.</span></span> <span data-ttu-id="3e3ca-186">Dans des cas plus réalistes avec des structures complexes et profondément imbriquées, et surtout avec des boucles, le coût des performances est généralement plus élevé.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="3e3ca-187">Au lieu d’identifier immédiatement les blocs de boucle ou les branches qui ont été insérés ou supprimés, l’algorithme diff doit être recurse profondément dans les arborescences de rendu.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="3e3ca-188">Cela entraîne généralement la création de scripts de modification plus longs, car l’algorithme diff est mal informé de la relation entre les anciennes et les nouvelles structures.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="3e3ca-189">Conseils et conclusions</span><span class="sxs-lookup"><span data-stu-id="3e3ca-189">Guidance and conclusions</span></span>

* <span data-ttu-id="3e3ca-190">Les performances de l’application sont affectées si les numéros séquentiels sont générés dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="3e3ca-191">L’infrastructure ne peut pas créer ses propres numéros de séquence automatiquement au moment de l’exécution, car les informations nécessaires n’existent pas, sauf si elles sont capturées au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="3e3ca-192">N’écrivez pas de longs blocs de logique implémentée manuellement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3e3ca-192">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="3e3ca-193">Préférer les `.razor` fichiers et permettre au compilateur de gérer les numéros de séquence.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-193">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="3e3ca-194">Si vous ne pouvez pas éviter <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> une logique manuelle, fractionnez les blocs de code longs en éléments plus petits inclus dans des <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> appels.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-194">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="3e3ca-195">Chaque région a son propre espace distinct pour les numéros de séquence, ce qui vous permet de redémarrer à partir de zéro (ou tout autre nombre arbitraire) à l’intérieur de chaque région.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="3e3ca-196">Si les numéros de séquence sont codés en dur, l’algorithme diff exige uniquement que les numéros de séquence augmentent dans la valeur.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="3e3ca-197">La valeur initiale et les écarts ne sont pas pertinents.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="3e3ca-198">Une option légitime consiste à utiliser le numéro de ligne de code comme numéro de séquence, ou à commencer à partir de zéro et à augmenter par des ou des centaines (ou un intervalle de préférence).</span><span class="sxs-lookup"><span data-stu-id="3e3ca-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="3e3ca-199">Blazor utilise des numéros de séquence, tandis que d’autres infrastructures d’interface utilisateur de comparaison d’arborescence ne les utilisent pas.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-199">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="3e3ca-200">La comparaison est beaucoup plus rapide lorsque les numéros de séquence sont utilisés et Blazor présente l’avantage d’une étape de compilation qui traite automatiquement les numéros séquentiels pour les développeurs qui créent des `.razor` fichiers.</span><span class="sxs-lookup"><span data-stu-id="3e3ca-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
