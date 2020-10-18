---
title: ASP.NET Core des Blazor scénarios avancés
author: guardrex
description: En savoir plus sur les scénarios avancés dans Blazor , y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
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
ms.openlocfilehash: 295e5dd025afc486be08ecadbf661bf765c2745f
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113606"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a>ASP.NET Core des Blazor scénarios avancés

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

## <a name="no-locblazor-server-circuit-handler"></a>Blazor Server Gestionnaire de circuit

Blazor Server permet au code de définir un *Gestionnaire de circuit*qui permet d’exécuter du code sur les modifications de l’état du circuit d’un utilisateur. Un gestionnaire de circuit est implémenté en dérivant de `CircuitHandler` et en inscrivant la classe dans le conteneur de services de l’application. L’exemple suivant d’un gestionnaire de circuit effectue le suivi des SignalR connexions ouvertes :

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

Les gestionnaires de circuits sont inscrits à l’aide de DI. Les instances délimitées sont créées par instance d’un circuit. À l’aide `TrackingCircuitHandler` de dans l’exemple précédent, un service Singleton est créé car l’état de tous les circuits doit être suivi :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Si les méthodes d’un gestionnaire de circuit personnalisé lèvent une exception non gérée, l’exception est irrécupérable pour le Blazor Server circuit. Pour tolérer des exceptions dans le code d’un gestionnaire ou les méthodes appelées, encapsulez le code dans une ou plusieurs [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instructions avec la gestion des erreurs et la journalisation.

Lorsqu’un circuit se termine parce qu’un utilisateur s’est déconnecté et que l’infrastructure nettoie l’état du circuit, le Framework supprime l’étendue de l’injection de port. La suppression de l’étendue supprime tous les services d’étendue de circuit qui implémentent <xref:System.IDisposable?displayProperty=fullName> . Si un service d’injection de services lève une exception non gérée pendant la suppression, le Framework journalise l’exception.

## <a name="manual-rendertreebuilder-logic"></a>Logique RenderTreeBuilder manuelle

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> fournit des méthodes pour manipuler des composants et des éléments, notamment la génération manuelle de composants dans du code C#.

> [!NOTE]
> L’utilisation de <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> pour créer des composants est un scénario avancé. Un composant mal formé (par exemple, une balise de balisage non fermée) peut entraîner un comportement indéfini.

Prenons le `PetDetails` composant suivant, qui peut être intégré manuellement à un autre composant :

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Dans l’exemple suivant, la boucle de la `CreateComponent` méthode génère trois `PetDetails` composants. Dans <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> les méthodes avec un numéro de séquence, les numéros de séquence sont les numéros de ligne de code source. L' Blazor algorithme de différence s’appuie sur les numéros de séquence correspondant à des lignes de code distinctes, et non sur des appels d’appel distincts. Lors de la création d’un composant avec des <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> méthodes, coder en dur les arguments pour les numéros de séquence. **L’utilisation d’un calcul ou d’un compteur pour générer le numéro de séquence peut entraîner des performances médiocres.** Pour plus d’informations, consultez la section [numéros de séquence liés à des numéros de ligne de code et non à un ordre d’exécution](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

`BuiltContent` -

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
> Les types dans <xref:Microsoft.AspNetCore.Components.RenderTree> autorisent le traitement des *résultats* des opérations de rendu. Il s’agit des détails internes de l' Blazor implémentation du Framework. Ces types doivent être considérés comme *instables* et susceptibles d’être modifiés dans les versions ultérieures.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Les numéros de séquence sont liés aux numéros de ligne de code et non à l’ordre d’exécution

Razor les fichiers de composants ( `.razor` ) sont toujours compilés. La compilation est un avantage potentiel de l’interprétation du code, car l’étape de compilation peut être utilisée pour injecter des informations qui améliorent les performances de l’application au moment de l’exécution.

Un exemple clé de ces améliorations concerne les *numéros de séquence*. Les numéros de séquence indiquent au runtime que les sorties proviennent de lignes de code distinctes et ordonnées. Le runtime utilise ces informations pour générer des différences d’arborescence efficaces en temps linéaire, ce qui est beaucoup plus rapide qu’un algorithme de comparaison d’arborescence générale.

Prenons le Razor fichier composant ( `.razor` ) suivant :

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Le code précédent se compile comme suit :

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Lorsque le code s’exécute pour la première fois, si `someFlag` est `true` , le générateur reçoit :

| Séquence | Type      | Données   |
| :------: | --------- | :----: |
| 0        | Nœud de texte | Premier  |
| 1        | Nœud de texte | Seconde |

Imaginez que `someFlag` devient `false` et le balisage est de nouveau restitué. Cette fois-ci, le générateur reçoit :

| Séquence | Type       | Données   |
| :------: | ---------- | :----: |
| 1        | Nœud de texte  | Seconde |

Quand le runtime effectue une comparaison, il constate que l’élément au niveau de la séquence `0` a été supprimé. il génère donc le *script d’édition*trivial suivant :

* Supprimez le premier nœud de texte.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Le problème de la génération par programmation des numéros de séquence

Imaginez plutôt que vous avez écrit la logique de générateur d’arborescence de rendu suivante :

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

La première sortie est désormais :

| Séquence | Type      | Données   |
| :------: | --------- | :----: |
| 0        | Nœud de texte | Premier  |
| 1        | Nœud de texte | Seconde |

Ce résultat est identique au cas précédent, donc aucun problème négatif n’existe. `someFlag` se trouve `false` sur le deuxième rendu et la sortie est :

| Séquence | Type      | Données   |
| :------: | --------- | ------ |
| 0        | Nœud de texte | Seconde |

Cette fois-ci, l’algorithme diff constate que *deux* modifications ont été apportées, et l’algorithme génère le script Edit suivant :

* Remplacez la valeur du premier nœud de texte par `Second` .
* Supprimez le deuxième nœud de texte.

La génération des numéros de séquence a perdu toutes les informations utiles sur l’emplacement où les `if/else` branches et les boucles étaient présentes dans le code d’origine. Il en résulte une comparaison de **deux fois plus longtemps** qu’auparavant.

Il s’agit d’un exemple trivial. Dans des cas plus réalistes avec des structures complexes et profondément imbriquées, et surtout avec des boucles, le coût des performances est généralement plus élevé. Au lieu d’identifier immédiatement les blocs de boucle ou les branches qui ont été insérés ou supprimés, l’algorithme diff doit être recurse profondément dans les arborescences de rendu. Cela entraîne généralement la création de scripts de modification plus longs, car l’algorithme diff est mal informé de la relation entre les anciennes et les nouvelles structures.

### <a name="guidance-and-conclusions"></a>Conseils et conclusions

* Les performances de l’application sont affectées si les numéros séquentiels sont générés dynamiquement.
* L’infrastructure ne peut pas créer ses propres numéros de séquence automatiquement au moment de l’exécution, car les informations nécessaires n’existent pas, sauf si elles sont capturées au moment de la compilation.
* N’écrivez pas de longs blocs de logique implémentée manuellement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> . Préférer les `.razor` fichiers et permettre au compilateur de gérer les numéros de séquence. Si vous ne pouvez pas éviter <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> une logique manuelle, fractionnez les blocs de code longs en éléments plus petits inclus dans des <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> appels. Chaque région a son propre espace distinct pour les numéros de séquence, ce qui vous permet de redémarrer à partir de zéro (ou tout autre nombre arbitraire) à l’intérieur de chaque région.
* Si les numéros de séquence sont codés en dur, l’algorithme diff exige uniquement que les numéros de séquence augmentent dans la valeur. La valeur initiale et les écarts ne sont pas pertinents. Une option légitime consiste à utiliser le numéro de ligne de code comme numéro de séquence, ou à commencer à partir de zéro et à augmenter par des ou des centaines (ou un intervalle de préférence). 
* Blazor utilise des numéros de séquence, tandis que d’autres infrastructures d’interface utilisateur de comparaison d’arborescence ne les utilisent pas. La comparaison est beaucoup plus rapide lorsque les numéros de séquence sont utilisés et Blazor présente l’avantage d’une étape de compilation qui traite automatiquement les numéros séquentiels pour les développeurs qui créent des `.razor` fichiers.
