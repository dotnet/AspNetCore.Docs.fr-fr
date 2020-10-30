---
title: 'Utiliser la diffusion en continu dans ASP.NET Core :::no-loc(SignalR):::'
author: bradygaster
description: Découvrez comment diffuser en continu des données entre le client et le serveur.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 10/29/2020
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
uid: signalr/streaming
ms.openlocfilehash: b07c280f271ccdd525128b973da065001a5cf0ed
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062439"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="7ffc5-103">Utiliser la diffusion en continu dans ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="7ffc5-103">Use streaming in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="7ffc5-104">Par [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="7ffc5-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ffc5-105">ASP.NET Core :::no-loc(SignalR)::: prend en charge la diffusion en continu du client vers le serveur et du serveur au client.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-105">ASP.NET Core :::no-loc(SignalR)::: supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="7ffc5-106">Cela est utile pour les scénarios où les fragments de données arrivent dans le temps.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="7ffc5-107">Lors de la diffusion en continu, chaque fragment est envoyé au client ou au serveur dès qu’il est disponible, au lieu d’attendre que toutes les données soient disponibles.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7ffc5-108">ASP.NET Core :::no-loc(SignalR)::: prend en charge la diffusion en continu des valeurs de retour des méthodes de serveur.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-108">ASP.NET Core :::no-loc(SignalR)::: supports streaming return values of server methods.</span></span> <span data-ttu-id="7ffc5-109">Cela est utile pour les scénarios où les fragments de données arrivent dans le temps.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="7ffc5-110">Lorsqu’une valeur de retour est diffusée sur le client, chaque fragment est envoyé au client dès qu’il est disponible, au lieu d’attendre que toutes les données soient disponibles.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="7ffc5-111">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7ffc5-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="7ffc5-112">Configurer un Hub pour la diffusion en continu</span><span class="sxs-lookup"><span data-stu-id="7ffc5-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ffc5-113">Une méthode de concentrateur devient automatiquement une méthode de diffusion de concentrateur quand elle retourne <xref:System.Collections.Generic.IAsyncEnumerable`1> , <xref:System.Threading.Channels.ChannelReader%601> , `Task<IAsyncEnumerable<T>>` ou `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="7ffc5-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7ffc5-114">Une méthode de concentrateur devient automatiquement une méthode de diffusion de concentrateur lorsqu’elle retourne un <xref:System.Threading.Channels.ChannelReader%601> ou un `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="7ffc5-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="7ffc5-115">Streaming de serveur à client</span><span class="sxs-lookup"><span data-stu-id="7ffc5-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ffc5-116">Les méthodes de streaming Hub peuvent être retournées `IAsyncEnumerable<T>` en plus de `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="7ffc5-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="7ffc5-117">La façon la plus simple de revenir `IAsyncEnumerable<T>` consiste à faire de la méthode de concentrateur une méthode d’itérateur Async, comme le montre l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="7ffc5-118">Les méthodes d’itérateur Async Hub peuvent accepter un `CancellationToken` paramètre qui est déclenché lorsque le client annule son abonnement à partir du flux.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="7ffc5-119">Les méthodes d’itérateur Async évitent les problèmes courants avec les canaux, tels que le non-retour de la `ChannelReader` méthode suffisamment tôt ou la sortie de la méthode sans terminer le <xref:System.Threading.Channels.ChannelWriter`1> .</span><span class="sxs-lookup"><span data-stu-id="7ffc5-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="7ffc5-120">L’exemple suivant montre les bases de la diffusion en continu de données au client à l’aide de canaux.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="7ffc5-121">Chaque fois qu’un objet est écrit dans le <xref:System.Threading.Channels.ChannelWriter%601> , l’objet est immédiatement envoyé au client.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="7ffc5-122">À la fin, la `ChannelWriter` est terminée pour indiquer au client que le flux est fermé.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="7ffc5-123">Écrivez dans `ChannelWriter<T>` sur un thread d’arrière-plan et retournez le dès `ChannelReader` que possible.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="7ffc5-124">D’autres appels de concentrateur sont bloqués jusqu’à ce qu’un `ChannelReader` soit retourné.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="7ffc5-125">Encapsulez la logique dans une [ `try ... catch` instruction](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="7ffc5-125">Wrap logic in a [`try ... catch` statement](/dotnet/csharp/language-reference/keywords/try-catch).</span></span> <span data-ttu-id="7ffc5-126">Complétez la `Channel` dans un [ `finally` bloc](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span><span class="sxs-lookup"><span data-stu-id="7ffc5-126">Complete the `Channel` in a [`finally` block](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span></span> <span data-ttu-id="7ffc5-127">Si vous souhaitez transmettre une erreur, capturez-la à l’intérieur du `catch` bloc et écrivez-la dans le `finally` bloc.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-127">If you want to flow an error, capture it inside the `catch` block and write it in the `finally` block.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7ffc5-128">Les méthodes de diffusion en continu de serveur à client peuvent accepter un `CancellationToken` paramètre qui est déclenché lorsque le client annule son abonnement à partir du flux.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-128">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="7ffc5-129">Utilisez ce jeton pour arrêter l’opération de serveur et libérer des ressources si le client se déconnecte avant la fin du flux.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-129">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="7ffc5-130">Streaming client à serveur</span><span class="sxs-lookup"><span data-stu-id="7ffc5-130">Client-to-server streaming</span></span>

<span data-ttu-id="7ffc5-131">Une méthode de concentrateur devient automatiquement une méthode de concentrateur de streaming client à serveur quand elle accepte un ou plusieurs objets de type <xref:System.Threading.Channels.ChannelReader%601> ou <xref:System.Collections.Generic.IAsyncEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="7ffc5-131">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="7ffc5-132">L’exemple suivant montre les principes fondamentaux de la lecture des données de streaming envoyées à partir du client.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-132">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="7ffc5-133">Chaque fois que le client écrit dans le <xref:System.Threading.Channels.ChannelWriter%601> , les données sont écrites dans le `ChannelReader` sur le serveur à partir duquel la méthode de concentrateur est lue.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-133">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="7ffc5-134">Une <xref:System.Collections.Generic.IAsyncEnumerable%601> version de la méthode suit.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-134">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="7ffc5-135">Client .NET</span><span class="sxs-lookup"><span data-stu-id="7ffc5-135">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="7ffc5-136">Streaming de serveur à client</span><span class="sxs-lookup"><span data-stu-id="7ffc5-136">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ffc5-137">Les `StreamAsync` `StreamAsChannelAsync` méthodes et sur `HubConnection` sont utilisées pour appeler des méthodes de streaming de serveur à client.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-137">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="7ffc5-138">Transmettez le nom de la méthode de concentrateur et les arguments définis dans la méthode de concentrateur à `StreamAsync` ou `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="7ffc5-138">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="7ffc5-139">Le paramètre générique sur `StreamAsync<T>` et `StreamAsChannelAsync<T>` spécifie le type des objets retournés par la méthode de diffusion en continu.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-139">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="7ffc5-140">Un objet de type `IAsyncEnumerable<T>` ou `ChannelReader<T>` est retourné à partir de l’appel de flux et représente le flux sur le client.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-140">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="7ffc5-141">`StreamAsync`Exemple qui retourne `IAsyncEnumerable<int>` :</span><span class="sxs-lookup"><span data-stu-id="7ffc5-141">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

<span data-ttu-id="7ffc5-142">Exemple correspondant `StreamAsChannelAsync` qui retourne `ChannelReader<int>` :</span><span class="sxs-lookup"><span data-stu-id="7ffc5-142">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7ffc5-143">La `StreamAsChannelAsync` méthode sur `HubConnection` est utilisée pour appeler une méthode de diffusion en continu de serveur à client.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-143">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="7ffc5-144">Transmettez le nom de la méthode de concentrateur et les arguments définis dans la méthode de concentrateur à `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="7ffc5-144">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="7ffc5-145">Le paramètre générique sur `StreamAsChannelAsync<T>` spécifie le type des objets retournés par la méthode de diffusion en continu.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-145">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="7ffc5-146">Un `ChannelReader<T>` est retourné à partir de l’appel de flux et représente le flux sur le client.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-146">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="7ffc5-147">La `StreamAsChannelAsync` méthode sur `HubConnection` est utilisée pour appeler une méthode de diffusion en continu de serveur à client.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-147">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="7ffc5-148">Transmettez le nom de la méthode de concentrateur et les arguments définis dans la méthode de concentrateur à `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="7ffc5-148">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="7ffc5-149">Le paramètre générique sur `StreamAsChannelAsync<T>` spécifie le type des objets retournés par la méthode de diffusion en continu.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-149">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="7ffc5-150">Un `ChannelReader<T>` est retourné à partir de l’appel de flux et représente le flux sur le client.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-150">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="7ffc5-151">Streaming client à serveur</span><span class="sxs-lookup"><span data-stu-id="7ffc5-151">Client-to-server streaming</span></span>

<span data-ttu-id="7ffc5-152">Il existe deux façons d’appeler une méthode de diffusion en continu client à serveur à partir du client .NET.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-152">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="7ffc5-153">Vous pouvez soit passer un `IAsyncEnumerable<T>` ou un `ChannelReader` comme argument à `SendAsync` , `InvokeAsync` ou `StreamAsChannelAsync` , selon la méthode de concentrateur appelée.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-153">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="7ffc5-154">Chaque fois que des données sont écrites dans l' `IAsyncEnumerable` `ChannelWriter` objet ou, la méthode de concentrateur sur le serveur reçoit un nouvel élément avec les données du client.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-154">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="7ffc5-155">Si vous utilisez un `IAsyncEnumerable` objet, le flux se termine après la sortie de la méthode qui retourne des éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-155">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="7ffc5-156">Ou, si vous utilisez un `ChannelWriter` , vous terminez le canal avec `channel.Writer.Complete()` :</span><span class="sxs-lookup"><span data-stu-id="7ffc5-156">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="7ffc5-157">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="7ffc5-157">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="7ffc5-158">Streaming de serveur à client</span><span class="sxs-lookup"><span data-stu-id="7ffc5-158">Server-to-client streaming</span></span>

<span data-ttu-id="7ffc5-159">Les clients JavaScript appellent des méthodes de streaming de serveur à client sur des hubs avec `connection.stream` .</span><span class="sxs-lookup"><span data-stu-id="7ffc5-159">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="7ffc5-160">La `stream` méthode accepte deux arguments :</span><span class="sxs-lookup"><span data-stu-id="7ffc5-160">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="7ffc5-161">Nom de la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-161">The name of the hub method.</span></span> <span data-ttu-id="7ffc5-162">Dans l’exemple suivant, le nom de la méthode de concentrateur est `Counter` .</span><span class="sxs-lookup"><span data-stu-id="7ffc5-162">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="7ffc5-163">Arguments définis dans la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-163">Arguments defined in the hub method.</span></span> <span data-ttu-id="7ffc5-164">Dans l’exemple suivant, les arguments représentent le nombre d’éléments de flux à recevoir et le délai entre les éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-164">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="7ffc5-165">`connection.stream` retourne un `IStreamResult` , qui contient une `subscribe` méthode.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-165">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="7ffc5-166">Transmettez un `IStreamSubscriber` à `subscribe` et définissez `next` les `error` `complete` rappels, et pour recevoir des notifications de l' `stream` appel.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-166">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="7ffc5-167">Pour terminer le flux du client, appelez la `dispose` méthode sur le `ISubscription` retourné par la `subscribe` méthode.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-167">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="7ffc5-168">L’appel de cette méthode entraîne l’annulation du `CancellationToken` paramètre de la méthode de concentrateur, si vous en avez fourni un.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-168">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="7ffc5-169">Pour terminer le flux du client, appelez la `dispose` méthode sur le `ISubscription` retourné par la `subscribe` méthode.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-169">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="7ffc5-170">Streaming client à serveur</span><span class="sxs-lookup"><span data-stu-id="7ffc5-170">Client-to-server streaming</span></span>

<span data-ttu-id="7ffc5-171">Les clients JavaScript appellent des méthodes de streaming client à serveur sur des hubs en passant un `Subject` comme argument à `send` , `invoke` ou `stream` , en fonction de la méthode de concentrateur appelée.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-171">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="7ffc5-172">`Subject`Est une classe qui ressemble à un `Subject` .</span><span class="sxs-lookup"><span data-stu-id="7ffc5-172">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="7ffc5-173">Par exemple, dans RxJS, vous pouvez utiliser la classe [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) de cette bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-173">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="7ffc5-174">`subject.next(item)`L’appel de avec un élément écrit l’élément dans le flux, et la méthode de concentrateur reçoit l’élément sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-174">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="7ffc5-175">Pour terminer le flux, appelez `subject.complete()` .</span><span class="sxs-lookup"><span data-stu-id="7ffc5-175">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="7ffc5-176">Client Java</span><span class="sxs-lookup"><span data-stu-id="7ffc5-176">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="7ffc5-177">Streaming de serveur à client</span><span class="sxs-lookup"><span data-stu-id="7ffc5-177">Server-to-client streaming</span></span>

<span data-ttu-id="7ffc5-178">Le :::no-loc(SignalR)::: client Java utilise la `stream` méthode pour appeler des méthodes de diffusion en continu.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-178">The :::no-loc(SignalR)::: Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="7ffc5-179">`stream` accepte au moins trois arguments :</span><span class="sxs-lookup"><span data-stu-id="7ffc5-179">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="7ffc5-180">Type attendu des éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-180">The expected type of the stream items.</span></span>
* <span data-ttu-id="7ffc5-181">Nom de la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-181">The name of the hub method.</span></span>
* <span data-ttu-id="7ffc5-182">Arguments définis dans la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-182">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="7ffc5-183">La `stream` méthode sur `HubConnection` retourne un observable du type d’élément de flux.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-183">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="7ffc5-184">La méthode du type observable `subscribe` est where `onNext` , `onError` et les `onCompleted` gestionnaires sont définis.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-184">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

### <a name="client-to-server-streaming"></a><span data-ttu-id="7ffc5-185">Streaming client à serveur</span><span class="sxs-lookup"><span data-stu-id="7ffc5-185">Client-to-server streaming</span></span>

<span data-ttu-id="7ffc5-186">Le :::no-loc(SignalR)::: client Java peut appeler des méthodes de streaming client-serveur sur des hubs en passant un [observable](https://rxjs-dev.firebaseapp.com/api/index/class/Observable) comme argument à `send` , `invoke` ou `stream` , en fonction de la méthode de concentrateur appelée.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-186">The :::no-loc(SignalR)::: Java client can call client-to-server streaming methods on hubs by passing in an [Observable](https://rxjs-dev.firebaseapp.com/api/index/class/Observable) as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span>

```java
ReplaySubject<String> stream = ReplaySubject.create();
hubConnection.send("UploadStream", stream);
stream.onNext("FirstItem");
stream.onNext("SecondItem");
stream.onComplete();
```

<span data-ttu-id="7ffc5-187">`stream.onNext(item)`L’appel de avec un élément écrit l’élément dans le flux, et la méthode de concentrateur reçoit l’élément sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="7ffc5-187">Calling `stream.onNext(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="7ffc5-188">Pour terminer le flux, appelez `stream.onComplete()` .</span><span class="sxs-lookup"><span data-stu-id="7ffc5-188">To end the stream, call `stream.onComplete()`.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7ffc5-189">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7ffc5-189">Additional resources</span></span>

* [<span data-ttu-id="7ffc5-190">Hubs</span><span class="sxs-lookup"><span data-stu-id="7ffc5-190">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="7ffc5-191">Client .NET</span><span class="sxs-lookup"><span data-stu-id="7ffc5-191">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="7ffc5-192">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="7ffc5-192">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="7ffc5-193">Publication dans Azure</span><span class="sxs-lookup"><span data-stu-id="7ffc5-193">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
