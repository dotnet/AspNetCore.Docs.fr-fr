---
title: 'Appeler des méthodes .NET à partir de fonctions JavaScript dans ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Découvrez comment appeler des méthodes .NET à partir de fonctions JavaScript dans des :::no-loc(Blazor)::: applications.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: c4ab82490a2b16cdb5fef691cfb1ca038f1dca00
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570066"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="12520-103">Appeler des méthodes .NET à partir de fonctions JavaScript dans ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="12520-103">Call .NET methods from JavaScript functions in ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="12520-104">Par [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="12520-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="12520-105">Une :::no-loc(Blazor)::: application peut appeler des fonctions JavaScript à partir de méthodes .net et de méthodes .net à partir de fonctions JavaScript.</span><span class="sxs-lookup"><span data-stu-id="12520-105">A :::no-loc(Blazor)::: app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="12520-106">Ces scénarios portent le nom de *l’interopérabilité de JavaScript* ( *js Interop* ).</span><span class="sxs-lookup"><span data-stu-id="12520-106">These scenarios are called *JavaScript interoperability* ( *JS interop* ).</span></span>

<span data-ttu-id="12520-107">Cet article traite de l’appel de méthodes .NET à partir de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="12520-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="12520-108">Pour plus d’informations sur l’appel de fonctions JavaScript à partir de .NET, consultez <xref:blazor/call-javascript-from-dotnet> .</span><span class="sxs-lookup"><span data-stu-id="12520-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="12520-109">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="12520-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="12520-110">Appel de méthode .NET statique</span><span class="sxs-lookup"><span data-stu-id="12520-110">Static .NET method call</span></span>

<span data-ttu-id="12520-111">Pour appeler une méthode .NET statique à partir de JavaScript, utilisez les `DotNet.invokeMethod` `DotNet.invokeMethodAsync` fonctions ou.</span><span class="sxs-lookup"><span data-stu-id="12520-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="12520-112">Transmettez l’identificateur de la méthode statique que vous souhaitez appeler, le nom de l’assembly contenant la fonction et les arguments éventuels.</span><span class="sxs-lookup"><span data-stu-id="12520-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="12520-113">La version asynchrone est recommandée pour prendre en charge les :::no-loc(Blazor Server)::: scénarios.</span><span class="sxs-lookup"><span data-stu-id="12520-113">The asynchronous version is preferred to support :::no-loc(Blazor Server)::: scenarios.</span></span> <span data-ttu-id="12520-114">La méthode .NET doit être publique, statique et avoir l' [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="12520-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="12520-115">L’appel de méthodes génériques ouvertes n’est pas pris en charge actuellement.</span><span class="sxs-lookup"><span data-stu-id="12520-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="12520-116">L’exemple d’application comprend une méthode C# pour retourner un `int` tableau.</span><span class="sxs-lookup"><span data-stu-id="12520-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="12520-117">L' [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribut est appliqué à la méthode.</span><span class="sxs-lookup"><span data-stu-id="12520-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="12520-118">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="12520-118">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="12520-119">JavaScript traité au client appelle la méthode .NET C#.</span><span class="sxs-lookup"><span data-stu-id="12520-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="12520-120">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="12520-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="12520-121">Lorsque le **`Trigger .NET static method ReturnArrayAsync`** bouton est sélectionné, examinez la sortie de la console dans les outils de développement Web du navigateur.</span><span class="sxs-lookup"><span data-stu-id="12520-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="12520-122">La sortie de la console est la suivante :</span><span class="sxs-lookup"><span data-stu-id="12520-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="12520-123">La quatrième valeur de tableau fait l’objet d’un push dans le tableau ( `data.push(4);` ) retourné par `ReturnArrayAsync` .</span><span class="sxs-lookup"><span data-stu-id="12520-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="12520-124">Par défaut, l’identificateur de méthode est le nom de la méthode, mais vous pouvez spécifier un identificateur différent à l’aide du [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) constructeur d’attribut :</span><span class="sxs-lookup"><span data-stu-id="12520-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="12520-125">Dans le fichier JavaScript côté client :</span><span class="sxs-lookup"><span data-stu-id="12520-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="12520-126">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="12520-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="12520-127">Appel de méthode d’instance</span><span class="sxs-lookup"><span data-stu-id="12520-127">Instance method call</span></span>

<span data-ttu-id="12520-128">Vous pouvez également appeler des méthodes d’instance .NET à partir de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="12520-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="12520-129">Pour appeler une méthode d’instance .NET à partir de JavaScript :</span><span class="sxs-lookup"><span data-stu-id="12520-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="12520-130">Passer l’instance .NET par référence à JavaScript :</span><span class="sxs-lookup"><span data-stu-id="12520-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="12520-131">Effectuez un appel statique à <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="12520-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="12520-132">Encapsulez l’instance dans une <xref:Microsoft.JSInterop.DotNetObjectReference> instance et appelez <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> sur l' <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span><span class="sxs-lookup"><span data-stu-id="12520-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="12520-133">Supprimez des <xref:Microsoft.JSInterop.DotNetObjectReference> objets (un exemple apparaît plus loin dans cette section).</span><span class="sxs-lookup"><span data-stu-id="12520-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="12520-134">Appeler des méthodes d’instance .NET sur l’instance à l’aide des `invokeMethod` `invokeMethodAsync` fonctions ou.</span><span class="sxs-lookup"><span data-stu-id="12520-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="12520-135">L’instance .NET peut également être passée comme argument lors de l’appel d’autres méthodes .NET à partir de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="12520-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="12520-136">L’exemple d’application enregistre les messages dans la console côté client.</span><span class="sxs-lookup"><span data-stu-id="12520-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="12520-137">Pour les exemples suivants présentés dans l’exemple d’application, examinez la sortie de console du navigateur dans les outils de développement du navigateur.</span><span class="sxs-lookup"><span data-stu-id="12520-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="12520-138">Lorsque le **`Trigger .NET instance method HelloHelper.SayHello`** bouton est sélectionné, `ExampleJsInterop.CallHelloHelperSayHello` est appelé et passe un nom, `:::no-loc(Blazor):::` , à la méthode.</span><span class="sxs-lookup"><span data-stu-id="12520-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `:::no-loc(Blazor):::`, to the method.</span></span>

<span data-ttu-id="12520-139">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="12520-139">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello(":::no-loc(Blazor):::");
    }
}
```

<span data-ttu-id="12520-140">`CallHelloHelperSayHello` appelle la fonction JavaScript `sayHello` avec une nouvelle instance de `HelloHelper` .</span><span class="sxs-lookup"><span data-stu-id="12520-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="12520-141">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="12520-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="12520-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="12520-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="12520-143">Le nom est passé au `HelloHelper` constructeur de, qui définit la `HelloHelper.Name` propriété.</span><span class="sxs-lookup"><span data-stu-id="12520-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="12520-144">Lorsque la fonction JavaScript `sayHello` est exécutée, `HelloHelper.SayHello` retourne le `Hello, {Name}!` message, qui est écrit dans la console par la fonction JavaScript.</span><span class="sxs-lookup"><span data-stu-id="12520-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="12520-145">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="12520-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="12520-146">Sortie de la console dans les outils de développement Web du navigateur :</span><span class="sxs-lookup"><span data-stu-id="12520-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, :::no-loc(Blazor):::!
```

<span data-ttu-id="12520-147">Pour éviter une fuite de mémoire et autoriser garbage collection sur un composant qui crée un <xref:Microsoft.JSInterop.DotNetObjectReference> , adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="12520-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="12520-148">Supprimez l’objet dans la classe qui a créé l' <xref:Microsoft.JSInterop.DotNetObjectReference> instance :</span><span class="sxs-lookup"><span data-stu-id="12520-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

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

  <span data-ttu-id="12520-149">Le modèle précédent présenté dans la `ExampleJsInterop` classe peut également être implémenté dans un composant :</span><span class="sxs-lookup"><span data-stu-id="12520-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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
          objRef = DotNetObjectReference.Create(new HelloHelper(":::no-loc(Blazor):::"));

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
  
  <span data-ttu-id="12520-150">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="12520-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

* <span data-ttu-id="12520-151">Lorsque le composant ou la classe ne supprime pas le <xref:Microsoft.JSInterop.DotNetObjectReference> , supprimez l’objet sur le client en appelant `.dispose()` :</span><span class="sxs-lookup"><span data-stu-id="12520-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="12520-152">Appel de méthode d’instance de composant</span><span class="sxs-lookup"><span data-stu-id="12520-152">Component instance method call</span></span>

<span data-ttu-id="12520-153">Pour appeler les méthodes .NET d’un composant :</span><span class="sxs-lookup"><span data-stu-id="12520-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="12520-154">Utilisez la `invokeMethod` `invokeMethodAsync` fonction ou pour effectuer un appel de méthode statique au composant.</span><span class="sxs-lookup"><span data-stu-id="12520-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="12520-155">La méthode statique du composant encapsule l’appel à sa méthode d’instance en tant que appelé <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="12520-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="12520-156">Pour les :::no-loc(Blazor Server)::: applications, où plusieurs utilisateurs peuvent utiliser le même composant simultanément, utilisez une classe d’assistance pour appeler des méthodes d’instance.</span><span class="sxs-lookup"><span data-stu-id="12520-156">For :::no-loc(Blazor Server)::: apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="12520-157">Pour plus d’informations, consultez la section de la [classe d’assistance de la méthode d’instance de composant](#component-instance-method-helper-class) .</span><span class="sxs-lookup"><span data-stu-id="12520-157">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="12520-158">Dans le code JavaScript côté client :</span><span class="sxs-lookup"><span data-stu-id="12520-158">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="12520-159">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="12520-159">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="12520-160">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="12520-160">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="12520-161">Pour passer des arguments à la méthode d’instance :</span><span class="sxs-lookup"><span data-stu-id="12520-161">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="12520-162">Ajoutez des paramètres à l’appel de méthode JS.</span><span class="sxs-lookup"><span data-stu-id="12520-162">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="12520-163">Dans l’exemple suivant, un nom est passé à la méthode.</span><span class="sxs-lookup"><span data-stu-id="12520-163">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="12520-164">Des paramètres supplémentaires peuvent être ajoutés à la liste en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="12520-164">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="12520-165">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="12520-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

* <span data-ttu-id="12520-166">Fournissez les types corrects au <xref:System.Action> pour les paramètres.</span><span class="sxs-lookup"><span data-stu-id="12520-166">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="12520-167">Fournissez la liste de paramètres aux méthodes C#.</span><span class="sxs-lookup"><span data-stu-id="12520-167">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="12520-168">Appelez <xref:System.Action> ( `UpdateMessage` ) avec les paramètres ( `action.Invoke(name)` ).</span><span class="sxs-lookup"><span data-stu-id="12520-168">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="12520-169">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="12520-169">`Pages/JSInteropComponent.razor`:</span></span>

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

  <span data-ttu-id="12520-170">Sortie `message` lorsque le bouton **Call js Method** est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="12520-170">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="12520-171">Classe d’assistance de méthode d’instance de composant</span><span class="sxs-lookup"><span data-stu-id="12520-171">Component instance method helper class</span></span>

<span data-ttu-id="12520-172">La classe d’assistance est utilisée pour appeler une méthode d’instance en tant que <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="12520-172">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="12520-173">Les classes d’assistance sont utiles dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="12520-173">Helper classes are useful when:</span></span>

* <span data-ttu-id="12520-174">Plusieurs composants du même type sont rendus sur la même page.</span><span class="sxs-lookup"><span data-stu-id="12520-174">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="12520-175">Une :::no-loc(Blazor Server)::: application est utilisée, où plusieurs utilisateurs peuvent utiliser un composant simultanément.</span><span class="sxs-lookup"><span data-stu-id="12520-175">A :::no-loc(Blazor Server)::: app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="12520-176">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="12520-176">In the following example:</span></span>

* <span data-ttu-id="12520-177">Le `JSInteropExample` composant contient plusieurs `ListItem` composants.</span><span class="sxs-lookup"><span data-stu-id="12520-177">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="12520-178">Chaque `ListItem` composant se compose d’un message et d’un bouton.</span><span class="sxs-lookup"><span data-stu-id="12520-178">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="12520-179">Quand un `ListItem` bouton de composant est sélectionné, `ListItem` la `UpdateMessage` méthode modifie le texte de l’élément de liste et masque le bouton.</span><span class="sxs-lookup"><span data-stu-id="12520-179">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="12520-180">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="12520-180">`MessageUpdateInvokeHelper.cs`:</span></span>

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

<span data-ttu-id="12520-181">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="12520-181">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="12520-182">Dans le code JavaScript côté client :</span><span class="sxs-lookup"><span data-stu-id="12520-182">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="12520-183">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="12520-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="12520-184">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="12520-184">`Shared/ListItem.razor`:</span></span>

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

<span data-ttu-id="12520-185">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="12520-185">`Pages/JSInteropExample.razor`:</span></span>

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

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="12520-186">Éviter les références d’objets circulaires</span><span class="sxs-lookup"><span data-stu-id="12520-186">Avoid circular object references</span></span>

<span data-ttu-id="12520-187">Les objets qui contiennent des références circulaires ne peuvent pas être sérialisés sur le client pour :</span><span class="sxs-lookup"><span data-stu-id="12520-187">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="12520-188">Appels de méthode .NET.</span><span class="sxs-lookup"><span data-stu-id="12520-188">.NET method calls.</span></span>
* <span data-ttu-id="12520-189">Appels de méthode JavaScript à partir de C# lorsque le type de retour a des références circulaires.</span><span class="sxs-lookup"><span data-stu-id="12520-189">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="12520-190">Pour plus d’informations, consultez les problèmes suivants :</span><span class="sxs-lookup"><span data-stu-id="12520-190">For more information, see the following issues:</span></span>

* [<span data-ttu-id="12520-191">Les références circulaires ne sont pas prises en charge, prennent deux (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="12520-191">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="12520-192">Proposition : ajouter un mécanisme pour gérer les références circulaires lors de la sérialisation (dotnet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="12520-192">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="js-modules"></a><span data-ttu-id="12520-193">Modules JS</span><span class="sxs-lookup"><span data-stu-id="12520-193">JS modules</span></span>

<span data-ttu-id="12520-194">Pour l’isolation de JS, l’interopérabilité de JS fonctionne avec la prise en charge par défaut du navigateur pour les [modules ECMAScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([spécification ECMAScript](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="12520-194">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12520-195">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="12520-195">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="12520-196">`InteropComponent.razor` exemple (référentiel GitHub dotnet/AspNetCore, branche de version 3,1)</span><span class="sxs-lookup"><span data-stu-id="12520-196">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
