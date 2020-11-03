---
title: Appeler des fonctions JavaScript à partir de méthodes .NET dans ASP.NET Core Blazor
author: guardrex
description: Découvrez comment appeler des fonctions JavaScript à partir de méthodes .NET dans des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/20/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 17d6087b884775a8bfcb41fe23296f508467e924
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234450"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a>Appeler des fonctions JavaScript à partir de méthodes .NET dans ASP.NET Core Blazor

Par [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)et [Luke Latham](https://github.com/guardrex)

Une Blazor application peut appeler des fonctions JavaScript à partir de méthodes .net et de méthodes .net à partir de fonctions JavaScript. Ces scénarios portent le nom de *l’interopérabilité de JavaScript* ( *js Interop* ).

Cet article traite de l’appel de fonctions JavaScript à partir de .NET. Pour plus d’informations sur la façon d’appeler des méthodes .NET à partir de JavaScript, consultez <xref:blazor/call-dotnet-from-javascript> .

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Pour appeler JavaScript à partir de .NET, utilisez l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction. Pour émettre des appels d’interopérabilité JS, injectez l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction dans votre composant. <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> prend un identificateur pour la fonction JavaScript que vous souhaitez appeler, ainsi que n’importe quel nombre d’arguments sérialisables JSON. L’identificateur de fonction est relatif à la portée globale ( `window` ). Si vous souhaitez appeler `window.someScope.someFunction` , l’identificateur est `someScope.someFunction` . Il n’est pas nécessaire d’inscrire la fonction avant qu’elle ne soit appelée. Le type de retour `T` doit également être sérialisable JSON. `T` doit correspondre au type .NET qui correspond le mieux au type JSON retourné.

Les fonctions JavaScript qui retournent une [promesse](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) sont appelées avec <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> . `InvokeAsync` désencapsule la promesse et retourne la valeur attendue par la promesse.

Pour Blazor Server les applications pour lesquelles le prérendu est activé, l’appel à JavaScript n’est pas possible pendant le prérendu initial. Les appels Interop JavaScript doivent être différés jusqu’à ce que la connexion avec le navigateur soit établie. Pour plus d’informations, consultez la section [détecter le Blazor Server prérendu d’une application](#detect-when-a-blazor-server-app-is-prerendering) .

L’exemple suivant est basé sur [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) , un décodeur basé sur JavaScript. L’exemple montre comment appeler une fonction JavaScript à partir d’une méthode C# qui décharge une spécification du code de développeur vers une API JavaScript existante. La fonction JavaScript accepte un tableau d’octets d’une méthode C#, décode le tableau et retourne le texte au composant pour l’affichage.

À l’intérieur `<head>` de l’élément de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), fournissez une fonction JavaScript qui utilise `TextDecoder` pour décoder un tableau passé et retourner la valeur décodée :

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Du code JavaScript, tel que le code illustré dans l’exemple précédent, peut également être chargé à partir d’un fichier JavaScript ( `.js` ) avec une référence au fichier de script :

```html
<script src="exampleJsInterop.js"></script>
```

Le composant suivant :

* Appelle la `convertArray` fonction JavaScript à l’aide `JS` de lorsqu’un bouton de composant ( **`Convert Array`** ) est sélectionné.
* Une fois la fonction JavaScript appelée, le tableau passé est converti en chaîne. La chaîne est retournée au composant pour l’affichage.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Pour utiliser l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adoptez l’une des approches suivantes :

* Injecter l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction dans le Razor composant ( `.razor` ) :

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  À l’intérieur `<head>` de l’élément de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), fournissez une `handleTickerChanged` fonction JavaScript. La fonction est appelée avec <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> et ne retourne pas de valeur :

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Injecter l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction dans une classe ( `.cs` ) :

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  À l’intérieur `<head>` de l’élément de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), fournissez une `handleTickerChanged` fonction JavaScript. La fonction est appelée avec `JS.InvokeAsync` et retourne une valeur :

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Pour la génération de contenu dynamique avec [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), utilisez l' `[Inject]` attribut :

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

Dans l’exemple d’application côté client qui accompagne cette rubrique, deux fonctions JavaScript sont disponibles pour l’application qui interagit avec le DOM pour recevoir l’entrée d’utilisateur et afficher un message d’accueil :

* `showPrompt`: Génère une invite pour accepter l’entrée d’utilisateur (nom de l’utilisateur) et retourne le nom à l’appelant.
* `displayWelcome`: Assigne un message de bienvenue de l’appelant à un objet DOM avec un `id` de `welcome` .

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Placez la `<script>` balise qui référence le fichier JavaScript dans le fichier `wwwroot/index.html` ( Blazor WebAssembly ) ou le `Pages/_Host.cshtml` fichier ( Blazor Server ).

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Ne placez pas de `<script>` balise dans un fichier de composant parce que la `<script>` balise ne peut pas être mise à jour dynamiquement.

Les méthodes .NET interagissent avec les fonctions JavaScript dans le `exampleJsInterop.js` fichier en appelant <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .

L' <xref:Microsoft.JSInterop.IJSRuntime> abstraction est asynchrone pour permettre les Blazor Server scénarios. Si l’application est une Blazor WebAssembly application et que vous souhaitez appeler une fonction JavaScript de manière synchrone, vous devez effectuer un cast aval vers <xref:Microsoft.JSInterop.IJSInProcessRuntime> et appeler à la <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> place. Nous recommandons que la plupart des bibliothèques d’interopérabilité JS utilisent les API Async pour s’assurer que les bibliothèques sont disponibles dans tous les scénarios.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Pour activer l’isolation JavaScript dans les [modules JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard, consultez la section [ Blazor isolation JavaScript et références d’objets](#blazor-javascript-isolation-and-object-references) .

::: moniker-end

L’exemple d’application comprend un composant pour illustrer l’interopérabilité de JS. Le composant :

* Reçoit une entrée d’utilisateur via une invite JavaScript.
* Retourne le texte du composant pour traitement.
* Appelle une deuxième fonction JavaScript qui interagit avec le DOM pour afficher un message d’accueil.

`Pages/JsInterop.razor`:

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JS

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JS.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JS.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).

1. Lorsque `TriggerJsPrompt` est exécuté en sélectionnant le bouton du composant **`Trigger JavaScript Prompt`** , la `showPrompt` fonction JavaScript fournie dans le `wwwroot/exampleJsInterop.js` fichier est appelée.
1. La `showPrompt` fonction accepte l’entrée d’utilisateur (nom de l’utilisateur), qui est codée au format HTML et renvoyée au composant. Le composant stocke le nom de l’utilisateur dans une variable locale, `name` .
1. La chaîne stockée dans `name` est incorporée dans un message de bienvenue, qui est transmis à une fonction JavaScript, `displayWelcome` , qui affiche le message de bienvenue dans une balise de titre.

## <a name="call-a-void-javascript-function"></a>Appeler une fonction JavaScript void

Utilisez <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> pour les éléments suivants :

* Fonctions JavaScript qui retournent [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [non défini](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).
* Si .NET n’est pas nécessaire pour lire le résultat d’un appel JavaScript.

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a>Détecter quand une Blazor Server application est prérendu
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Capturer des références à des éléments

Certains scénarios d’interopérabilité JS requièrent des références à des éléments HTML. Par exemple, une bibliothèque d’interface utilisateur peut nécessiter une référence d’élément pour l’initialisation, ou vous devrez peut-être appeler des API de type commande sur un élément, tel que `focus` ou `play` .

Capturer des références aux éléments HTML dans un composant à l’aide de l’approche suivante :

* Ajoutez un `@ref` attribut à l’élément HTML.
* Définissez un champ de type <xref:Microsoft.AspNetCore.Components.ElementReference> dont le nom correspond à la valeur de l' `@ref` attribut.

L’exemple suivant illustre la capture d’une référence à l' `username` `<input>` élément :

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Utilisez uniquement une référence d’élément pour muter le contenu d’un élément vide qui n’interagit pas avec Blazor . Ce scénario est utile quand une API tierce fournit du contenu à l’élément. Étant donné que Blazor n’interagit pas avec l’élément, il n’existe aucun risque de conflit entre Blazor la représentation de l’élément et le DOM.
>
> Dans l’exemple suivant, il est *dangereux* de faire muter le contenu de la liste non triée ( `ul` ), car Blazor interagit avec le DOM pour remplir les éléments de liste de cet élément ( `<li>` ) :
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> Si l’interopérabilité JS muter le contenu de l’élément `MyList` et Blazor tente d’appliquer des différences à l’élément, les différences ne correspondent pas au DOM.

Un <xref:Microsoft.AspNetCore.Components.ElementReference> est passé à du code JavaScript via l’interopérabilité js. Le code JavaScript reçoit une `HTMLElement` instance, qu’il peut utiliser avec les API DOM normales. Par exemple, le code suivant définit une méthode d’extension .NET qui permet d’envoyer un clic de souris à un élément :

`exampleJsInterop.js`:

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Utilisez [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) dans le code C# pour mettre le focus sur un élément, qui est intégré à l' Blazor infrastructure et fonctionne avec les références d’élément.

::: moniker-end

Pour appeler une fonction JavaScript qui ne retourne pas de valeur, utilisez <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> . Le code suivant déclenche un événement côté client `Click` en appelant la fonction JavaScript précédente avec le capturé <xref:Microsoft.AspNetCore.Components.ElementReference> :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

Pour utiliser une méthode d’extension, créez une méthode d’extension statique qui reçoit l' <xref:Microsoft.JSInterop.IJSRuntime> instance :

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

La `clickElement` méthode est appelée directement sur l’objet. L’exemple suivant suppose que la `TriggerClickEvent` méthode est disponible à partir de l' `JsInteropClasses` espace de noms :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> La `exampleButton` variable est remplie uniquement après le rendu du composant. Si un non rempli <xref:Microsoft.AspNetCore.Components.ElementReference> est passé à du code JavaScript, le code JavaScript reçoit la valeur `null` . Pour manipuler des références d’élément après que le composant a terminé le rendu, utilisez les [ `OnAfterRenderAsync` `OnAfterRender` méthodes de cycle de vie des composants ou](xref:blazor/components/lifecycle#after-component-render).

Lorsque vous utilisez des types génériques et que vous retournez une valeur, utilisez <xref:System.Threading.Tasks.ValueTask%601> :

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` est appelé directement sur l’objet avec un type. L’exemple suivant suppose que le `GenericMethod` est disponible à partir de l' `JsInteropClasses` espace de noms :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Éléments de référence sur les composants

Un <xref:Microsoft.AspNetCore.Components.ElementReference> ne peut pas être transmis entre les composants, car :

* L’instance ne peut exister qu’après le rendu du composant, c’est-à-dire pendant ou après l’exécution de la méthode d’un composant <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .
* Un <xref:Microsoft.AspNetCore.Components.ElementReference> est un [`struct`](/csharp/language-reference/builtin-types/struct) , qui ne peut pas être passé en tant que [paramètre de composant](xref:blazor/components/index#component-parameters).

Pour qu’un composant parent rende une référence d’élément disponible pour d’autres composants, le composant parent peut :

* Autorisez les composants enfants à inscrire des rappels.
* Appelez les rappels inscrits pendant l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> événement avec la référence d’élément passée. Indirectement, cette approche permet aux composants enfants d’interagir avec la référence d’élément du parent.

L' Blazor WebAssembly exemple suivant illustre l’approche.

Dans le `<head>` de `wwwroot/index.html` :

```html
<style>
    .red { color: red }
</style>
```

Dans le `<body>` de `wwwroot/index.html` :

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

`Pages/Index.razor` (composant parent) :

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

`Pages/Index.razor.cs`:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).

`Shared/SurveyPrompt.razor` (composant enfant) :

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

`Shared/SurveyPrompt.razor.cs`:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).

## <a name="harden-js-interop-calls"></a>Sécuriser les appels d’interopérabilité JS

L’interopérabilité de JS peut échouer en raison d’erreurs réseau et doit être considérée comme non fiable. Par défaut, une Blazor Server application expire les appels d’interopérabilité js sur le serveur après une minute. Si une application peut tolérer un délai d’expiration plus agressif, définissez le délai d’expiration à l’aide de l’une des approches suivantes :

* Globalement dans `Startup.ConfigureServices` , spécifiez le délai d’expiration :

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Par appel dans le code du composant, un appel unique peut spécifier le délai d’attente :

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Pour plus d’informations sur l’épuisement des ressources, consultez <xref:blazor/security/server/threat-mitigation> .

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Éviter les références d’objets circulaires

Les objets qui contiennent des références circulaires ne peuvent pas être sérialisés sur le client pour :

* Appels de méthode .NET.
* Appels de méthode JavaScript à partir de C# lorsque le type de retour a des références circulaires.

Pour plus d’informations, consultez les problèmes suivants :

* [Les références circulaires ne sont pas prises en charge, prennent deux (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposition : ajouter un mécanisme pour gérer les références circulaires lors de la sérialisation (dotnet/Runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor Isolation JavaScript et références d’objets

Blazor active l’isolation JavaScript dans les [modules JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard. L’isolation JavaScript offre les avantages suivants :

* Le JavaScript importé ne pollue plus l’espace de noms global.
* Les consommateurs d’une bibliothèque et de composants ne sont pas requis pour importer le code JavaScript associé.

Par exemple, le module JavaScript suivant exporte une fonction JavaScript pour afficher une invite de navigateur :

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

Ajoutez le module JavaScript précédent à une bibliothèque .NET en tant que ressource Web statique ( `wwwroot/exampleJsInterop.js` ), puis importez le module dans le code .net à l’aide du <xref:Microsoft.JSInterop.IJSRuntime> service. Le service est injecté comme `js` (non affiché) pour l’exemple suivant :

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

L' `import` identificateur dans l’exemple précédent est un identificateur spécial utilisé spécifiquement pour l’importation d’un module JavaScript. Spécifiez le module à l’aide de son chemin d’accès aux ressources Web statiques stable : `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` . L’espace réservé `{LIBRARY NAME}` est le nom de la bibliothèque. L’espace réservé `{PATH UNDER WWWROOT}` est le chemin d’accès au script sous `wwwroot` .

<xref:Microsoft.JSInterop.IJSRuntime> importe le module en tant que `IJSObjectReference` , qui représente une référence à un objet JavaScript à partir du code .net. Utilisez `IJSObjectReference` pour appeler les fonctions JavaScript exportées à partir du module :

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

`IJSInProcessObjectReference` représente une référence à un objet JavaScript dont les fonctions peuvent être appelées de façon synchrone.

`IJSUnmarshalledObjectReference` représente une référence à un objet JavaScript dont les fonctions peuvent être appelées sans la surcharge liée à la sérialisation des données .NET. Cela peut être utilisé dans Blazor WebAssembly lorsque les performances sont cruciales :

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = Blazor.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)js;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

Dans l’exemple précédent, le <xref:Microsoft.JSInterop.IJSRuntime> service est injecté dans la classe et assigné à `js` (non affiché).

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a>Utilisation de bibliothèques JavaScript qui affichent l’interface utilisateur (éléments DOM)

Parfois, vous souhaiterez peut-être utiliser des bibliothèques JavaScript qui produisent des éléments d’interface utilisateur visibles dans le DOM du navigateur. À première vue, cela peut paraître difficile, car la Blazor différenciation du système repose sur le contrôle de l’arborescence des éléments DOM et s’exécute en erreurs si un code externe fait muter l’arborescence DOM et invalide son mécanisme pour l’application de différences. Il ne s’agit pas d’une Blazor limitation spécifique. Le même défi se produit avec toute infrastructure d’interface utilisateur basée sur des différences.

Heureusement, il est facile d’incorporer de manière fiable une interface utilisateur générée en externe dans une Blazor interface utilisateur de composant. La technique recommandée consiste à faire en sorte que le code ( `.razor` fichier) du composant produise un élément vide. En ce qui concerne Blazor le système de différenciation, l’élément est toujours vide, de sorte que le convertisseur ne parcourt pas de manière récursive l’élément et laisse son contenu seul. Cela permet de remplir en toute sécurité l’élément avec un contenu arbitraire géré en externe.

L’exemple suivant illustre le concept. Dans l' `if` instruction, lorsque a la valeur `firstRender` `true` , effectuez une opération avec `myElement` . Par exemple, appelez une bibliothèque JavaScript externe pour la remplir. Blazor conserve le contenu de l’élément seul jusqu’à ce que ce composant lui-même soit supprimé. Lorsque le composant est supprimé, la sous-arborescence DOM entière du composant est également supprimée.

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

En guise d’exemple plus détaillé, considérez le composant suivant qui restitue une carte interactive à l’aide des [API Mapbox Open source](https://www.mapbox.com/):

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

Le module JavaScript correspondant, qui doit être placé à l’adresse `wwwroot/mapComponent.js` , est le suivant :

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

Dans l’exemple précédent, remplacez la chaîne `{ACCESS TOKEN}` par un jeton d’accès valide à partir duquel vous pouvez obtenir https://account.mapbox.com .

Pour produire un style correct, ajoutez la balise de feuille de style suivante à la page HTML de l’hôte ( `index.html` ou `_Host.cshtml` ) :

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

L’exemple précédent produit une interface utilisateur de carte interactive, dans laquelle l’utilisateur :

* Peut faire glisser pour faire défiler ou zoomer.
* Cliquez sur les boutons pour accéder aux emplacements prédéfinis.

![Mapbox Street Map de Tokyo, Japon et des boutons permettant de sélectionner les Bristol, le Royaume-Uni et Tokyo, le Japon](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

Les points clés à comprendre sont les suivants :

 * Le `<div>` avec `@ref="mapElement"` est laissé vide en ce qui concerne le Blazor . C’est pourquoi il est possible de le `mapbox-gl.js` remplir et de modifier son contenu au fil du temps. Vous pouvez utiliser cette technique avec n’importe quelle bibliothèque JavaScript qui restitue l’interface utilisateur. Vous pouvez même incorporer des composants d’une infrastructure SPA JavaScript tierce dans des Blazor composants, à condition qu’ils ne tentent pas d’atteindre et de modifier d’autres parties de la page. Il n’est *pas* possible pour le code JavaScript externe de modifier des éléments qui Blazor ne sont pas pris en compte comme vides.
 * Lorsque vous utilisez cette approche, gardez à l’esprit les règles de Blazor conservation ou de destruction des éléments DOM. Dans l’exemple précédent, le composant gère en toute sécurité les événements de clic de bouton et met à jour l’instance de mappage existante, car les éléments DOM sont conservés dans la mesure du possible par défaut. Si vous avez rendu une liste d’éléments cartographiques à partir d’une `@foreach` boucle, vous souhaitez utiliser `@key` pour garantir la préservation des instances de composant. Dans le cas contraire, les modifications apportées aux données de la liste peuvent entraîner la conservation de l’état des instances précédentes par les instances de composant de manière indésirable. Pour plus d’informations, consultez [utilisation @key de pour conserver les éléments et les composants](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).

En outre, l’exemple précédent montre comment il est possible d’encapsuler la logique JavaScript et les dépendances dans un module ES6 et de le charger dynamiquement à l’aide de l' `import` identificateur. Pour plus d’informations, consultez la rubrique [isolation JavaScript et références d’objets](#blazor-javascript-isolation-and-object-references).

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a>Limites de taille sur les appels d’interopérabilité JS

Dans Blazor WebAssembly , l’infrastructure n’impose pas de limites sur la taille des entrées et des sorties des appels d’interopérabilité js.

Dans Blazor Server , le résultat d’un appel d’interopérabilité js est limité par la taille maximale de charge utile appliquée par SignalR ( <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> ), qui est par défaut de 32 Ko. Les applications qui tentent de répondre à un appel d’interopérabilité JS avec une charge utile supérieure à <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> génèrent une erreur. Une limite plus grande peut être configurée en modifiant <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> . L’exemple suivant définit la taille maximale des messages de réception sur 64 Ko (64 * 1024 * 1024) :

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

L’augmentation de la SignalR limite survient au détriment de l’utilisation de ressources serveur supplémentaires et expose le serveur à des risques accrus d’un utilisateur malveillant. En outre, la lecture d’une grande quantité de contenu dans la mémoire en tant que chaînes ou tableaux d’octets peut également entraîner des allocations qui fonctionnent mal avec le garbage collector, ce qui entraîne des pénalités en matière de performances. L’une des options de lecture des charges utiles volumineuses consiste à entourer le contenu de plus petits segments et à traiter la charge utile sous la forme d’un <xref:System.IO.Stream> . Cela peut être utilisé lors de la lecture de charges utiles JSON volumineuses ou si les données sont disponibles en tant qu’octets bruts dans JavaScript. Pour obtenir un exemple qui montre comment envoyer des charges utiles binaires volumineuses dans Blazor Server qui utilise des techniques similaires au `InputFile` composant, consultez l' [exemple d’application de soumission binaire](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).

Tenez compte des conseils suivants lors du développement de code qui transfère une grande quantité de données entre JavaScript et Blazor :

* Découpez les données en éléments plus petits et envoyez les segments de données de façon séquentielle jusqu’à ce que toutes les données soient reçues par le serveur.
* N’allouez pas d’objets volumineux dans du code JavaScript et C#.
* Ne bloquez pas le thread d’interface utilisateur principal pendant de longues périodes lors de l’envoi ou de la réception de données.
* Libérez la mémoire consommée lorsque le processus est terminé ou annulé.
* Appliquer les exigences supplémentaires suivantes pour des raisons de sécurité :
  * Déclarez la taille maximale du fichier ou des données qui peut être passée.
  * Déclarez le taux de téléchargement minimal du client au serveur.
* Une fois les données reçues par le serveur, les données peuvent être :
  * Stocké temporairement dans une mémoire tampon jusqu’à ce que tous les segments soient collectés.
  * Consommé immédiatement. Par exemple, les données peuvent être stockées immédiatement dans une base de données ou écrites sur le disque à mesure que chaque segment est reçu.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/call-dotnet-from-javascript>
* [Exemple InteropComponent. Razor (référentiel GitHub dotnet/AspNetCore, branche de version 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
