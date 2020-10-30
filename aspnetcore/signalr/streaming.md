---
title: Utiliser la diffusion en continu dans ASP.NET Core SignalR
author: bradygaster
description: Découvrez comment diffuser en continu des données entre le client et le serveur.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 10/29/2020
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
uid: signalr/streaming
ms.openlocfilehash: b07c280f271ccdd525128b973da065001a5cf0ed
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062439"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a>Utiliser la diffusion en continu dans ASP.NET Core SignalR

Par [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR prend en charge la diffusion en continu du client vers le serveur et du serveur au client. Cela est utile pour les scénarios où les fragments de données arrivent dans le temps. Lors de la diffusion en continu, chaque fragment est envoyé au client ou au serveur dès qu’il est disponible, au lieu d’attendre que toutes les données soient disponibles.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR prend en charge la diffusion en continu des valeurs de retour des méthodes de serveur. Cela est utile pour les scénarios où les fragments de données arrivent dans le temps. Lorsqu’une valeur de retour est diffusée sur le client, chaque fragment est envoyé au client dès qu’il est disponible, au lieu d’attendre que toutes les données soient disponibles.

::: moniker-end

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Configurer un Hub pour la diffusion en continu

::: moniker range=">= aspnetcore-3.0"

Une méthode de concentrateur devient automatiquement une méthode de diffusion de concentrateur quand elle retourne <xref:System.Collections.Generic.IAsyncEnumerable`1> , <xref:System.Threading.Channels.ChannelReader%601> , `Task<IAsyncEnumerable<T>>` ou `Task<ChannelReader<T>>` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Une méthode de concentrateur devient automatiquement une méthode de diffusion de concentrateur lorsqu’elle retourne un <xref:System.Threading.Channels.ChannelReader%601> ou un `Task<ChannelReader<T>>` .

::: moniker-end

### <a name="server-to-client-streaming"></a>Streaming de serveur à client

::: moniker range=">= aspnetcore-3.0"

Les méthodes de streaming Hub peuvent être retournées `IAsyncEnumerable<T>` en plus de `ChannelReader<T>` . La façon la plus simple de revenir `IAsyncEnumerable<T>` consiste à faire de la méthode de concentrateur une méthode d’itérateur Async, comme le montre l’exemple suivant. Les méthodes d’itérateur Async Hub peuvent accepter un `CancellationToken` paramètre qui est déclenché lorsque le client annule son abonnement à partir du flux. Les méthodes d’itérateur Async évitent les problèmes courants avec les canaux, tels que le non-retour de la `ChannelReader` méthode suffisamment tôt ou la sortie de la méthode sans terminer le <xref:System.Threading.Channels.ChannelWriter`1> .

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

L’exemple suivant montre les bases de la diffusion en continu de données au client à l’aide de canaux. Chaque fois qu’un objet est écrit dans le <xref:System.Threading.Channels.ChannelWriter%601> , l’objet est immédiatement envoyé au client. À la fin, la `ChannelWriter` est terminée pour indiquer au client que le flux est fermé.

> [!NOTE]
> Écrivez dans `ChannelWriter<T>` sur un thread d’arrière-plan et retournez le dès `ChannelReader` que possible. D’autres appels de concentrateur sont bloqués jusqu’à ce qu’un `ChannelReader` soit retourné.
>
> Encapsulez la logique dans une [ `try ... catch` instruction](/dotnet/csharp/language-reference/keywords/try-catch). Complétez la `Channel` dans un [ `finally` bloc](/dotnet/csharp/language-reference/keywords/try-catch-finally). Si vous souhaitez transmettre une erreur, capturez-la à l’intérieur du `catch` bloc et écrivez-la dans le `finally` bloc.

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

Les méthodes de diffusion en continu de serveur à client peuvent accepter un `CancellationToken` paramètre qui est déclenché lorsque le client annule son abonnement à partir du flux. Utilisez ce jeton pour arrêter l’opération de serveur et libérer des ressources si le client se déconnecte avant la fin du flux.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streaming client à serveur

Une méthode de concentrateur devient automatiquement une méthode de concentrateur de streaming client à serveur quand elle accepte un ou plusieurs objets de type <xref:System.Threading.Channels.ChannelReader%601> ou <xref:System.Collections.Generic.IAsyncEnumerable%601> . L’exemple suivant montre les principes fondamentaux de la lecture des données de streaming envoyées à partir du client. Chaque fois que le client écrit dans le <xref:System.Threading.Channels.ChannelWriter%601> , les données sont écrites dans le `ChannelReader` sur le serveur à partir duquel la méthode de concentrateur est lue.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

Une <xref:System.Collections.Generic.IAsyncEnumerable%601> version de la méthode suit.

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

## <a name="net-client"></a>Client .NET

### <a name="server-to-client-streaming"></a>Streaming de serveur à client


::: moniker range=">= aspnetcore-3.0"

Les `StreamAsync` `StreamAsChannelAsync` méthodes et sur `HubConnection` sont utilisées pour appeler des méthodes de streaming de serveur à client. Transmettez le nom de la méthode de concentrateur et les arguments définis dans la méthode de concentrateur à `StreamAsync` ou `StreamAsChannelAsync` . Le paramètre générique sur `StreamAsync<T>` et `StreamAsChannelAsync<T>` spécifie le type des objets retournés par la méthode de diffusion en continu. Un objet de type `IAsyncEnumerable<T>` ou `ChannelReader<T>` est retourné à partir de l’appel de flux et représente le flux sur le client.

`StreamAsync`Exemple qui retourne `IAsyncEnumerable<int>` :

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

Exemple correspondant `StreamAsChannelAsync` qui retourne `ChannelReader<int>` :

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

La `StreamAsChannelAsync` méthode sur `HubConnection` est utilisée pour appeler une méthode de diffusion en continu de serveur à client. Transmettez le nom de la méthode de concentrateur et les arguments définis dans la méthode de concentrateur à `StreamAsChannelAsync` . Le paramètre générique sur `StreamAsChannelAsync<T>` spécifie le type des objets retournés par la méthode de diffusion en continu. Un `ChannelReader<T>` est retourné à partir de l’appel de flux et représente le flux sur le client.

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

La `StreamAsChannelAsync` méthode sur `HubConnection` est utilisée pour appeler une méthode de diffusion en continu de serveur à client. Transmettez le nom de la méthode de concentrateur et les arguments définis dans la méthode de concentrateur à `StreamAsChannelAsync` . Le paramètre générique sur `StreamAsChannelAsync<T>` spécifie le type des objets retournés par la méthode de diffusion en continu. Un `ChannelReader<T>` est retourné à partir de l’appel de flux et représente le flux sur le client.

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

### <a name="client-to-server-streaming"></a>Streaming client à serveur

Il existe deux façons d’appeler une méthode de diffusion en continu client à serveur à partir du client .NET. Vous pouvez soit passer un `IAsyncEnumerable<T>` ou un `ChannelReader` comme argument à `SendAsync` , `InvokeAsync` ou `StreamAsChannelAsync` , selon la méthode de concentrateur appelée.

Chaque fois que des données sont écrites dans l' `IAsyncEnumerable` `ChannelWriter` objet ou, la méthode de concentrateur sur le serveur reçoit un nouvel élément avec les données du client.

Si vous utilisez un `IAsyncEnumerable` objet, le flux se termine après la sortie de la méthode qui retourne des éléments de flux.

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

Ou, si vous utilisez un `ChannelWriter` , vous terminez le canal avec `channel.Writer.Complete()` :

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>Client JavaScript

### <a name="server-to-client-streaming"></a>Streaming de serveur à client

Les clients JavaScript appellent des méthodes de streaming de serveur à client sur des hubs avec `connection.stream` . La `stream` méthode accepte deux arguments :

* Nom de la méthode de concentrateur. Dans l’exemple suivant, le nom de la méthode de concentrateur est `Counter` .
* Arguments définis dans la méthode de concentrateur. Dans l’exemple suivant, les arguments représentent le nombre d’éléments de flux à recevoir et le délai entre les éléments de flux.

`connection.stream` retourne un `IStreamResult` , qui contient une `subscribe` méthode. Transmettez un `IStreamSubscriber` à `subscribe` et définissez `next` les `error` `complete` rappels, et pour recevoir des notifications de l' `stream` appel.

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

Pour terminer le flux du client, appelez la `dispose` méthode sur le `ISubscription` retourné par la `subscribe` méthode. L’appel de cette méthode entraîne l’annulation du `CancellationToken` paramètre de la méthode de concentrateur, si vous en avez fourni un.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

Pour terminer le flux du client, appelez la `dispose` méthode sur le `ISubscription` retourné par la `subscribe` méthode.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streaming client à serveur

Les clients JavaScript appellent des méthodes de streaming client à serveur sur des hubs en passant un `Subject` comme argument à `send` , `invoke` ou `stream` , en fonction de la méthode de concentrateur appelée. `Subject`Est une classe qui ressemble à un `Subject` . Par exemple, dans RxJS, vous pouvez utiliser la classe [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) de cette bibliothèque.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

`subject.next(item)`L’appel de avec un élément écrit l’élément dans le flux, et la méthode de concentrateur reçoit l’élément sur le serveur.

Pour terminer le flux, appelez `subject.complete()` .

## <a name="java-client"></a>Client Java

### <a name="server-to-client-streaming"></a>Streaming de serveur à client

Le SignalR client Java utilise la `stream` méthode pour appeler des méthodes de diffusion en continu. `stream` accepte au moins trois arguments :

* Type attendu des éléments de flux.
* Nom de la méthode de concentrateur.
* Arguments définis dans la méthode de concentrateur.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

La `stream` méthode sur `HubConnection` retourne un observable du type d’élément de flux. La méthode du type observable `subscribe` est where `onNext` , `onError` et les `onCompleted` gestionnaires sont définis.

### <a name="client-to-server-streaming"></a>Streaming client à serveur

Le SignalR client Java peut appeler des méthodes de streaming client-serveur sur des hubs en passant un [observable](https://rxjs-dev.firebaseapp.com/api/index/class/Observable) comme argument à `send` , `invoke` ou `stream` , en fonction de la méthode de concentrateur appelée.

```java
ReplaySubject<String> stream = ReplaySubject.create();
hubConnection.send("UploadStream", stream);
stream.onNext("FirstItem");
stream.onNext("SecondItem");
stream.onComplete();
```

`stream.onNext(item)`L’appel de avec un élément écrit l’élément dans le flux, et la méthode de concentrateur reçoit l’élément sur le serveur.

Pour terminer le flux, appelez `stream.onComplete()` .

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* [Hubs](xref:signalr/hubs)
* [Client .NET](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)
* [Publication dans Azure](xref:signalr/publish-to-azure-web-app)
