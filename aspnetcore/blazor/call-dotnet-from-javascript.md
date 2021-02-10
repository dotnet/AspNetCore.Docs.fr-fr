---
title: Appeler des méthodes .NET à partir de fonctions JavaScript dans ASP.NET Core Blazor
author: guardrex
description: Découvrez comment appeler des méthodes .NET à partir de fonctions JavaScript dans des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 645bbb33b5a0a43e630095e375526b0686f86277
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106581"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a>Appeler des méthodes .NET à partir de fonctions JavaScript dans ASP.NET Core Blazor

Par [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)et [Luke Latham](https://github.com/guardrex)

Une Blazor application peut appeler des fonctions JavaScript à partir de méthodes .net et de méthodes .net à partir de fonctions JavaScript. Ces scénarios portent le nom de *l’interopérabilité de JavaScript* (*js Interop*).

Cet article traite de l’appel de méthodes .NET à partir de JavaScript. Pour plus d’informations sur l’appel de fonctions JavaScript à partir de .NET, consultez <xref:blazor/call-javascript-from-dotnet> .

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

> [!NOTE]
> Ajoutez des fichiers JS ( `<script>` balises) avant la `</body>` balise de fermeture dans le fichier `wwwroot/index.html` ( Blazor WebAssembly ) ou le `Pages/_Host.cshtml` fichier ( Blazor Server ). Assurez-vous que les fichiers JS avec les méthodes d’interopérabilité JS sont inclus avant les Blazor fichiers Framework js.

## <a name="static-net-method-call"></a>Appel de méthode .NET statique

Pour appeler une méthode .NET statique à partir de JavaScript, utilisez les `DotNet.invokeMethod` `DotNet.invokeMethodAsync` fonctions ou. Transmettez l’identificateur de la méthode statique que vous souhaitez appeler, le nom de l’assembly contenant la fonction et les arguments éventuels. La version asynchrone est recommandée pour prendre en charge les Blazor Server scénarios. La méthode .NET doit être publique, statique et avoir l' [ `[JSInvokable]` attribut](xref:Microsoft.JSInterop.JSInvokableAttribute). L’appel de méthodes génériques ouvertes n’est pas pris en charge actuellement.

L’exemple d’application comprend une méthode C# pour retourner un `int` tableau. L' [ `[JSInvokable]` attribut](xref:Microsoft.JSInterop.JSInvokableAttribute) est appliqué à la méthode.

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

JavaScript traité au client appelle la méthode .NET C#.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Lorsque le **`Trigger .NET static method ReturnArrayAsync`** bouton est sélectionné, examinez la sortie de la console dans les outils de développement Web du navigateur.

La sortie de la console est la suivante :

```console
Array(4) [ 1, 2, 3, 4 ]
```

La quatrième valeur de tableau fait l’objet d’un push dans le tableau ( `data.push(4);` ) retourné par `ReturnArrayAsync` .

Par défaut, l’identificateur de méthode est le nom de la méthode, mais vous pouvez spécifier un identificateur différent à l’aide du constructeur d' [ `[JSInvokable]` attribut](xref:Microsoft.JSInterop.JSInvokableAttribute) :

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

Dans le fichier JavaScript côté client :

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).

## <a name="instance-method-call"></a>Appel de méthode d’instance

Vous pouvez également appeler des méthodes d’instance .NET à partir de JavaScript. Pour appeler une méthode d’instance .NET à partir de JavaScript :

* Passer l’instance .NET par référence à JavaScript :
  * Effectuez un appel statique à <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .
  * Encapsulez l’instance dans une <xref:Microsoft.JSInterop.DotNetObjectReference> instance et appelez <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> sur l' <xref:Microsoft.JSInterop.DotNetObjectReference> instance. Supprimez des <xref:Microsoft.JSInterop.DotNetObjectReference> objets (un exemple apparaît plus loin dans cette section).
* Appeler des méthodes d’instance .NET sur l’instance à l’aide des `invokeMethod` `invokeMethodAsync` fonctions ou. L’instance .NET peut également être passée comme argument lors de l’appel d’autres méthodes .NET à partir de JavaScript.

> [!NOTE]
> L’exemple d’application enregistre les messages dans la console côté client. Pour les exemples suivants présentés dans l’exemple d’application, examinez la sortie de console du navigateur dans les outils de développement du navigateur.

Lorsque le **`Trigger .NET instance method HelloHelper.SayHello`** bouton est sélectionné, `ExampleJsInterop.CallHelloHelperSayHello` est appelé et passe un nom, `Blazor` , à la méthode.

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello` appelle la fonction JavaScript `sayHello` avec une nouvelle instance de `HelloHelper` .

`JsInteropClasses/ExampleJsInterop.cs`:

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Le nom est passé au `HelloHelper` constructeur de, qui définit la `HelloHelper.Name` propriété. Lorsque la fonction JavaScript `sayHello` est exécutée, `HelloHelper.SayHello` retourne le `Hello, {Name}!` message, qui est écrit dans la console par la fonction JavaScript.

`JsInteropClasses/HelloHelper.cs`:

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Sortie de la console dans les outils de développement Web du navigateur :

```console
Hello, Blazor!
```

Pour éviter une fuite de mémoire et autoriser garbage collection sur un composant qui crée un <xref:Microsoft.JSInterop.DotNetObjectReference> , adoptez l’une des approches suivantes :

* Supprimez l’objet dans la classe qui a créé l' <xref:Microsoft.JSInterop.DotNetObjectReference> instance :

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  Le modèle précédent présenté dans la `ExampleJsInterop` classe peut également être implémenté dans un composant :

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).

* Lorsque le composant ou la classe ne supprime pas le <xref:Microsoft.JSInterop.DotNetObjectReference> , supprimez l’objet sur le client en appelant `.dispose()` :

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Appel de méthode d’instance de composant

Pour appeler les méthodes .NET d’un composant :

* Utilisez la `invokeMethod` `invokeMethodAsync` fonction ou pour effectuer un appel de méthode statique au composant.
* La méthode statique du composant encapsule l’appel à sa méthode d’instance en tant que appelé <xref:System.Action> .

> [!NOTE]
> Pour les Blazor Server applications, où plusieurs utilisateurs peuvent utiliser le même composant simultanément, utilisez une classe d’assistance pour appeler des méthodes d’instance.
>
> Pour plus d’informations, consultez la section de la [classe d’assistance de la méthode d’instance de composant](#component-instance-method-helper-class) .

Dans le code JavaScript côté client :

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).

`Pages/JSInteropComponent.razor`:

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

Pour passer des arguments à la méthode d’instance :

* Ajoutez des paramètres à l’appel de méthode JS. Dans l’exemple suivant, un nom est passé à la méthode. Des paramètres supplémentaires peuvent être ajoutés à la liste en fonction des besoins.

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).

* Fournissez les types corrects au <xref:System.Action> pour les paramètres. Fournissez la liste de paramètres aux méthodes C#. Appelez <xref:System.Action> ( `UpdateMessage` ) avec les paramètres ( `action.Invoke(name)` ).

  `Pages/JSInteropComponent.razor`:

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  Sortie `message` lorsque le bouton **Call js Method** est sélectionné :

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a>Classe d’assistance de méthode d’instance de composant

La classe d’assistance est utilisée pour appeler une méthode d’instance en tant que <xref:System.Action> . Les classes d’assistance sont utiles dans les cas suivants :

* Plusieurs composants du même type sont rendus sur la même page.
* Une Blazor Server application est utilisée, où plusieurs utilisateurs peuvent utiliser un composant simultanément.

Dans l’exemple suivant :

* Le `JSInteropExample` composant contient plusieurs `ListItem` composants.
* Chaque `ListItem` composant se compose d’un message et d’un bouton.
* Quand un `ListItem` bouton de composant est sélectionné, `ListItem` la `UpdateMessage` méthode modifie le texte de l’élément de liste et masque le bouton.

`MessageUpdateInvokeHelper.cs`:

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).

Dans le code JavaScript côté client :

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).

`Shared/ListItem.razor`:

```razor
@inject IJSRuntime JS

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JS.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

`Pages/JSInteropExample.razor`:

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Éviter les références d’objets circulaires

Les objets qui contiennent des références circulaires ne peuvent pas être sérialisés sur le client pour :

* Appels de méthode .NET.
* Appels de méthode JavaScript à partir de C# lorsque le type de retour a des références circulaires.

Pour plus d’informations, consultez les problèmes suivants :

* [Les références circulaires ne sont pas prises en charge, prennent deux (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposition : ajouter un mécanisme pour gérer les références circulaires lors de la sérialisation (dotnet/Runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="size-limits-on-js-interop-calls"></a>Limites de taille sur les appels d’interopérabilité JS

Dans Blazor WebAssembly , l’infrastructure n’impose pas de limite sur la taille des entrées et sorties d’interopérabilité js.

Dans Blazor Server , les appels d’interopérabilité js ont une taille limitée par la SignalR taille maximale autorisée pour les méthodes de concentrateur, qui est appliquée par <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (valeur par défaut : 32 Ko). JS en SignalR messages .net plus grands que ne <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> génèrent une erreur. L’infrastructure n’impose pas de limite sur la taille d’un SignalR message du concentrateur à un client.

Lorsque SignalR la journalisation n’est pas définie sur [Debug](xref:Microsoft.Extensions.Logging.LogLevel) ou [trace](xref:Microsoft.Extensions.Logging.LogLevel), une erreur de taille de message s’affiche uniquement dans la console outils de développement du navigateur :

> Erreur : connexion déconnectée avec l’erreur’erreur : le serveur a retourné une erreur lors de la fermeture : la connexion a été fermée avec une erreur. '.

Lorsque la [ SignalR journalisation côté serveur](xref:signalr/diagnostics#server-side-logging) est définie sur [Debug](xref:Microsoft.Extensions.Logging.LogLevel) ou [trace](xref:Microsoft.Extensions.Logging.LogLevel), la journalisation côté serveur couvre un <xref:System.IO.InvalidDataException> pour une erreur de taille de message.

`appsettings.Development.json`:

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> System. IO. InvalidDataException : la taille de message maximale de 32768B a été dépassée. La taille du message peut être configurée dans AddHubOptions.

Augmentez la limite en définissant <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> dans `Startup.ConfigureServices` . L’exemple suivant définit la taille maximale des messages de réception sur 64 Ko (64 * 1024) :

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

Si vous augmentez la SignalR limite de taille des messages entrants, vous avez besoin de davantage de ressources serveur et cela expose le serveur à des risques accrus d’un utilisateur malveillant. En outre, la lecture d’une grande quantité de contenu dans la mémoire en tant que chaînes ou tableaux d’octets peut également entraîner des allocations qui fonctionnent mal avec le garbage collector, ce qui entraîne des pénalités en matière de performances.

L’une des options de lecture des charges utiles volumineuses consiste à envoyer le contenu en plus petits segments et à traiter la charge utile en tant que <xref:System.IO.Stream> . Cela peut être utilisé lors de la lecture de charges utiles JSON volumineuses ou si les données sont disponibles en tant qu’octets bruts dans JavaScript. Pour obtenir un exemple qui montre comment envoyer des charges utiles binaires volumineuses dans Blazor Server qui utilise des techniques similaires au `InputFile` composant, consultez l' [exemple d’application de soumission binaire](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).

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

## <a name="js-modules"></a>Modules JS

Pour l’isolation de JS, l’interopérabilité de JS fonctionne avec la prise en charge par défaut du navigateur pour les [modules ECMAScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([spécification ECMAScript](https://tc39.es/ecma262/#sec-modules)).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/call-javascript-from-dotnet>
* [`InteropComponent.razor` exemple (référentiel GitHub dotnet/AspNetCore, branche de version 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
