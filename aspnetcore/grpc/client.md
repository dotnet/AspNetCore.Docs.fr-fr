---
title: Appeler les services gRPC avec le client .NET
author: jamesnk
description: Découvrez comment appeler des services gRPC avec le client .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/18/2020
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
uid: grpc/client
ms.openlocfilehash: 39f9b3fde19e31ca970668552e5829308705f513
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97699139"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="f8112-103">Appeler les services gRPC avec le client .NET</span><span class="sxs-lookup"><span data-stu-id="f8112-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="f8112-104">Une bibliothèque cliente .NET gRPC est disponible dans le package NuGet [gRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="f8112-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="f8112-105">Ce document explique comment effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="f8112-105">This document explains how to:</span></span>

* <span data-ttu-id="f8112-106">Configurez un client gRPC pour appeler les services gRPC.</span><span class="sxs-lookup"><span data-stu-id="f8112-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="f8112-107">Effectuez des appels gRPC à des méthodes unaire, de streaming de serveur, de diffusion de client et de diffusion bidirectionnelle.</span><span class="sxs-lookup"><span data-stu-id="f8112-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="f8112-108">Configurer le client gRPC</span><span class="sxs-lookup"><span data-stu-id="f8112-108">Configure gRPC client</span></span>

<span data-ttu-id="f8112-109">les clients gRPC sont des types de client concrets qui sont [générés à partir de fichiers *\* . proto*](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="f8112-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="f8112-110">Le client gRPC concret possède des méthodes qui traduisent le service gRPC dans le fichier *\* . proto* .</span><span class="sxs-lookup"><span data-stu-id="f8112-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span> <span data-ttu-id="f8112-111">Par exemple, un service appelé `Greeter` génère un `GreeterClient` type avec des méthodes pour appeler le service.</span><span class="sxs-lookup"><span data-stu-id="f8112-111">For example, a service called `Greeter` generates a `GreeterClient` type with methods to call the service.</span></span>

<span data-ttu-id="f8112-112">Un client gRPC est créé à partir d’un canal.</span><span class="sxs-lookup"><span data-stu-id="f8112-112">A gRPC client is created from a channel.</span></span> <span data-ttu-id="f8112-113">Commencez par utiliser `GrpcChannel.ForAddress` pour créer un canal, puis utilisez le canal pour créer un client gRPC :</span><span class="sxs-lookup"><span data-stu-id="f8112-113">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="f8112-114">Un canal représente une connexion à long terme à un service gRPC.</span><span class="sxs-lookup"><span data-stu-id="f8112-114">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="f8112-115">Lorsqu’un canal est créé, il est configuré avec les options relatives à l’appel d’un service.</span><span class="sxs-lookup"><span data-stu-id="f8112-115">When a channel is created, it's configured with options related to calling a service.</span></span> <span data-ttu-id="f8112-116">Par exemple, le `HttpClient` utilisé pour effectuer des appels, la taille maximale des messages d’envoi et de réception, et la journalisation peuvent être spécifiés sur `GrpcChannelOptions` et utilisés avec `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="f8112-116">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="f8112-117">Pour obtenir la liste complète des options, consultez [options de configuration du client](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="f8112-117">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="f8112-118">Configurer TLS</span><span class="sxs-lookup"><span data-stu-id="f8112-118">Configure TLS</span></span>

<span data-ttu-id="f8112-119">Un client gRPC doit utiliser la même sécurité au niveau de la connexion que le service appelé.</span><span class="sxs-lookup"><span data-stu-id="f8112-119">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="f8112-120">gRPC client Transport Layer Security (TLS) est configuré lors de la création du canal gRPC.</span><span class="sxs-lookup"><span data-stu-id="f8112-120">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="f8112-121">Un client gRPC génère une erreur lorsqu’il appelle un service et que la sécurité au niveau de la connexion du canal et du service ne correspond pas.</span><span class="sxs-lookup"><span data-stu-id="f8112-121">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="f8112-122">Pour configurer un canal gRPC pour utiliser TLS, assurez-vous que l’adresse du serveur commence par `https` .</span><span class="sxs-lookup"><span data-stu-id="f8112-122">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="f8112-123">Par exemple, `GrpcChannel.ForAddress("https://localhost:5001")` utilise le protocole HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f8112-123">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="f8112-124">Le canal gRPC négocie automatiquement une connexion sécurisée par TLS et utilise une connexion sécurisée pour effectuer des appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="f8112-124">The gRPC channel automatically negotiates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="f8112-125">gRPC prend en charge l’authentification par certificat client sur TLS.</span><span class="sxs-lookup"><span data-stu-id="f8112-125">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="f8112-126">Pour plus d’informations sur la configuration des certificats clients avec un canal gRPC, consultez <xref:grpc/authn-and-authz#client-certificate-authentication> .</span><span class="sxs-lookup"><span data-stu-id="f8112-126">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="f8112-127">Pour appeler des services gRPC non sécurisés, assurez-vous que l’adresse du serveur commence par `http` .</span><span class="sxs-lookup"><span data-stu-id="f8112-127">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="f8112-128">Par exemple, `GrpcChannel.ForAddress("http://localhost:5000")` utilise le protocole http.</span><span class="sxs-lookup"><span data-stu-id="f8112-128">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="f8112-129">Dans .NET Core 3,1 ou version ultérieure, une configuration supplémentaire est requise pour [appeler des services gRPC non sécurisés avec le client .net](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="f8112-129">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="f8112-130">Performances du client</span><span class="sxs-lookup"><span data-stu-id="f8112-130">Client performance</span></span>

<span data-ttu-id="f8112-131">Performances et utilisation des canaux et des clients :</span><span class="sxs-lookup"><span data-stu-id="f8112-131">Channel and client performance and usage:</span></span>

* <span data-ttu-id="f8112-132">La création d’un canal peut être une opération coûteuse.</span><span class="sxs-lookup"><span data-stu-id="f8112-132">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="f8112-133">La réutilisation d’un canal pour les appels gRPC offre des avantages en matière de performances.</span><span class="sxs-lookup"><span data-stu-id="f8112-133">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="f8112-134">les clients gRPC sont créés avec des canaux.</span><span class="sxs-lookup"><span data-stu-id="f8112-134">gRPC clients are created with channels.</span></span> <span data-ttu-id="f8112-135">les clients gRPC sont des objets légers et n’ont pas besoin d’être mis en cache ou réutilisés.</span><span class="sxs-lookup"><span data-stu-id="f8112-135">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="f8112-136">Plusieurs clients gRPC peuvent être créés à partir d’un canal, y compris différents types de clients.</span><span class="sxs-lookup"><span data-stu-id="f8112-136">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="f8112-137">Un canal et des clients créés à partir du canal peuvent être utilisés en toute sécurité par plusieurs threads.</span><span class="sxs-lookup"><span data-stu-id="f8112-137">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="f8112-138">Les clients créés à partir du canal peuvent effectuer plusieurs appels simultanés.</span><span class="sxs-lookup"><span data-stu-id="f8112-138">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="f8112-139">`GrpcChannel.ForAddress` n’est pas la seule option pour créer un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="f8112-139">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="f8112-140">Si vous appelez gRPC services à partir d’une application ASP.NET Core, envisagez l’intégration de la [fabrique de clients gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="f8112-140">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="f8112-141">l’intégration de gRPC avec `HttpClientFactory` offre une alternative centralisée à la création de clients gRPC.</span><span class="sxs-lookup"><span data-stu-id="f8112-141">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="f8112-142">L’appel de gRPC sur HTTP/2 avec `Grpc.Net.Client` n’est pas pris en charge actuellement sur Xamarin.</span><span class="sxs-lookup"><span data-stu-id="f8112-142">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="f8112-143">Nous nous efforçons d’améliorer la prise en charge de HTTP/2 dans une prochaine version de Xamarin.</span><span class="sxs-lookup"><span data-stu-id="f8112-143">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="f8112-144">[GRPC. Core](https://www.nuget.org/packages/Grpc.Core) et [GRPC-Web](xref:grpc/browser) sont des alternatives viables qui fonctionnent aujourd’hui.</span><span class="sxs-lookup"><span data-stu-id="f8112-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="f8112-145">Effectuer des appels gRPC</span><span class="sxs-lookup"><span data-stu-id="f8112-145">Make gRPC calls</span></span>

<span data-ttu-id="f8112-146">Un appel gRPC est initié par l’appel d’une méthode sur le client.</span><span class="sxs-lookup"><span data-stu-id="f8112-146">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="f8112-147">Le client gRPC gère la sérialisation des messages et traite l’appel gRPC au service approprié.</span><span class="sxs-lookup"><span data-stu-id="f8112-147">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="f8112-148">gRPC a différents types de méthodes.</span><span class="sxs-lookup"><span data-stu-id="f8112-148">gRPC has different types of methods.</span></span> <span data-ttu-id="f8112-149">La façon dont le client est utilisé pour effectuer un appel gRPC dépend du type de méthode appelé.</span><span class="sxs-lookup"><span data-stu-id="f8112-149">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="f8112-150">Les types de méthode gRPC sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="f8112-150">The gRPC method types are:</span></span>

* <span data-ttu-id="f8112-151">Unaire</span><span class="sxs-lookup"><span data-stu-id="f8112-151">Unary</span></span>
* <span data-ttu-id="f8112-152">Streaming de serveur</span><span class="sxs-lookup"><span data-stu-id="f8112-152">Server streaming</span></span>
* <span data-ttu-id="f8112-153">Diffusion en continu du client</span><span class="sxs-lookup"><span data-stu-id="f8112-153">Client streaming</span></span>
* <span data-ttu-id="f8112-154">Streaming bidirectionnel</span><span class="sxs-lookup"><span data-stu-id="f8112-154">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="f8112-155">Appel unaire</span><span class="sxs-lookup"><span data-stu-id="f8112-155">Unary call</span></span>

<span data-ttu-id="f8112-156">Un appel unaire commence par le client envoyant un message de demande.</span><span class="sxs-lookup"><span data-stu-id="f8112-156">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="f8112-157">Un message de réponse est retourné lorsque le service se termine.</span><span class="sxs-lookup"><span data-stu-id="f8112-157">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="f8112-158">Chaque méthode de service unaire du fichier *\* . proto* se traduira par deux méthodes .net sur le type de client gRPC concret pour l’appel de la méthode : une méthode asynchrone et une méthode de blocage.</span><span class="sxs-lookup"><span data-stu-id="f8112-158">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="f8112-159">Par exemple, `GreeterClient` il existe deux façons d’appeler `SayHello` :</span><span class="sxs-lookup"><span data-stu-id="f8112-159">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="f8112-160">`GreeterClient.SayHelloAsync` -appelle le `Greeter.SayHello` service de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="f8112-160">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="f8112-161">Peut être attendu.</span><span class="sxs-lookup"><span data-stu-id="f8112-161">Can be awaited.</span></span>
* <span data-ttu-id="f8112-162">`GreeterClient.SayHello` -appelle le `Greeter.SayHello` service et bloque jusqu’à la fin.</span><span class="sxs-lookup"><span data-stu-id="f8112-162">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="f8112-163">N’utilisez pas dans le code asynchrone.</span><span class="sxs-lookup"><span data-stu-id="f8112-163">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="f8112-164">Appel de streaming de serveur</span><span class="sxs-lookup"><span data-stu-id="f8112-164">Server streaming call</span></span>

<span data-ttu-id="f8112-165">Un appel de streaming de serveur commence par le client qui envoie un message de demande.</span><span class="sxs-lookup"><span data-stu-id="f8112-165">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="f8112-166">`ResponseStream.MoveNext()` lit les messages diffusés en continu à partir du service.</span><span class="sxs-lookup"><span data-stu-id="f8112-166">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="f8112-167">L’appel de streaming de serveur est terminé lorsque `ResponseStream.MoveNext()` retourne `false` .</span><span class="sxs-lookup"><span data-stu-id="f8112-167">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="f8112-168">Si vous utilisez C# 8 ou une version ultérieure, la `await foreach` syntaxe peut être utilisée pour lire les messages.</span><span class="sxs-lookup"><span data-stu-id="f8112-168">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="f8112-169">La `IAsyncStreamReader<T>.ReadAllAsync()` méthode d’extension lit tous les messages du flux de réponse :</span><span class="sxs-lookup"><span data-stu-id="f8112-169">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="f8112-170">Appel de diffusion en continu du client</span><span class="sxs-lookup"><span data-stu-id="f8112-170">Client streaming call</span></span>

<span data-ttu-id="f8112-171">Un appel de diffusion en continu du client démarre *sans* que le client envoie un message.</span><span class="sxs-lookup"><span data-stu-id="f8112-171">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="f8112-172">Le client peut choisir d’envoyer des messages avec `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="f8112-172">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="f8112-173">Lorsque le client a terminé l’envoi des messages, `RequestStream.CompleteAsync()` doit être appelé pour notifier le service.</span><span class="sxs-lookup"><span data-stu-id="f8112-173">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="f8112-174">L’appel est terminé lorsque le service renvoie un message de réponse.</span><span class="sxs-lookup"><span data-stu-id="f8112-174">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="f8112-175">Appel de streaming bidirectionnel</span><span class="sxs-lookup"><span data-stu-id="f8112-175">Bi-directional streaming call</span></span>

<span data-ttu-id="f8112-176">Un appel de streaming bidirectionnel démarre *sans* que le client envoie un message.</span><span class="sxs-lookup"><span data-stu-id="f8112-176">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="f8112-177">Le client peut choisir d’envoyer des messages avec `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="f8112-177">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="f8112-178">Les messages transmis en continu à partir du service sont accessibles avec `ResponseStream.MoveNext()` ou `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="f8112-178">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="f8112-179">L’appel de streaming bidirectionnel est terminé lorsque le n' `ResponseStream` a plus de messages.</span><span class="sxs-lookup"><span data-stu-id="f8112-179">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

<span data-ttu-id="f8112-180">Pour des performances optimales et pour éviter les erreurs inutiles dans le client et le service, essayez d’effectuer des appels de diffusion en continu bidirectionnelle de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="f8112-180">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="f8112-181">Un appel bidirectionnel se termine correctement lorsque le serveur a fini de lire le flux de requête et que le client a terminé la lecture du flux de réponse.</span><span class="sxs-lookup"><span data-stu-id="f8112-181">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="f8112-182">L’exemple d’appel précédent est un exemple d’appel bidirectionnel qui se termine normalement.</span><span class="sxs-lookup"><span data-stu-id="f8112-182">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="f8112-183">Dans l’appel, le client :</span><span class="sxs-lookup"><span data-stu-id="f8112-183">In the call, the client:</span></span>

1. <span data-ttu-id="f8112-184">Démarre un nouvel appel de streaming bidirectionnel en appelant `EchoClient.Echo` .</span><span class="sxs-lookup"><span data-stu-id="f8112-184">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="f8112-185">Crée une tâche en arrière-plan pour lire les messages du service à l’aide de `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="f8112-185">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="f8112-186">Envoie des messages au serveur avec `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="f8112-186">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="f8112-187">Indique au serveur qu’il a terminé d’envoyer des messages avec `RequestStream.CompleteAsync()` .</span><span class="sxs-lookup"><span data-stu-id="f8112-187">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="f8112-188">Attend que la tâche en arrière-plan ait lu tous les messages entrants.</span><span class="sxs-lookup"><span data-stu-id="f8112-188">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="f8112-189">Pendant un appel de streaming bidirectionnel, le client et le service peuvent envoyer des messages entre eux à tout moment.</span><span class="sxs-lookup"><span data-stu-id="f8112-189">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="f8112-190">La meilleure logique cliente pour interagir avec un appel bidirectionnel varie en fonction de la logique du service.</span><span class="sxs-lookup"><span data-stu-id="f8112-190">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-headers"></a><span data-ttu-id="f8112-191">Accéder aux en-têtes gRPC</span><span class="sxs-lookup"><span data-stu-id="f8112-191">Access gRPC headers</span></span>

<span data-ttu-id="f8112-192">les appels gRPC retournent des en-têtes de réponse.</span><span class="sxs-lookup"><span data-stu-id="f8112-192">gRPC calls return response headers.</span></span> <span data-ttu-id="f8112-193">Les en-têtes de réponse HTTP transmettent les métadonnées de nom/valeur relatives à un appel qui n’est pas lié au message retourné.</span><span class="sxs-lookup"><span data-stu-id="f8112-193">HTTP response headers pass name/value metadata about a call that isn't related the returned message.</span></span>

<span data-ttu-id="f8112-194">Les en-têtes sont accessibles à l’aide de `ResponseHeadersAsync` , qui retourne une collection de métadonnées.</span><span class="sxs-lookup"><span data-stu-id="f8112-194">Headers are accessible using `ResponseHeadersAsync`, which returns a collection of metadata.</span></span> <span data-ttu-id="f8112-195">Les en-têtes sont généralement retournés avec le message de réponse ; par conséquent, vous devez les attendre.</span><span class="sxs-lookup"><span data-stu-id="f8112-195">Headers are typically returned with the response message; therefore, you must await them.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });

var headers = await call.ResponseHeadersAsync;
var myValue = headers.GetValue("my-trailer-name");

var response = await call.ResponseAsync;
```

<span data-ttu-id="f8112-196">`ResponseHeadersAsync` syntaxe</span><span class="sxs-lookup"><span data-stu-id="f8112-196">`ResponseHeadersAsync` usage:</span></span>

* <span data-ttu-id="f8112-197">Doit attendre le résultat de `ResponseHeadersAsync` pour obtenir la collection d’en-têtes.</span><span class="sxs-lookup"><span data-stu-id="f8112-197">Must await the result of `ResponseHeadersAsync` to get the headers collection.</span></span>
* <span data-ttu-id="f8112-198">Ne doit pas être accessible avant `ResponseAsync` (ou le flux de réponse lors de la diffusion en continu).</span><span class="sxs-lookup"><span data-stu-id="f8112-198">Doesn't have to be accessed before `ResponseAsync` (or the response stream when streaming).</span></span> <span data-ttu-id="f8112-199">Si une réponse a été retournée, `ResponseHeadersAsync` retourne des en-têtes instantanément.</span><span class="sxs-lookup"><span data-stu-id="f8112-199">If a response has been returned, then `ResponseHeadersAsync` returns headers instantly.</span></span>
* <span data-ttu-id="f8112-200">Lèvera une exception en cas d’erreur de connexion ou de serveur et si les en-têtes n’ont pas été retournés pour l’appel gRPC.</span><span class="sxs-lookup"><span data-stu-id="f8112-200">Will throw an exception if there was a connection or server error and headers weren't returned for the gRPC call.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="f8112-201">Accéder aux codes de fin gRPC</span><span class="sxs-lookup"><span data-stu-id="f8112-201">Access gRPC trailers</span></span>

<span data-ttu-id="f8112-202">les appels gRPC peuvent retourner des codes de fin de réponse.</span><span class="sxs-lookup"><span data-stu-id="f8112-202">gRPC calls may return response trailers.</span></span> <span data-ttu-id="f8112-203">Les codes de fin sont utilisés pour fournir les métadonnées nom/valeur relatives à un appel.</span><span class="sxs-lookup"><span data-stu-id="f8112-203">Trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="f8112-204">Les codes de fin offrent des fonctionnalités similaires aux en-têtes HTTP, mais sont reçus à la fin de l’appel.</span><span class="sxs-lookup"><span data-stu-id="f8112-204">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="f8112-205">Les codes de fin sont accessibles à l’aide de `GetTrailers()` , qui retourne une collection de métadonnées.</span><span class="sxs-lookup"><span data-stu-id="f8112-205">Trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="f8112-206">Les codes de fin sont retournés une fois la réponse terminée.</span><span class="sxs-lookup"><span data-stu-id="f8112-206">Trailers are returned after the response is complete.</span></span> <span data-ttu-id="f8112-207">Par conséquent, vous devez attendre tous les messages de réponse avant d’accéder aux codes de fin.</span><span class="sxs-lookup"><span data-stu-id="f8112-207">Therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="f8112-208">Les appels unaires et de streaming client doivent attendre `ResponseAsync` avant d’appeler `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="f8112-208">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="f8112-209">Les appels de diffusion en continu bidirectionnelle et serveur doivent finir par attendre le flux de réponse avant d’appeler `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="f8112-209">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="f8112-210">Les codes de fin sont également accessibles à partir de `RpcException` .</span><span class="sxs-lookup"><span data-stu-id="f8112-210">Trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="f8112-211">Un service peut retourner des codes de fin avec un État gRPC non OK.</span><span class="sxs-lookup"><span data-stu-id="f8112-211">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="f8112-212">Dans ce cas, les codes de fin sont récupérés à partir de l’exception levée par le client gRPC :</span><span class="sxs-lookup"><span data-stu-id="f8112-212">In this situation, the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="configure-deadline"></a><span data-ttu-id="f8112-213">Configurer l’échéance</span><span class="sxs-lookup"><span data-stu-id="f8112-213">Configure deadline</span></span>

<span data-ttu-id="f8112-214">La configuration d’une échéance d’appel gRPC est recommandée, car elle fournit une limite supérieure à la durée pendant laquelle un appel peut s’exécuter.</span><span class="sxs-lookup"><span data-stu-id="f8112-214">Configuring a gRPC call deadline is recommended because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="f8112-215">Il empêche les services incorrects de s’exécuter de façon permanente et d’épuiser les ressources du serveur.</span><span class="sxs-lookup"><span data-stu-id="f8112-215">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="f8112-216">Les échéances sont un outil utile pour créer des applications fiables.</span><span class="sxs-lookup"><span data-stu-id="f8112-216">Deadlines are a useful tool for building reliable apps.</span></span>

<span data-ttu-id="f8112-217">Configurez `CallOptions.Deadline` pour définir une échéance pour un appel gRPC :</span><span class="sxs-lookup"><span data-stu-id="f8112-217">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="f8112-218">Pour plus d’informations, consultez <xref:grpc/deadlines-cancellation#deadlines>.</span><span class="sxs-lookup"><span data-stu-id="f8112-218">For more information, see <xref:grpc/deadlines-cancellation#deadlines>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8112-219">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f8112-219">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
