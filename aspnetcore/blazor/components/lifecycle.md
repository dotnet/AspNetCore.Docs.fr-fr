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
# <a name="aspnet-core-blazor-lifecycle"></a>BlazorCycle de vie ASP.net Core

L' Blazor infrastructure comprend des méthodes de cycle de vie synchrones et asynchrones. Substituez les méthodes de cycle de vie pour effectuer des opérations supplémentaires sur les composants lors de l’initialisation et du rendu des composants.

Les diagrammes suivants illustrent le Blazor cycle de vie. Les méthodes de cycle de vie sont définies avec des exemples dans les sections suivantes de cet article.

Événements de cycle de vie des composants :

1. Si le composant est rendu pour la première fois sur une demande :
   * Créez l’instance du composant.
   * Effectuez l’injection de propriété. Exécutez [`SetParametersAsync`](#before-parameters-are-set).
   * Appelez [`OnInitialized{Async}`](#component-initialization-methods) . Si un <xref:System.Threading.Tasks.Task> est retourné, <xref:System.Threading.Tasks.Task> est attendu et le composant est rendu. Si un <xref:System.Threading.Tasks.Task> n’est pas retourné, le composant est rendu.
1. Appelez [`OnParametersSet{Async}`](#after-parameters-are-set) et affichez le composant. Si un <xref:System.Threading.Tasks.Task> est retourné à partir de `OnParametersSetAsync` , <xref:System.Threading.Tasks.Task> est attendu, puis le composant est restitué à un rendu.

![Événements de cycle de vie d’un composant ::: No-Loc (Razor) ::: Component dans ::: No-Loc (éblouissant) :::](lifecycle/_static/lifecycle1.png)

Traitement des événements Document Object Model (DOM) :

1. Le gestionnaire d’événements est exécuté.
1. Si un <xref:System.Threading.Tasks.Task> est retourné, <xref:System.Threading.Tasks.Task> est attendu, puis le composant est rendu. Si un <xref:System.Threading.Tasks.Task> n’est pas retourné, le composant est rendu.

![Traitement des événements Document Object Model (DOM)](lifecycle/_static/lifecycle2.png)

Le `Render` cycle de vie :

1. Évitez les opérations de rendu supplémentaires sur le composant :
   * Après le premier rendu.
   * Lorsque [`ShouldRender`](#suppress-ui-refreshing) est `false` .
1. Générez la diffation de l’arborescence de rendu (différence) et le rendu du composant.
1. Attendez le DOM à mettre à jour.
1. Appelez [`OnAfterRender{Async}`](#after-component-render) .

![Cycle de vie du rendu](lifecycle/_static/lifecycle3.png)

Les développeurs appellent pour [`StateHasChanged`](#state-changes) générer un rendu. Pour plus d’informations, consultez <xref:blazor/components/rendering>.

## <a name="lifecycle-methods"></a>Méthodes de cycle de vie

### <a name="before-parameters-are-set"></a>Les paramètres Before sont définis

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> définit les paramètres fournis par le parent du composant dans l’arborescence de rendu ou à partir des paramètres de routage. En remplaçant la méthode, le code de développeur peut interagir directement avec les <xref:Microsoft.AspNetCore.Components.ParameterView> paramètres de.

Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> affecte la `Param` valeur du paramètre à `value` si l’analyse d’un paramètre d’itinéraire pour `Param` est réussie. Dans le cas `value` contraire `null` , la valeur est affichée par le composant.

Bien que la [correspondance des paramètres d’itinéraire ne](xref:blazor/fundamentals/routing#route-parameters)respecte pas la casse, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> ne met en correspondance que les noms de paramètres respectant la casse dans le modèle de routage. L’exemple suivant est requis pour utiliser `/{Param?}` , et non `/{param?}` , afin d’extraire la valeur. Si `/{param?}` est utilisé dans ce scénario, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> retourne `false` et `message` n’a pas la valeur String.

`Pages/SetParametersAsyncExample.razor`:

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

<xref:Microsoft.AspNetCore.Components.ParameterView> contient l’ensemble des valeurs de paramètre pour le composant chaque fois que <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> est appelé.

L’implémentation par défaut de <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> définit la valeur de chaque propriété avec [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) l' [ `[CascadingParameter]` attribut](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) ou ayant une valeur correspondante dans le <xref:Microsoft.AspNetCore.Components.ParameterView> . Les paramètres qui n’ont pas de valeur correspondante dans <xref:Microsoft.AspNetCore.Components.ParameterView> ne sont pas modifiés.

Si [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) n’est pas appelé, le code personnalisé peut interpréter la valeur des paramètres entrants de la façon requise. Par exemple, il n’est pas nécessaire d’assigner les paramètres entrants aux propriétés de la classe.

Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression. Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .

### <a name="component-initialization-methods"></a>Méthodes d’initialisation de composant

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> sont appelés lorsque le composant est initialisé après avoir reçu ses paramètres initiaux de son composant parent dans <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> . 

Utilisez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> lorsque le composant exécute une opération asynchrone et doit être actualisé lorsque l’opération est terminée.

Pour une opération synchrone, remplacez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :

```csharp
protected override void OnInitialized()
{
    ...
}
```

Pour effectuer une opération asynchrone, substituez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> et utilisez l' [`await`](/dotnet/csharp/language-reference/operators/await) opérateur sur l’opération :

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor Server applications qui [préaffichent le contenu](xref:blazor/fundamentals/signalr#render-mode) de l’appel à <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *deux reprises*:

* Une fois lorsque le composant est initialement restitué de manière statique dans le cadre de la page.
* Une deuxième fois lorsque le navigateur établit une connexion au serveur.

Pour empêcher <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> l’exécution à deux reprises du code du développeur, consultez la section [reconnexion avec état après le prérendu](#stateful-reconnection-after-prerendering) .

Pendant Blazor Server le prérendu d’une application, certaines actions, telles que l’appel de JavaScript, ne sont pas possibles, car une connexion avec le navigateur n’a pas été établie. Les composants peuvent avoir besoin d’être restitués différemment lorsqu’ils sont prérendus. Pour plus d’informations, consultez la section [détecter quand l’application est un prérendu](#detect-when-the-app-is-prerendering) .

Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression. Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .

### <a name="after-parameters-are-set"></a>Une fois les paramètres définis

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ou <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> sont appelés :

* Une fois le composant initialisé dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> ou <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
* Lorsque le composant parent est à nouveau rendu et fournit :
  * Seuls les types immuables primitifs connus dont au moins un paramètre a été modifié.
  * Tous les paramètres de type complexe. L’infrastructure ne peut pas savoir si les valeurs d’un paramètre de type complexe ont muté en interne, donc elle traite le jeu de paramètres comme étant modifié.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Un travail asynchrone lors de l’application de paramètres et de valeurs de propriété doit se produire pendant l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> événement de cycle de vie.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression. Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .

### <a name="after-component-render"></a>Après le rendu du composant

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sont appelées après la fin d’un rendu d’un composant. Les références d’élément et de composant sont remplies à ce stade. Utilisez cette étape pour effectuer des étapes d’initialisation supplémentaires à l’aide du contenu rendu, par exemple l’activation de bibliothèques JavaScript tierces qui opèrent sur les éléments DOM rendus.

Le `firstRender` paramètre pour <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :

* A la valeur `true` la première fois que l’instance de composant est restituée.
* Peut être utilisé pour s’assurer que le travail d’initialisation n’est effectué qu’une seule fois.

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
> Le travail asynchrone immédiatement après le rendu doit se produire pendant l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> événement de cycle de vie.
>
> Même si vous retournez un <xref:System.Threading.Tasks.Task> à partir de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , l’infrastructure ne planifie pas un cycle de rendu supplémentaire pour votre composant une fois cette tâche terminée. Cela permet d’éviter une boucle de rendu infinie. Elle est différente des autres méthodes de cycle de vie, qui planifient un cycle de rendu supplémentaire une fois que la tâche retournée est terminée.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *ne sont pas appelées pendant le processus de prérendu sur le serveur*. Les méthodes sont appelées lorsque le composant est rendu de manière interactive une fois le prérendu terminé. Lors du prérendu de l’application :

1. Le composant s’exécute sur le serveur pour produire un balisage HTML statique dans la réponse HTTP. Pendant cette phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ne sont pas appelés.
1. Quand `blazor.server.js` ou `blazor.webassembly.js` Démarrer dans le navigateur, le composant est redémarré en mode de rendu interactif. Après le redémarrage d’un composant, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **sont** appelés parce que l’application n’est plus à l’intérieur de la phase de prérendu.

Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression. Pour plus d’informations, consultez la section [suppression `IDisposable` de composant avec](#component-disposal-with-idisposable) .

### <a name="suppress-ui-refreshing"></a>Supprimer l’actualisation de l’interface utilisateur

Substituez <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pour supprimer l’actualisation de l’interface utilisateur. Si l’implémentation retourne `true` , l’interface utilisateur est actualisée :

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> est appelé chaque fois que le composant est restitué.

Même si <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> est substitué, le composant est toujours restitué initialement.

Pour plus d’informations, consultez <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.

## <a name="state-changes"></a>Modifications d'état

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifie le composant que son état a changé. Le cas échéant, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> l’appel de entraîne le rerendu du composant.

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> est appelé automatiquement pour les <xref:Microsoft.AspNetCore.Components.EventCallback> méthodes. Pour plus d’informations, consultez <xref:blazor/components/event-handling#eventcallback>.

Pour plus d’informations, consultez <xref:blazor/components/rendering>.

## <a name="handle-incomplete-async-actions-at-render"></a>Gérer les actions asynchrones incomplètes au rendu

Les actions asynchrones exécutées dans des événements de cycle de vie peuvent ne pas être terminées avant le rendu du composant. Les objets peuvent être `null` ou être remplis de façon incomplète avec des données pendant l’exécution de la méthode Lifecycle. Fournissez une logique de rendu pour confirmer que les objets sont initialisés. Affichez les éléments d’interface utilisateur d’espace réservé (par exemple, un message de chargement) tandis que les objets sont `null` .

Dans le `FetchData` composant des Blazor modèles, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> est substitué pour recevoir des données de prévision de manière asynchrone ( `forecasts` ). Lorsque `forecasts` a `null` la valeur, un message de chargement est affiché à l’utilisateur. Une fois la `Task` retournée <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> terminée, le composant est rerendu avec l’État mis à jour.

`Pages/FetchData.razor` dans le Blazor Server modèle :

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

## <a name="handle-errors"></a>des erreurs

Pour plus d’informations sur la gestion des erreurs lors de l’exécution de la méthode de cycle de vie, consultez <xref:blazor/fundamentals/handle-errors> .

## <a name="stateful-reconnection-after-prerendering"></a>Reconnexion avec état après le prérendu

Dans une Blazor Server application quand <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> est <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , le composant est initialement restitué de manière statique dans le cadre de la page. Une fois que le navigateur a établi une connexion au serveur, le composant est *à nouveau* rendu et le composant est maintenant interactif. Si la [`OnInitialized{Async}`](#component-initialization-methods) méthode de cycle de vie pour l’initialisation du composant est présente, la méthode est exécutée *deux fois*:

* Lorsque le composant est prérendu statiquement.
* Après l’établissement de la connexion au serveur.

Cela peut entraîner une modification notable des données affichées dans l’interface utilisateur lorsque le composant est finalement restitué.

Pour éviter le double-rendu du scénario dans une Blazor Server application :

* Transmettez un identificateur qui peut être utilisé pour mettre en cache l’État pendant le prérendu et pour récupérer l’état après le redémarrage de l’application.
* Utilisez l’identificateur pendant le prérendu pour enregistrer l’état du composant.
* Utilisez l’identificateur après le prérendu pour récupérer l’État mis en cache.

Le code suivant illustre une mise à jour `WeatherForecastService` dans une application basée sur un modèle Blazor Server qui évite le double rendu :

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

Pour plus d’informations sur le <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , consultez <xref:blazor/fundamentals/signalr#render-mode> .

## <a name="detect-when-the-app-is-prerendering"></a>Détecter quand l’application est prérendu

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a>Élimination des composants avec `IDisposable`

Si un composant implémente <xref:System.IDisposable> , l’infrastructure appelle la [méthode de suppression](/dotnet/standard/garbage-collection/implementing-dispose) lorsque le composant est supprimé de l’interface utilisateur, où les ressources non managées peuvent être libérées. La suppression peut avoir lieu à tout moment, y compris lors de [l’initialisation du composant](#component-initialization-methods). Le composant suivant implémente <xref:System.IDisposable> avec la [`@implements`](xref:mvc/views/razor#implements) Razor directive :

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

Si un objet nécessite une suppression, une expression lambda peut être utilisée pour supprimer l’objet lorsque <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> est appelé :

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

L’exemple précédent apparaît dans <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system> .

Pour les tâches de suppression asynchrones, utilisez `DisposeAsync` au lieu de <xref:System.IDisposable.Dispose> :

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>L’appel de dans `Dispose` n’est pas pris en charge. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> peut être appelé dans le cadre du détachement du convertisseur, donc demander des mises à jour de l’interface utilisateur à ce stade n’est pas pris en charge.

Annule l’abonnement des gestionnaires d’événements des événements .NET. Les exemples de [ Blazor formulaires](xref:blazor/forms-validation) suivants montrent comment annuler l’abonnement à un gestionnaire d’événements dans la `Dispose` méthode :

* Approche de champ privé et lambda

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

* Approche de la méthode privée

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

Lorsque des fonctions, des méthodes ou des expressions [anonymes](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions)sont utilisées, il n’est pas nécessaire d’implémenter <xref:System.IDisposable> et de désabonner des délégués. Toutefois, l’échec de l’annulation de l’abonnement à un délégué est un problème **lorsque l’objet qui expose l’événement indique la durée de vie du composant qui inscrit le délégué**. Dans ce cas, une fuite de mémoire est due au fait que le délégué inscrit conserve l’objet d’origine actif. Par conséquent, utilisez uniquement les approches suivantes lorsque vous savez que le délégué d’événement s’en supprime rapidement. En cas de doute sur la durée de vie des objets qui nécessitent une suppression, abonnez une méthode déléguée et supprimez correctement le délégué comme le montrent les exemples précédents.

* Approche de la méthode lambda anonyme (suppression explicite non requise)

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

* Approche d’expression lambda anonyme (suppression explicite non requise)

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

  L’exemple complet du code précédent avec des expressions lambda anonymes s’affiche dans <xref:blazor/forms-validation#validator-components> .

Pour plus d’informations, consultez [nettoyage des ressources non managées](/dotnet/standard/garbage-collection/unmanaged) et les rubriques qui le suivent sur l’implémentation des `Dispose` `DisposeAsync` méthodes et.

## <a name="cancelable-background-work"></a>Travail en arrière-plan annulable

Les composants effectuent souvent des tâches en arrière-plan de longue durée, telles que la création d’appels réseau ( <xref:System.Net.Http.HttpClient> ) et l’interaction avec les bases de données. Il est souhaitable d’arrêter le travail en arrière-plan pour économiser les ressources système dans plusieurs situations. Par exemple, les opérations asynchrones en arrière-plan ne s’arrêtent pas automatiquement lorsqu’un utilisateur quitte un composant.

Les autres raisons pour lesquelles les éléments de travail en arrière-plan peuvent nécessiter l’annulation sont les suivantes :

* Une tâche en arrière-plan en cours d’exécution a été démarrée avec des données d’entrée ou des paramètres de traitement défectueux.
* Le jeu actuel d’éléments de travail en arrière-plan doit être remplacé par un nouvel ensemble d’éléments de travail.
* La priorité des tâches en cours d’exécution doit être modifiée.
* L’application doit être arrêtée pour pouvoir être redéployée sur le serveur.
* Les ressources du serveur sont limitées, ce qui nécessite la replanification des éléments de travail en arrière-plan.

Pour implémenter un modèle de travail d’arrière-plan annulable dans un composant :

* Utilisez un <xref:System.Threading.CancellationTokenSource> et un <xref:System.Threading.CancellationToken> .
* Lors de [la suppression du composant](#component-disposal-with-idisposable) et à tout moment, l’annulation est souhaitée en annulant manuellement le jeton, puis appelez [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) pour signaler que le travail en arrière-plan doit être annulé.
* Une fois l’appel asynchrone retourné, appelez <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> sur le jeton.

Dans l’exemple suivant :

* `await Task.Delay(5000, cts.Token);` représente le travail en arrière-plan asynchrone de longue durée.
* `BackgroundResourceMethod` représente une méthode d’arrière-plan de longue durée qui ne doit pas démarrer si le `Resource` est supprimé avant l’appel de la méthode.

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

## <a name="blazor-server-reconnection-events"></a>Blazor Server événements de reconnexion

Les événements de cycle de vie des composants traités dans cet article fonctionnent séparément des [ Blazor Server gestionnaires d’événements de reconnexion de](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui). Quand une Blazor Server application perd sa SignalR connexion au client, seules les mises à jour de l’interface utilisateur sont interrompues. Les mises à jour de l’interface utilisateur sont reprises lorsque la connexion est rétablie. Pour plus d’informations sur la configuration et les événements du gestionnaire de circuit, consultez <xref:blazor/fundamentals/signalr> .
