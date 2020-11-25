---
title: Appeler des fonctions JavaScript à partir de méthodes .NET dans ASP.NET Core Blazor
author: guardrex
description: Découvrez comment appeler des fonctions JavaScript à partir de méthodes .NET dans des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/25/2020
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
ms.openlocfilehash: c73de0e30b7b564915f30d75f754f89fecccdc78
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035721"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="847d0-103">Appeler des fonctions JavaScript à partir de méthodes .NET dans ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="847d0-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="847d0-104">Par [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Pranav Krishnamoorthy](https://github.com/pranavkm)et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="847d0-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Pranav Krishnamoorthy](https://github.com/pranavkm), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="847d0-105">Une Blazor application peut appeler des fonctions JavaScript à partir de méthodes .net et de méthodes .net à partir de fonctions JavaScript.</span><span class="sxs-lookup"><span data-stu-id="847d0-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="847d0-106">Ces scénarios portent le nom de *l’interopérabilité de JavaScript* (*js Interop*).</span><span class="sxs-lookup"><span data-stu-id="847d0-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="847d0-107">Cet article traite de l’appel de fonctions JavaScript à partir de .NET.</span><span class="sxs-lookup"><span data-stu-id="847d0-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="847d0-108">Pour plus d’informations sur la façon d’appeler des méthodes .NET à partir de JavaScript, consultez <xref:blazor/call-dotnet-from-javascript> .</span><span class="sxs-lookup"><span data-stu-id="847d0-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="847d0-109">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="847d0-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="847d0-110">Pour appeler JavaScript à partir de .NET, utilisez l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span><span class="sxs-lookup"><span data-stu-id="847d0-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="847d0-111">Pour émettre des appels d’interopérabilité JS, injectez l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction dans votre composant.</span><span class="sxs-lookup"><span data-stu-id="847d0-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="847d0-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> prend un identificateur pour la fonction JavaScript que vous souhaitez appeler, ainsi que n’importe quel nombre d’arguments sérialisables JSON.</span><span class="sxs-lookup"><span data-stu-id="847d0-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="847d0-113">L’identificateur de fonction est relatif à la portée globale ( `window` ).</span><span class="sxs-lookup"><span data-stu-id="847d0-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="847d0-114">Si vous souhaitez appeler `window.someScope.someFunction` , l’identificateur est `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="847d0-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="847d0-115">Il n’est pas nécessaire d’inscrire la fonction avant qu’elle ne soit appelée.</span><span class="sxs-lookup"><span data-stu-id="847d0-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="847d0-116">Le type de retour `T` doit également être sérialisable JSON.</span><span class="sxs-lookup"><span data-stu-id="847d0-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="847d0-117">`T` doit correspondre au type .NET qui correspond le mieux au type JSON retourné.</span><span class="sxs-lookup"><span data-stu-id="847d0-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="847d0-118">Les fonctions JavaScript qui retournent une [promesse](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) sont appelées avec <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="847d0-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="847d0-119">`InvokeAsync` désencapsule la promesse et retourne la valeur attendue par la promesse.</span><span class="sxs-lookup"><span data-stu-id="847d0-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="847d0-120">Pour Blazor Server les applications pour lesquelles le prérendu est activé, l’appel à JavaScript n’est pas possible pendant le prérendu initial.</span><span class="sxs-lookup"><span data-stu-id="847d0-120">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="847d0-121">Les appels Interop JavaScript doivent être différés jusqu’à ce que la connexion avec le navigateur soit établie.</span><span class="sxs-lookup"><span data-stu-id="847d0-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="847d0-122">Pour plus d’informations, consultez la section [détecter le Blazor Server prérendu d’une application](#detect-when-a-blazor-server-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="847d0-122">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="847d0-123">L’exemple suivant est basé sur [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) , un décodeur basé sur JavaScript.</span><span class="sxs-lookup"><span data-stu-id="847d0-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="847d0-124">L’exemple montre comment appeler une fonction JavaScript à partir d’une méthode C# qui décharge une spécification du code de développeur vers une API JavaScript existante.</span><span class="sxs-lookup"><span data-stu-id="847d0-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="847d0-125">La fonction JavaScript accepte un tableau d’octets d’une méthode C#, décode le tableau et retourne le texte au composant pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="847d0-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="847d0-126">À l’intérieur `<head>` de l’élément de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), fournissez une fonction JavaScript qui utilise `TextDecoder` pour décoder un tableau passé et retourner la valeur décodée :</span><span class="sxs-lookup"><span data-stu-id="847d0-126">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="847d0-127">Du code JavaScript, tel que le code illustré dans l’exemple précédent, peut également être chargé à partir d’un fichier JavaScript ( `.js` ) avec une référence au fichier de script :</span><span class="sxs-lookup"><span data-stu-id="847d0-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="847d0-128">Le composant suivant :</span><span class="sxs-lookup"><span data-stu-id="847d0-128">The following component:</span></span>

* <span data-ttu-id="847d0-129">Appelle la `convertArray` fonction JavaScript à l’aide `JS` de lorsqu’un bouton de composant ( **`Convert Array`** ) est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="847d0-129">Invokes the `convertArray` JavaScript function using `JS` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="847d0-130">Une fois la fonction JavaScript appelée, le tableau passé est converti en chaîne.</span><span class="sxs-lookup"><span data-stu-id="847d0-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="847d0-131">La chaîne est retournée au composant pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="847d0-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="847d0-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="847d0-132">IJSRuntime</span></span>

<span data-ttu-id="847d0-133">Pour utiliser l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="847d0-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="847d0-134">Injecter l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction dans le Razor composant ( `.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="847d0-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="847d0-135">À l’intérieur `<head>` de l’élément de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), fournissez une `handleTickerChanged` fonction JavaScript.</span><span class="sxs-lookup"><span data-stu-id="847d0-135">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="847d0-136">La fonction est appelée avec <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> et ne retourne pas de valeur :</span><span class="sxs-lookup"><span data-stu-id="847d0-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="847d0-137">Injecter l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction dans une classe ( `.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="847d0-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="847d0-138">À l’intérieur `<head>` de l’élément de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), fournissez une `handleTickerChanged` fonction JavaScript.</span><span class="sxs-lookup"><span data-stu-id="847d0-138">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="847d0-139">La fonction est appelée avec `JS.InvokeAsync` et retourne une valeur :</span><span class="sxs-lookup"><span data-stu-id="847d0-139">The function is called with `JS.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="847d0-140">Pour la génération de contenu dynamique avec [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), utilisez l' `[Inject]` attribut :</span><span class="sxs-lookup"><span data-stu-id="847d0-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

<span data-ttu-id="847d0-141">Dans l’exemple d’application côté client qui accompagne cette rubrique, deux fonctions JavaScript sont disponibles pour l’application qui interagit avec le DOM pour recevoir l’entrée d’utilisateur et afficher un message d’accueil :</span><span class="sxs-lookup"><span data-stu-id="847d0-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="847d0-142">`showPrompt`: Génère une invite pour accepter l’entrée d’utilisateur (nom de l’utilisateur) et retourne le nom à l’appelant.</span><span class="sxs-lookup"><span data-stu-id="847d0-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="847d0-143">`displayWelcome`: Assigne un message de bienvenue de l’appelant à un objet DOM avec un `id` de `welcome` .</span><span class="sxs-lookup"><span data-stu-id="847d0-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="847d0-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="847d0-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="847d0-145">Placez la `<script>` balise qui référence le fichier JavaScript dans le fichier `wwwroot/index.html` ( Blazor WebAssembly ) ou le `Pages/_Host.cshtml` fichier ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="847d0-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="847d0-146">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="847d0-146">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="847d0-147">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="847d0-147">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

<span data-ttu-id="847d0-148">Ne placez pas de `<script>` balise dans un fichier de composant parce que la `<script>` balise ne peut pas être mise à jour dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="847d0-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="847d0-149">Les méthodes .NET interagissent avec les fonctions JavaScript dans le `exampleJsInterop.js` fichier en appelant <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="847d0-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="847d0-150">L' <xref:Microsoft.JSInterop.IJSRuntime> abstraction est asynchrone pour permettre les Blazor Server scénarios.</span><span class="sxs-lookup"><span data-stu-id="847d0-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="847d0-151">Si l’application est une Blazor WebAssembly application et que vous souhaitez appeler une fonction JavaScript de manière synchrone, vous devez effectuer un cast aval vers <xref:Microsoft.JSInterop.IJSInProcessRuntime> et appeler à la <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> place.</span><span class="sxs-lookup"><span data-stu-id="847d0-151">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="847d0-152">Nous recommandons que la plupart des bibliothèques d’interopérabilité JS utilisent les API Async pour s’assurer que les bibliothèques sont disponibles dans tous les scénarios.</span><span class="sxs-lookup"><span data-stu-id="847d0-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="847d0-153">Pour activer l’isolation JavaScript dans les [modules JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard, consultez la section [ Blazor isolation JavaScript et références d’objets](#blazor-javascript-isolation-and-object-references) .</span><span class="sxs-lookup"><span data-stu-id="847d0-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="847d0-154">L’exemple d’application comprend un composant pour illustrer l’interopérabilité de JS.</span><span class="sxs-lookup"><span data-stu-id="847d0-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="847d0-155">Le composant :</span><span class="sxs-lookup"><span data-stu-id="847d0-155">The component:</span></span>

* <span data-ttu-id="847d0-156">Reçoit une entrée d’utilisateur via une invite JavaScript.</span><span class="sxs-lookup"><span data-stu-id="847d0-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="847d0-157">Retourne le texte du composant pour traitement.</span><span class="sxs-lookup"><span data-stu-id="847d0-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="847d0-158">Appelle une deuxième fonction JavaScript qui interagit avec le DOM pour afficher un message d’accueil.</span><span class="sxs-lookup"><span data-stu-id="847d0-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="847d0-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="847d0-159">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="847d0-160">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="847d0-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="847d0-161">Lorsque `TriggerJsPrompt` est exécuté en sélectionnant le bouton du composant **`Trigger JavaScript Prompt`** , la `showPrompt` fonction JavaScript fournie dans le `wwwroot/exampleJsInterop.js` fichier est appelée.</span><span class="sxs-lookup"><span data-stu-id="847d0-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="847d0-162">La `showPrompt` fonction accepte l’entrée d’utilisateur (nom de l’utilisateur), qui est codée au format HTML et renvoyée au composant.</span><span class="sxs-lookup"><span data-stu-id="847d0-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="847d0-163">Le composant stocke le nom de l’utilisateur dans une variable locale, `name` .</span><span class="sxs-lookup"><span data-stu-id="847d0-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="847d0-164">La chaîne stockée dans `name` est incorporée dans un message de bienvenue, qui est transmis à une fonction JavaScript, `displayWelcome` , qui affiche le message de bienvenue dans une balise de titre.</span><span class="sxs-lookup"><span data-stu-id="847d0-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="847d0-165">Appeler une fonction JavaScript void</span><span class="sxs-lookup"><span data-stu-id="847d0-165">Call a void JavaScript function</span></span>

<span data-ttu-id="847d0-166">Utilisez <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> pour les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="847d0-166">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="847d0-167">Fonctions JavaScript qui retournent [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [non défini](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span><span class="sxs-lookup"><span data-stu-id="847d0-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="847d0-168">Si .NET n’est pas nécessaire pour lire le résultat d’un appel JavaScript.</span><span class="sxs-lookup"><span data-stu-id="847d0-168">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="847d0-169">Détecter quand une Blazor Server application est prérendu</span><span class="sxs-lookup"><span data-stu-id="847d0-169">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="847d0-170">Capturer des références à des éléments</span><span class="sxs-lookup"><span data-stu-id="847d0-170">Capture references to elements</span></span>

<span data-ttu-id="847d0-171">Certains scénarios d’interopérabilité JS requièrent des références à des éléments HTML.</span><span class="sxs-lookup"><span data-stu-id="847d0-171">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="847d0-172">Par exemple, une bibliothèque d’interface utilisateur peut nécessiter une référence d’élément pour l’initialisation, ou vous devrez peut-être appeler des API de type commande sur un élément, tel que `focus` ou `play` .</span><span class="sxs-lookup"><span data-stu-id="847d0-172">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="847d0-173">Capturer des références aux éléments HTML dans un composant à l’aide de l’approche suivante :</span><span class="sxs-lookup"><span data-stu-id="847d0-173">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="847d0-174">Ajoutez un `@ref` attribut à l’élément HTML.</span><span class="sxs-lookup"><span data-stu-id="847d0-174">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="847d0-175">Définissez un champ de type <xref:Microsoft.AspNetCore.Components.ElementReference> dont le nom correspond à la valeur de l' `@ref` attribut.</span><span class="sxs-lookup"><span data-stu-id="847d0-175">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="847d0-176">L’exemple suivant illustre la capture d’une référence à l' `username` `<input>` élément :</span><span class="sxs-lookup"><span data-stu-id="847d0-176">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="847d0-177">Utilisez uniquement une référence d’élément pour muter le contenu d’un élément vide qui n’interagit pas avec Blazor .</span><span class="sxs-lookup"><span data-stu-id="847d0-177">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="847d0-178">Ce scénario est utile quand une API tierce fournit du contenu à l’élément.</span><span class="sxs-lookup"><span data-stu-id="847d0-178">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="847d0-179">Étant donné que Blazor n’interagit pas avec l’élément, il n’existe aucun risque de conflit entre Blazor la représentation de l’élément et le DOM.</span><span class="sxs-lookup"><span data-stu-id="847d0-179">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="847d0-180">Dans l’exemple suivant, il est *dangereux* de faire muter le contenu de la liste non triée ( `ul` ), car Blazor interagit avec le DOM pour remplir les éléments de liste de cet élément ( `<li>` ) :</span><span class="sxs-lookup"><span data-stu-id="847d0-180">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="847d0-181">Si l’interopérabilité JS muter le contenu de l’élément `MyList` et Blazor tente d’appliquer des différences à l’élément, les différences ne correspondent pas au DOM.</span><span class="sxs-lookup"><span data-stu-id="847d0-181">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="847d0-182">Un <xref:Microsoft.AspNetCore.Components.ElementReference> est passé à du code JavaScript via l’interopérabilité js.</span><span class="sxs-lookup"><span data-stu-id="847d0-182">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="847d0-183">Le code JavaScript reçoit une `HTMLElement` instance, qu’il peut utiliser avec les API DOM normales.</span><span class="sxs-lookup"><span data-stu-id="847d0-183">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="847d0-184">Par exemple, le code suivant définit une méthode d’extension .NET qui permet d’envoyer un clic de souris à un élément :</span><span class="sxs-lookup"><span data-stu-id="847d0-184">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="847d0-185">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="847d0-185">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="847d0-186">Utilisez [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) dans le code C# pour mettre le focus sur un élément, qui est intégré à l' Blazor infrastructure et fonctionne avec les références d’élément.</span><span class="sxs-lookup"><span data-stu-id="847d0-186">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the Blazor framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="847d0-187">Pour appeler une fonction JavaScript qui ne retourne pas de valeur, utilisez <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="847d0-187">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="847d0-188">Le code suivant déclenche un événement côté client `Click` en appelant la fonction JavaScript précédente avec le capturé <xref:Microsoft.AspNetCore.Components.ElementReference> :</span><span class="sxs-lookup"><span data-stu-id="847d0-188">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="847d0-189">Pour utiliser une méthode d’extension, créez une méthode d’extension statique qui reçoit l' <xref:Microsoft.JSInterop.IJSRuntime> instance :</span><span class="sxs-lookup"><span data-stu-id="847d0-189">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="847d0-190">La `clickElement` méthode est appelée directement sur l’objet.</span><span class="sxs-lookup"><span data-stu-id="847d0-190">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="847d0-191">L’exemple suivant suppose que la `TriggerClickEvent` méthode est disponible à partir de l' `JsInteropClasses` espace de noms :</span><span class="sxs-lookup"><span data-stu-id="847d0-191">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="847d0-192">La `exampleButton` variable est remplie uniquement après le rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="847d0-192">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="847d0-193">Si un non rempli <xref:Microsoft.AspNetCore.Components.ElementReference> est passé à du code JavaScript, le code JavaScript reçoit la valeur `null` .</span><span class="sxs-lookup"><span data-stu-id="847d0-193">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="847d0-194">Pour manipuler des références d’élément après que le composant a terminé le rendu, utilisez les [ `OnAfterRenderAsync` `OnAfterRender` méthodes de cycle de vie des composants ou](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="847d0-194">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="847d0-195">Lorsque vous utilisez des types génériques et que vous retournez une valeur, utilisez <xref:System.Threading.Tasks.ValueTask%601> :</span><span class="sxs-lookup"><span data-stu-id="847d0-195">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="847d0-196">`GenericMethod` est appelé directement sur l’objet avec un type.</span><span class="sxs-lookup"><span data-stu-id="847d0-196">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="847d0-197">L’exemple suivant suppose que le `GenericMethod` est disponible à partir de l' `JsInteropClasses` espace de noms :</span><span class="sxs-lookup"><span data-stu-id="847d0-197">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="847d0-198">Éléments de référence sur les composants</span><span class="sxs-lookup"><span data-stu-id="847d0-198">Reference elements across components</span></span>

<span data-ttu-id="847d0-199">Un <xref:Microsoft.AspNetCore.Components.ElementReference> ne peut pas être transmis entre les composants, car :</span><span class="sxs-lookup"><span data-stu-id="847d0-199">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="847d0-200">L’instance ne peut exister qu’après le rendu du composant, c’est-à-dire pendant ou après l’exécution de la méthode d’un composant <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="847d0-200">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="847d0-201">Un <xref:Microsoft.AspNetCore.Components.ElementReference> est un [`struct`](/csharp/language-reference/builtin-types/struct) , qui ne peut pas être passé en tant que [paramètre de composant](xref:blazor/components/index#component-parameters).</span><span class="sxs-lookup"><span data-stu-id="847d0-201">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="847d0-202">Pour qu’un composant parent rende une référence d’élément disponible pour d’autres composants, le composant parent peut :</span><span class="sxs-lookup"><span data-stu-id="847d0-202">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="847d0-203">Autorisez les composants enfants à inscrire des rappels.</span><span class="sxs-lookup"><span data-stu-id="847d0-203">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="847d0-204">Appelez les rappels inscrits pendant l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> événement avec la référence d’élément passée.</span><span class="sxs-lookup"><span data-stu-id="847d0-204">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="847d0-205">Indirectement, cette approche permet aux composants enfants d’interagir avec la référence d’élément du parent.</span><span class="sxs-lookup"><span data-stu-id="847d0-205">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="847d0-206">L' Blazor WebAssembly exemple suivant illustre l’approche.</span><span class="sxs-lookup"><span data-stu-id="847d0-206">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="847d0-207">Dans le `<head>` de `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="847d0-207">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="847d0-208">Dans le `<body>` de `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="847d0-208">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="847d0-209">`Pages/Index.razor` (composant parent) :</span><span class="sxs-lookup"><span data-stu-id="847d0-209">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="847d0-210">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="847d0-210">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="847d0-211">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="847d0-211">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="847d0-212">`Shared/SurveyPrompt.razor` (composant enfant) :</span><span class="sxs-lookup"><span data-stu-id="847d0-212">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="847d0-213">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="847d0-213">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="847d0-214">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="847d0-214">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="847d0-215">Sécuriser les appels d’interopérabilité JS</span><span class="sxs-lookup"><span data-stu-id="847d0-215">Harden JS interop calls</span></span>

<span data-ttu-id="847d0-216">L’interopérabilité de JS peut échouer en raison d’erreurs réseau et doit être considérée comme non fiable.</span><span class="sxs-lookup"><span data-stu-id="847d0-216">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="847d0-217">Par défaut, une Blazor Server application expire les appels d’interopérabilité js sur le serveur après une minute.</span><span class="sxs-lookup"><span data-stu-id="847d0-217">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="847d0-218">Si une application peut tolérer un délai d’expiration plus agressif, définissez le délai d’expiration à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="847d0-218">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="847d0-219">Globalement dans `Startup.ConfigureServices` , spécifiez le délai d’expiration :</span><span class="sxs-lookup"><span data-stu-id="847d0-219">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="847d0-220">Par appel dans le code du composant, un appel unique peut spécifier le délai d’attente :</span><span class="sxs-lookup"><span data-stu-id="847d0-220">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="847d0-221">Pour plus d’informations sur l’épuisement des ressources, consultez <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="847d0-221">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="847d0-222">Éviter les références d’objets circulaires</span><span class="sxs-lookup"><span data-stu-id="847d0-222">Avoid circular object references</span></span>

<span data-ttu-id="847d0-223">Les objets qui contiennent des références circulaires ne peuvent pas être sérialisés sur le client pour :</span><span class="sxs-lookup"><span data-stu-id="847d0-223">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="847d0-224">Appels de méthode .NET.</span><span class="sxs-lookup"><span data-stu-id="847d0-224">.NET method calls.</span></span>
* <span data-ttu-id="847d0-225">Appels de méthode JavaScript à partir de C# lorsque le type de retour a des références circulaires.</span><span class="sxs-lookup"><span data-stu-id="847d0-225">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="847d0-226">Pour plus d’informations, consultez les problèmes suivants :</span><span class="sxs-lookup"><span data-stu-id="847d0-226">For more information, see the following issues:</span></span>

* [<span data-ttu-id="847d0-227">Les références circulaires ne sont pas prises en charge, prennent deux (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="847d0-227">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="847d0-228">Proposition : ajouter un mécanisme pour gérer les références circulaires lors de la sérialisation (dotnet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="847d0-228">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="847d0-229">Blazor Isolation JavaScript et références d’objets</span><span class="sxs-lookup"><span data-stu-id="847d0-229">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="847d0-230">Blazor active l’isolation JavaScript dans les [modules JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard.</span><span class="sxs-lookup"><span data-stu-id="847d0-230">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="847d0-231">L’isolation JavaScript offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="847d0-231">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="847d0-232">Le JavaScript importé ne pollue plus l’espace de noms global.</span><span class="sxs-lookup"><span data-stu-id="847d0-232">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="847d0-233">Les consommateurs d’une bibliothèque et de composants ne sont pas requis pour importer le code JavaScript associé.</span><span class="sxs-lookup"><span data-stu-id="847d0-233">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="847d0-234">Par exemple, le module JavaScript suivant exporte une fonction JavaScript pour afficher une invite de navigateur :</span><span class="sxs-lookup"><span data-stu-id="847d0-234">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="847d0-235">Ajoutez le module JavaScript précédent à une bibliothèque .NET en tant que ressource Web statique ( `wwwroot/exampleJsInterop.js` ), puis importez le module dans le code .net à l’aide du <xref:Microsoft.JSInterop.IJSRuntime> service.</span><span class="sxs-lookup"><span data-stu-id="847d0-235">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="847d0-236">Le service est injecté comme `js` (non affiché) pour l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="847d0-236">The service is injected as `js` (not shown) for the following example:</span></span>

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="847d0-237">L' `import` identificateur dans l’exemple précédent est un identificateur spécial utilisé spécifiquement pour l’importation d’un module JavaScript.</span><span class="sxs-lookup"><span data-stu-id="847d0-237">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="847d0-238">Spécifiez le module à l’aide de son chemin d’accès aux ressources Web statiques stable : `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` .</span><span class="sxs-lookup"><span data-stu-id="847d0-238">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="847d0-239">L’espace réservé `{LIBRARY NAME}` est le nom de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="847d0-239">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="847d0-240">L’espace réservé `{PATH UNDER WWWROOT}` est le chemin d’accès au script sous `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="847d0-240">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="847d0-241"><xref:Microsoft.JSInterop.IJSRuntime> importe le module en tant que `IJSObjectReference` , qui représente une référence à un objet JavaScript à partir du code .net.</span><span class="sxs-lookup"><span data-stu-id="847d0-241"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="847d0-242">Utilisez `IJSObjectReference` pour appeler les fonctions JavaScript exportées à partir du module :</span><span class="sxs-lookup"><span data-stu-id="847d0-242">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="847d0-243">`IJSInProcessObjectReference` représente une référence à un objet JavaScript dont les fonctions peuvent être appelées de façon synchrone.</span><span class="sxs-lookup"><span data-stu-id="847d0-243">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="847d0-244">Utilisation de bibliothèques JavaScript qui affichent l’interface utilisateur (éléments DOM)</span><span class="sxs-lookup"><span data-stu-id="847d0-244">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="847d0-245">Parfois, vous souhaiterez peut-être utiliser des bibliothèques JavaScript qui produisent des éléments d’interface utilisateur visibles dans le DOM du navigateur.</span><span class="sxs-lookup"><span data-stu-id="847d0-245">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="847d0-246">À première vue, cela peut paraître difficile, car la Blazor différenciation du système repose sur le contrôle de l’arborescence des éléments DOM et s’exécute en erreurs si un code externe fait muter l’arborescence DOM et invalide son mécanisme pour l’application de différences.</span><span class="sxs-lookup"><span data-stu-id="847d0-246">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="847d0-247">Il ne s’agit pas d’une Blazor limitation spécifique.</span><span class="sxs-lookup"><span data-stu-id="847d0-247">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="847d0-248">Le même défi se produit avec toute infrastructure d’interface utilisateur basée sur des différences.</span><span class="sxs-lookup"><span data-stu-id="847d0-248">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="847d0-249">Heureusement, il est facile d’incorporer de manière fiable une interface utilisateur générée en externe dans une Blazor interface utilisateur de composant.</span><span class="sxs-lookup"><span data-stu-id="847d0-249">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="847d0-250">La technique recommandée consiste à faire en sorte que le code ( `.razor` fichier) du composant produise un élément vide.</span><span class="sxs-lookup"><span data-stu-id="847d0-250">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="847d0-251">En ce qui concerne Blazor le système de différenciation, l’élément est toujours vide, de sorte que le convertisseur ne parcourt pas de manière récursive l’élément et laisse son contenu seul.</span><span class="sxs-lookup"><span data-stu-id="847d0-251">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="847d0-252">Cela permet de remplir en toute sécurité l’élément avec un contenu arbitraire géré en externe.</span><span class="sxs-lookup"><span data-stu-id="847d0-252">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="847d0-253">L’exemple suivant illustre le concept.</span><span class="sxs-lookup"><span data-stu-id="847d0-253">The following example demonstrates the concept.</span></span> <span data-ttu-id="847d0-254">Dans l' `if` instruction, lorsque a la valeur `firstRender` `true` , effectuez une opération avec `myElement` .</span><span class="sxs-lookup"><span data-stu-id="847d0-254">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="847d0-255">Par exemple, appelez une bibliothèque JavaScript externe pour la remplir.</span><span class="sxs-lookup"><span data-stu-id="847d0-255">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="847d0-256">Blazor conserve le contenu de l’élément seul jusqu’à ce que ce composant lui-même soit supprimé.</span><span class="sxs-lookup"><span data-stu-id="847d0-256">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="847d0-257">Lorsque le composant est supprimé, la sous-arborescence DOM entière du composant est également supprimée.</span><span class="sxs-lookup"><span data-stu-id="847d0-257">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

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

<span data-ttu-id="847d0-258">En guise d’exemple plus détaillé, considérez le composant suivant qui restitue une carte interactive à l’aide des [API Mapbox Open source](https://www.mapbox.com/):</span><span class="sxs-lookup"><span data-stu-id="847d0-258">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

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

<span data-ttu-id="847d0-259">Le module JavaScript correspondant, qui doit être placé à l’adresse `wwwroot/mapComponent.js` , est le suivant :</span><span class="sxs-lookup"><span data-stu-id="847d0-259">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

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

<span data-ttu-id="847d0-260">Dans l’exemple précédent, remplacez la chaîne `{ACCESS TOKEN}` par un jeton d’accès valide à partir duquel vous pouvez obtenir https://account.mapbox.com .</span><span class="sxs-lookup"><span data-stu-id="847d0-260">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="847d0-261">Pour produire un style correct, ajoutez la balise de feuille de style suivante à la page HTML de l’hôte ( `index.html` ou `_Host.cshtml` ) :</span><span class="sxs-lookup"><span data-stu-id="847d0-261">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="847d0-262">L’exemple précédent produit une interface utilisateur de carte interactive, dans laquelle l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="847d0-262">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="847d0-263">Peut faire glisser pour faire défiler ou zoomer.</span><span class="sxs-lookup"><span data-stu-id="847d0-263">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="847d0-264">Cliquez sur les boutons pour accéder aux emplacements prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="847d0-264">Click buttons to jump to predefined locations.</span></span>

![Mapbox Street Map de Tokyo, Japon et des boutons permettant de sélectionner les Bristol, le Royaume-Uni et Tokyo, le Japon](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="847d0-266">Les points clés à comprendre sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="847d0-266">The key points to understand are:</span></span>

 * <span data-ttu-id="847d0-267">Le `<div>` avec `@ref="mapElement"` est laissé vide en ce qui concerne le Blazor .</span><span class="sxs-lookup"><span data-stu-id="847d0-267">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="847d0-268">C’est pourquoi il est possible de le `mapbox-gl.js` remplir et de modifier son contenu au fil du temps.</span><span class="sxs-lookup"><span data-stu-id="847d0-268">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="847d0-269">Vous pouvez utiliser cette technique avec n’importe quelle bibliothèque JavaScript qui restitue l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="847d0-269">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="847d0-270">Vous pouvez même incorporer des composants d’une infrastructure SPA JavaScript tierce dans des Blazor composants, à condition qu’ils ne tentent pas d’atteindre et de modifier d’autres parties de la page.</span><span class="sxs-lookup"><span data-stu-id="847d0-270">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="847d0-271">Il n’est *pas* possible pour le code JavaScript externe de modifier des éléments qui Blazor ne sont pas pris en compte comme vides.</span><span class="sxs-lookup"><span data-stu-id="847d0-271">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="847d0-272">Lorsque vous utilisez cette approche, gardez à l’esprit les règles de Blazor conservation ou de destruction des éléments DOM.</span><span class="sxs-lookup"><span data-stu-id="847d0-272">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="847d0-273">Dans l’exemple précédent, le composant gère en toute sécurité les événements de clic de bouton et met à jour l’instance de mappage existante, car les éléments DOM sont conservés dans la mesure du possible par défaut.</span><span class="sxs-lookup"><span data-stu-id="847d0-273">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="847d0-274">Si vous avez rendu une liste d’éléments cartographiques à partir d’une `@foreach` boucle, vous souhaitez utiliser `@key` pour garantir la préservation des instances de composant.</span><span class="sxs-lookup"><span data-stu-id="847d0-274">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="847d0-275">Dans le cas contraire, les modifications apportées aux données de la liste peuvent entraîner la conservation de l’état des instances précédentes par les instances de composant de manière indésirable.</span><span class="sxs-lookup"><span data-stu-id="847d0-275">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="847d0-276">Pour plus d’informations, consultez [utilisation @key de pour conserver les éléments et les composants](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="847d0-276">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="847d0-277">En outre, l’exemple précédent montre comment il est possible d’encapsuler la logique JavaScript et les dépendances dans un module ES6 et de le charger dynamiquement à l’aide de l' `import` identificateur.</span><span class="sxs-lookup"><span data-stu-id="847d0-277">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="847d0-278">Pour plus d’informations, consultez la rubrique [isolation JavaScript et références d’objets](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="847d0-278">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="847d0-279">Limites de taille sur les appels d’interopérabilité JS</span><span class="sxs-lookup"><span data-stu-id="847d0-279">Size limits on JS interop calls</span></span>

<span data-ttu-id="847d0-280">Dans Blazor WebAssembly , l’infrastructure n’impose pas de limites sur la taille des entrées et des sorties des appels d’interopérabilité js.</span><span class="sxs-lookup"><span data-stu-id="847d0-280">In Blazor WebAssembly, the framework doesn't impose limits on the size of inputs and outputs of JS interop calls.</span></span>

<span data-ttu-id="847d0-281">Dans Blazor Server , le résultat d’un appel d’interopérabilité js est limité par la taille maximale de charge utile appliquée par SignalR ( <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> ), qui est par défaut de 32 Ko.</span><span class="sxs-lookup"><span data-stu-id="847d0-281">In Blazor Server, the result of a JS interop call is limited by the maximum payload size enforced by SignalR (<xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>), which defaults to 32 KB.</span></span> <span data-ttu-id="847d0-282">Les applications qui tentent de répondre à un appel d’interopérabilité JS avec une charge utile supérieure à <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> génèrent une erreur.</span><span class="sxs-lookup"><span data-stu-id="847d0-282">Applications that attempt to respond to a JS interop call with a payload larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="847d0-283">Une limite plus grande peut être configurée en modifiant <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> .</span><span class="sxs-lookup"><span data-stu-id="847d0-283">A larger limit can be configured by modifying <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>.</span></span> <span data-ttu-id="847d0-284">L’exemple suivant définit la taille maximale des messages de réception sur 64 Ko (64 \* 1024 \* 1024) :</span><span class="sxs-lookup"><span data-stu-id="847d0-284">The following example sets the maximum receive message size to 64 KB (64 \* 1024 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

<span data-ttu-id="847d0-285">L’augmentation de la SignalR limite survient au détriment de l’utilisation de ressources serveur supplémentaires et expose le serveur à des risques accrus d’un utilisateur malveillant.</span><span class="sxs-lookup"><span data-stu-id="847d0-285">Increasing the SignalR limit comes at the cost of requiring the use of more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="847d0-286">En outre, la lecture d’une grande quantité de contenu dans la mémoire en tant que chaînes ou tableaux d’octets peut également entraîner des allocations qui fonctionnent mal avec le garbage collector, ce qui entraîne des pénalités en matière de performances.</span><span class="sxs-lookup"><span data-stu-id="847d0-286">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span> <span data-ttu-id="847d0-287">L’une des options de lecture des charges utiles volumineuses consiste à entourer le contenu de plus petits segments et à traiter la charge utile sous la forme d’un <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="847d0-287">One option for reading large payloads is to consider sending the content in smaller chunks and processing the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="847d0-288">Cela peut être utilisé lors de la lecture de charges utiles JSON volumineuses ou si les données sont disponibles en tant qu’octets bruts dans JavaScript.</span><span class="sxs-lookup"><span data-stu-id="847d0-288">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="847d0-289">Pour obtenir un exemple qui montre comment envoyer des charges utiles binaires volumineuses dans Blazor Server qui utilise des techniques similaires au `InputFile` composant, consultez l' [exemple d’application de soumission binaire](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="847d0-289">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="847d0-290">Tenez compte des conseils suivants lors du développement de code qui transfère une grande quantité de données entre JavaScript et Blazor :</span><span class="sxs-lookup"><span data-stu-id="847d0-290">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="847d0-291">Découpez les données en éléments plus petits et envoyez les segments de données de façon séquentielle jusqu’à ce que toutes les données soient reçues par le serveur.</span><span class="sxs-lookup"><span data-stu-id="847d0-291">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="847d0-292">N’allouez pas d’objets volumineux dans du code JavaScript et C#.</span><span class="sxs-lookup"><span data-stu-id="847d0-292">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="847d0-293">Ne bloquez pas le thread d’interface utilisateur principal pendant de longues périodes lors de l’envoi ou de la réception de données.</span><span class="sxs-lookup"><span data-stu-id="847d0-293">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="847d0-294">Libérez la mémoire consommée lorsque le processus est terminé ou annulé.</span><span class="sxs-lookup"><span data-stu-id="847d0-294">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="847d0-295">Appliquer les exigences supplémentaires suivantes pour des raisons de sécurité :</span><span class="sxs-lookup"><span data-stu-id="847d0-295">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="847d0-296">Déclarez la taille maximale du fichier ou des données qui peut être passée.</span><span class="sxs-lookup"><span data-stu-id="847d0-296">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="847d0-297">Déclarez le taux de téléchargement minimal du client au serveur.</span><span class="sxs-lookup"><span data-stu-id="847d0-297">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="847d0-298">Une fois les données reçues par le serveur, les données peuvent être :</span><span class="sxs-lookup"><span data-stu-id="847d0-298">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="847d0-299">Stocké temporairement dans une mémoire tampon jusqu’à ce que tous les segments soient collectés.</span><span class="sxs-lookup"><span data-stu-id="847d0-299">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="847d0-300">Consommé immédiatement.</span><span class="sxs-lookup"><span data-stu-id="847d0-300">Consumed immediately.</span></span> <span data-ttu-id="847d0-301">Par exemple, les données peuvent être stockées immédiatement dans une base de données ou écrites sur le disque à mesure que chaque segment est reçu.</span><span class="sxs-lookup"><span data-stu-id="847d0-301">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>
  
## <a name="js-modules"></a><span data-ttu-id="847d0-302">Modules JS</span><span class="sxs-lookup"><span data-stu-id="847d0-302">JS modules</span></span>

<span data-ttu-id="847d0-303">Pour l’isolation de JS, l’interopérabilité de JS fonctionne avec la prise en charge par défaut du navigateur pour les [modules ECMAScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([spécification ECMAScript](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="847d0-303">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="unmarshalled-js-interop"></a><span data-ttu-id="847d0-304">Interopérabilité JS démarshalé</span><span class="sxs-lookup"><span data-stu-id="847d0-304">Unmarshalled JS interop</span></span>

<span data-ttu-id="847d0-305">Blazor WebAssembly les composants peuvent présenter des performances médiocres lorsque les objets .NET sont sérialisés pour l’interopérabilité JS et que l’une des conditions suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="847d0-305">Blazor WebAssembly components may experience poor performance when .NET objects are serialized for JS interop and either of the following are true:</span></span>

* <span data-ttu-id="847d0-306">Un volume élevé d’objets .NET est sérialisé rapidement.</span><span class="sxs-lookup"><span data-stu-id="847d0-306">A high volume of .NET objects are rapidly serialized.</span></span> <span data-ttu-id="847d0-307">Exemple : les appels d’interopérabilité JS sont effectués sur la base du déplacement d’un périphérique d’entrée, tel que la rotation d’une roulette de souris.</span><span class="sxs-lookup"><span data-stu-id="847d0-307">Example: JS interop calls are made on the basis of moving an input device, such as spinning a mouse wheel.</span></span>
* <span data-ttu-id="847d0-308">Les objets .NET volumineux ou de nombreux objets .NET doivent être sérialisés pour l’interopérabilité de JS.</span><span class="sxs-lookup"><span data-stu-id="847d0-308">Large .NET objects or many .NET objects must be serialized for JS interop.</span></span> <span data-ttu-id="847d0-309">Exemple : les appels d’interopérabilité JS nécessitent la sérialisation de dizaines de fichiers.</span><span class="sxs-lookup"><span data-stu-id="847d0-309">Example: JS interop calls require serializing dozens of files.</span></span>

<span data-ttu-id="847d0-310"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> représente une référence à un objet JavaScript dont les fonctions peuvent être appelées sans la surcharge liée à la sérialisation des données .NET.</span><span class="sxs-lookup"><span data-stu-id="847d0-310"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span>

<span data-ttu-id="847d0-311">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="847d0-311">In the following example:</span></span>

* <span data-ttu-id="847d0-312">Un [struct](/dotnet/csharp/language-reference/builtin-types/struct) contenant une chaîne et un entier est passé non sérialisé à JavaScript.</span><span class="sxs-lookup"><span data-stu-id="847d0-312">A [struct](/dotnet/csharp/language-reference/builtin-types/struct) containing a string and an integer is passed unserialized to JavaScript.</span></span>
* <span data-ttu-id="847d0-313">Les fonctions JavaScript traitent les données et retournent une valeur booléenne ou une chaîne à l’appelant.</span><span class="sxs-lookup"><span data-stu-id="847d0-313">JavaScript functions process the data and return either a boolean or string to the caller.</span></span>
* <span data-ttu-id="847d0-314">Une chaîne JavaScript n’est pas directement convertible en `string` objet .net.</span><span class="sxs-lookup"><span data-stu-id="847d0-314">A JavaScript string isn't directly convertible into a .NET `string` object.</span></span> <span data-ttu-id="847d0-315">La `unmarshalledFunctionReturnString` fonction appelle `BINDING.js_string_to_mono_string` pour gérer la conversion d’une chaîne JavaScript.</span><span class="sxs-lookup"><span data-stu-id="847d0-315">The `unmarshalledFunctionReturnString` function calls `BINDING.js_string_to_mono_string` to manage the conversion of a Javascript string.</span></span>

> [!NOTE]
> <span data-ttu-id="847d0-316">Les exemples suivants ne sont pas des cas d’utilisation typiques pour ce scénario, car le [struct](/dotnet/csharp/language-reference/builtin-types/struct) passé à JavaScript n’entraîne pas de mauvaises performances des composants.</span><span class="sxs-lookup"><span data-stu-id="847d0-316">The following examples aren't typical use cases for this scenario because the [struct](/dotnet/csharp/language-reference/builtin-types/struct) passed to JavaScript doesn't result in poor component performance.</span></span> <span data-ttu-id="847d0-317">L’exemple utilise un petit objet simplement pour illustrer les concepts de passage de données .NET désérialisées.</span><span class="sxs-lookup"><span data-stu-id="847d0-317">The example uses a small object merely to demonstrate the concepts for passing unserialized .NET data.</span></span>

<span data-ttu-id="847d0-318">Contenu d’un `<script>` bloc dans `wwwroot/index.html` ou d’un fichier JavaScript externe référencé par `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="847d0-318">Content of a `<script>` block in `wwwroot/index.html` or an external Javascript file referenced by `wwwroot/index.html`:</span></span>

```javascript
window.returnJSObjectReference = () => {
    return {
        unmarshalledFunctionReturnBoolean: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return name === "Brigadier Alistair Gordon Lethbridge-Stewart" &&
                year === 1968;
        },
        unmarshalledFunctionReturnString: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return BINDING.js_string_to_mono_string(`Hello, ${name} (${year})!`);
        }
    };
}
```

> [!WARNING]
> <span data-ttu-id="847d0-319">Le `js_string_to_mono_string` nom, le comportement et l’existence de la fonction sont susceptibles d’être modifiés dans une prochaine version de .net.</span><span class="sxs-lookup"><span data-stu-id="847d0-319">The `js_string_to_mono_string` function name, behavior, and existence is subject to change in a future release of .NET.</span></span> <span data-ttu-id="847d0-320">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="847d0-320">For example:</span></span>
>
> * <span data-ttu-id="847d0-321">La fonction est susceptible d’être renommée.</span><span class="sxs-lookup"><span data-stu-id="847d0-321">The function is likely to be renamed.</span></span>
> * <span data-ttu-id="847d0-322">La fonction elle-même peut être supprimée en faveur de la conversion automatique des chaînes par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="847d0-322">The function itself might be removed in favor of automatic conversion of strings by the framework.</span></span>

<span data-ttu-id="847d0-323">`Pages/UnmarshalledJSInterop.razor` (URL : `/unmarshalled-js-interop` ) :</span><span class="sxs-lookup"><span data-stu-id="847d0-323">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop`):</span></span>

```razor
@page "/unmarshalled-js-interop"
@using System.Runtime.InteropServices
@using Microsoft.JSInterop
@inject IJSRuntime JS

<h1>Unmarshalled JS interop</h1>

@if (callResultForBoolean)
{
    <p>JS interop was successful!</p>
}

@if (!string.IsNullOrEmpty(callResultForString))
{
    <p>@callResultForString</p>
}

<p>
    <button @onclick="CallJSUnmarshalledForBoolean">
        Call Unmarshalled JS & Return Boolean
    </button>
    <button @onclick="CallJSUnmarshalledForString">
        Call Unmarshalled JS & Return String
    </button>
</p>

<p>
    <a href="https://www.doctorwho.tv">Doctor Who</a>
    is a registered trademark of the <a href="https://www.bbc.com/">BBC</a>.
</p>

@code {
    private bool callResultForBoolean;
    private string callResultForString;

    private void CallJSUnmarshalledForBoolean()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForBoolean = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, bool>(
                "unmarshalledFunctionReturnBoolean", GetStruct());
    }

    private void CallJSUnmarshalledForString()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForString = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, string>(
                "unmarshalledFunctionReturnString", GetStruct());
    }

    private InteropStruct GetStruct()
    {
        return new InteropStruct
        {
            Name = "Brigadier Alistair Gordon Lethbridge-Stewart",
            Year = 1968,
        };
    }

    [StructLayout(LayoutKind.Explicit)]
    public struct InteropStruct
    {
        [FieldOffset(0)]
        public string Name;

        [FieldOffset(8)]
        public int Year;
    }
}
```

<span data-ttu-id="847d0-324">Si une `IJSUnmarshalledObjectReference` instance n’est pas supprimée dans du code C#, elle peut être supprimée en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="847d0-324">If an `IJSUnmarshalledObjectReference` instance isn't disposed in C# code, it can be disposed in JavaScript.</span></span> <span data-ttu-id="847d0-325">La `dispose` fonction suivante supprime la référence d’objet quand elle est appelée à partir de JavaScript :</span><span class="sxs-lookup"><span data-stu-id="847d0-325">The following `dispose` function disposes the object reference when called from JavaScript:</span></span>

```javascript
window.exampleJSObjectReferenceNotDisposedInCSharp = () => {
    return {
        dispose: function () {
            DotNet.disposeJSObjectReference(this);
        },

        ...
    };
}
```

<span data-ttu-id="847d0-326">Les types tableau peuvent être convertis à partir d’objets JavaScript en objets .NET à l’aide de `js_typed_array_to_array` , mais le tableau JavaScript doit être un tableau typé.</span><span class="sxs-lookup"><span data-stu-id="847d0-326">Array types can be converted from JavaScript objects into .NET objects using `js_typed_array_to_array`, but the JavaScript array must be a typed array.</span></span> <span data-ttu-id="847d0-327">Les tableaux de JavaScript peuvent être lus dans du code C# en tant que tableau d’objets .NET ( `object[]` ).</span><span class="sxs-lookup"><span data-stu-id="847d0-327">Arrays from JavaScript can be read in C# code as a .NET object array (`object[]`).</span></span>

<span data-ttu-id="847d0-328">D’autres types de données, tels que les tableaux de chaînes, peuvent être convertis, mais nécessitent la création d’un nouvel objet de tableau mono ( `mono_obj_array_new` ) et la définition de sa valeur ( `mono_obj_array_set` ).</span><span class="sxs-lookup"><span data-stu-id="847d0-328">Other data types, such as string arrays, can be converted but require creating a new Mono array object (`mono_obj_array_new`) and setting its value (`mono_obj_array_set`).</span></span>

> [!WARNING]
> <span data-ttu-id="847d0-329">Les fonctions JavaScript fournies par l' Blazor infrastructure, telles que `js_typed_array_to_array` , `mono_obj_array_new` et `mono_obj_array_set` , sont sujettes à des modifications de nom, à des changements de comportement ou à la suppression dans les futures versions de .net.</span><span class="sxs-lookup"><span data-stu-id="847d0-329">JavaScript functions provided by the Blazor framework, such as `js_typed_array_to_array`, `mono_obj_array_new`, and `mono_obj_array_set`, are subject to name changes, behavioral changes, or removal in future releases of .NET.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="847d0-330">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="847d0-330">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="847d0-331">Exemple InteropComponent. Razor (référentiel GitHub dotnet/AspNetCore, branche de version 3,1)</span><span class="sxs-lookup"><span data-stu-id="847d0-331">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
