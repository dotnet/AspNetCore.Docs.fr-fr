---
title: Créer des services et méthodes gRPC
author: jamesnk
description: Découvrez comment créer des services et méthodes gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
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
uid: grpc/services
ms.openlocfilehash: c4d37556a3345d275f45c537a40908c5966fe015
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113619"
---
# <a name="create-grpc-services-and-methods"></a><span data-ttu-id="10fec-103">Créer des services et méthodes gRPC</span><span class="sxs-lookup"><span data-stu-id="10fec-103">Create gRPC services and methods</span></span>

<span data-ttu-id="10fec-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="10fec-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="10fec-105">Ce document explique comment créer des services et des méthodes gRPC en C#.</span><span class="sxs-lookup"><span data-stu-id="10fec-105">This document explains how to create gRPC services and methods in C#.</span></span> <span data-ttu-id="10fec-106">Les sujets abordés sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="10fec-106">Topics include:</span></span>

* <span data-ttu-id="10fec-107">Comment définir des services et des méthodes dans des fichiers *. proto* .</span><span class="sxs-lookup"><span data-stu-id="10fec-107">How to define services and methods in *.proto* files.</span></span>
* <span data-ttu-id="10fec-108">Code généré à l’aide des outils C# gRPC.</span><span class="sxs-lookup"><span data-stu-id="10fec-108">Generated code using gRPC C# tooling.</span></span>
* <span data-ttu-id="10fec-109">Implémentation des méthodes et des services gRPC.</span><span class="sxs-lookup"><span data-stu-id="10fec-109">Implementing gRPC services and methods.</span></span>

## <a name="create-new-grpc-services"></a><span data-ttu-id="10fec-110">Créer de nouveaux services gRPC</span><span class="sxs-lookup"><span data-stu-id="10fec-110">Create new gRPC services</span></span>

<span data-ttu-id="10fec-111">[les services gRPC avec C# ont](xref:grpc/basics) introduit la première approche « Contract-First » de gRPC pour le développement d’API.</span><span class="sxs-lookup"><span data-stu-id="10fec-111">[gRPC services with C#](xref:grpc/basics) introduced gRPC's contract-first approach to API development.</span></span> <span data-ttu-id="10fec-112">Les services et les messages sont définis dans les fichiers *. proto* .</span><span class="sxs-lookup"><span data-stu-id="10fec-112">Services and messages are defined in *.proto* files.</span></span> <span data-ttu-id="10fec-113">Les outils C# génèrent ensuite du code à partir de fichiers *. proto* .</span><span class="sxs-lookup"><span data-stu-id="10fec-113">C# tooling then generates code from *.proto* files.</span></span> <span data-ttu-id="10fec-114">Pour les ressources côté serveur, un type de base abstrait est généré pour chaque service, ainsi que des classes pour tous les messages.</span><span class="sxs-lookup"><span data-stu-id="10fec-114">For server-side assets, an abstract base type is generated for each service, along with classes for any messages.</span></span>

<span data-ttu-id="10fec-115">Le fichier *. proto* suivant :</span><span class="sxs-lookup"><span data-stu-id="10fec-115">The following *.proto* file:</span></span>

* <span data-ttu-id="10fec-116">Définit un `Greeter` service.</span><span class="sxs-lookup"><span data-stu-id="10fec-116">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="10fec-117">Le `Greeter` service définit un `SayHello` appel.</span><span class="sxs-lookup"><span data-stu-id="10fec-117">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="10fec-118">`SayHello` envoie un `HelloRequest` message et reçoit un `HelloReply` message</span><span class="sxs-lookup"><span data-stu-id="10fec-118">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message</span></span>

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="10fec-119">Les outils c# génèrent le `GreeterBase` type de base c# :</span><span class="sxs-lookup"><span data-stu-id="10fec-119">C# tooling generates the C# `GreeterBase` base type:</span></span>

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="10fec-120">Par défaut, le généré `GreeterBase` ne fait rien.</span><span class="sxs-lookup"><span data-stu-id="10fec-120">By default the generated `GreeterBase` doesn't do anything.</span></span> <span data-ttu-id="10fec-121">Sa `SayHello` méthode virtuelle renverra une `UNIMPLEMENTED` erreur aux clients qui l’appellent.</span><span class="sxs-lookup"><span data-stu-id="10fec-121">Its virtual `SayHello` method will return an `UNIMPLEMENTED` error to any clients that call it.</span></span> <span data-ttu-id="10fec-122">Pour que le service soit utile, une application doit créer une implémentation concrète de `GreeterBase` :</span><span class="sxs-lookup"><span data-stu-id="10fec-122">For the service to be useful an app must create a concrete implementation of `GreeterBase`:</span></span>

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

<span data-ttu-id="10fec-123">L’implémentation du service est inscrite auprès de l’application.</span><span class="sxs-lookup"><span data-stu-id="10fec-123">The service implementation is registered with the app.</span></span> <span data-ttu-id="10fec-124">Si le service est hébergé par ASP.NET Core gRPC, il doit être ajouté au pipeline de routage à l’aide de la `MapGrpcService` méthode.</span><span class="sxs-lookup"><span data-stu-id="10fec-124">If the service is hosted by ASP.NET Core gRPC, it should be added to the routing pipeline with the `MapGrpcService` method.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="10fec-125">Consultez la rubrique <xref:grpc/aspnetcore> (éventuellement en anglais) pour plus d'informations.</span><span class="sxs-lookup"><span data-stu-id="10fec-125">See <xref:grpc/aspnetcore> for more information.</span></span>

## <a name="implement-grpc-methods"></a><span data-ttu-id="10fec-126">Implémenter des méthodes gRPC</span><span class="sxs-lookup"><span data-stu-id="10fec-126">Implement gRPC methods</span></span>

<span data-ttu-id="10fec-127">Un service gRPC peut avoir différents types de méthodes.</span><span class="sxs-lookup"><span data-stu-id="10fec-127">A gRPC service can have different types of methods.</span></span> <span data-ttu-id="10fec-128">La façon dont les messages sont envoyés et reçus par un service dépend du type de méthode défini.</span><span class="sxs-lookup"><span data-stu-id="10fec-128">How messages are sent and received by a service depends on the type of method defined.</span></span> <span data-ttu-id="10fec-129">Les types de méthode gRPC sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="10fec-129">The gRPC method types are:</span></span>

* <span data-ttu-id="10fec-130">Unaire</span><span class="sxs-lookup"><span data-stu-id="10fec-130">Unary</span></span>
* <span data-ttu-id="10fec-131">Streaming de serveur</span><span class="sxs-lookup"><span data-stu-id="10fec-131">Server streaming</span></span>
* <span data-ttu-id="10fec-132">Diffusion en continu du client</span><span class="sxs-lookup"><span data-stu-id="10fec-132">Client streaming</span></span>
* <span data-ttu-id="10fec-133">Streaming bidirectionnel</span><span class="sxs-lookup"><span data-stu-id="10fec-133">Bi-directional streaming</span></span>

<span data-ttu-id="10fec-134">Les appels de diffusion en continu sont spécifiés avec le `stream` mot clé dans le fichier *. proto* .</span><span class="sxs-lookup"><span data-stu-id="10fec-134">Streaming calls are specified with the `stream` keyword in the *.proto* file.</span></span> <span data-ttu-id="10fec-135">`stream` peut être placé sur le message de demande d’un appel, sur un message de réponse, ou sur les deux.</span><span class="sxs-lookup"><span data-stu-id="10fec-135">`stream` can be placed on a call's request message, response message, or both.</span></span>

```protobuf
syntax = "proto3";

service ExampleService {
  // Unary
  rpc UnaryCall (ExampleRequest) returns (ExampleResponse);

  // Server streaming
  rpc StreamingFromServer (ExampleRequest) returns (stream ExampleResponse);

  // Client streaming
  rpc StreamingFromClient (stream ExampleRequest) returns (ExampleResponse);

  // Bi-directional streaming
  rpc StreamingBothWays (stream ExampleRequest) returns (stream ExampleResponse);
}
```

<span data-ttu-id="10fec-136">Chaque type d’appel a une signature de méthode différente.</span><span class="sxs-lookup"><span data-stu-id="10fec-136">Each call type has a different method signature.</span></span> <span data-ttu-id="10fec-137">La substitution de méthodes générées à partir du type de service de base abstrait dans une implémentation concrète garantit que les arguments et le type de retour corrects sont utilisés.</span><span class="sxs-lookup"><span data-stu-id="10fec-137">Overriding generated methods from the abstract base service type in a concrete implementation ensures the correct arguments and return type are used.</span></span>

### <a name="unary-method"></a><span data-ttu-id="10fec-138">Unaire, méthode</span><span class="sxs-lookup"><span data-stu-id="10fec-138">Unary method</span></span>

<span data-ttu-id="10fec-139">Une méthode unaire obtient le message de requête en tant que paramètre et retourne la réponse.</span><span class="sxs-lookup"><span data-stu-id="10fec-139">A unary method gets the request message as a parameter, and returns the response.</span></span> <span data-ttu-id="10fec-140">Un appel unaire est terminé lorsque la réponse est retournée.</span><span class="sxs-lookup"><span data-stu-id="10fec-140">A unary call is complete when the response is returned.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

<span data-ttu-id="10fec-141">Les appels unaires sont les plus similaires aux [actions sur les contrôleurs d’API Web](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="10fec-141">Unary calls are the most similar to [actions on web API controllers](xref:web-api/index).</span></span> <span data-ttu-id="10fec-142">Une différence importante entre les méthodes gRPC et les actions est que les méthodes gRPC ne sont pas en mesure de lier des parties d’une requête à différents arguments de méthode.</span><span class="sxs-lookup"><span data-stu-id="10fec-142">One important difference gRPC methods have from actions is gRPC methods are not able to bind parts of a request to different method arguments.</span></span> <span data-ttu-id="10fec-143">les méthodes gRPC ont toujours un argument de message pour les données de la demande entrante.</span><span class="sxs-lookup"><span data-stu-id="10fec-143">gRPC methods always have one message argument for the incoming request data.</span></span> <span data-ttu-id="10fec-144">Il est toujours possible d’envoyer plusieurs valeurs à un service gRPC en les mettant à la disposition des champs du message de demande :</span><span class="sxs-lookup"><span data-stu-id="10fec-144">Multiple values can still be sent to a gRPC service by making them fields on the request message:</span></span>

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a><span data-ttu-id="10fec-145">Méthode de diffusion en continu du serveur</span><span class="sxs-lookup"><span data-stu-id="10fec-145">Server streaming method</span></span>

<span data-ttu-id="10fec-146">Une méthode de diffusion en continu du serveur obtient le message de requête en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="10fec-146">A server streaming method gets the request message as a parameter.</span></span> <span data-ttu-id="10fec-147">Étant donné que plusieurs messages peuvent être transmis en continu à l’appelant, `responseStream.WriteAsync` est utilisé pour envoyer des messages de réponse.</span><span class="sxs-lookup"><span data-stu-id="10fec-147">Because multiple messages can be streamed back to the caller, `responseStream.WriteAsync` is used to send response messages.</span></span> <span data-ttu-id="10fec-148">Un appel de streaming de serveur est terminé lorsque la méthode est retournée.</span><span class="sxs-lookup"><span data-stu-id="10fec-148">A server streaming call is complete when the method returns.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    for (var i = 0; i < 5; i++)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1));
    }
}
```

<span data-ttu-id="10fec-149">Le client n’a aucun moyen d’envoyer des messages ou des données supplémentaires une fois que la méthode de diffusion en continu du serveur a démarré.</span><span class="sxs-lookup"><span data-stu-id="10fec-149">The client has no way to send additional messages or data once the server streaming method has started.</span></span> <span data-ttu-id="10fec-150">Certaines méthodes de streaming sont conçues pour s’exécuter indéfiniment.</span><span class="sxs-lookup"><span data-stu-id="10fec-150">Some streaming methods are designed to run forever.</span></span> <span data-ttu-id="10fec-151">Pour les méthodes de streaming continues, un client peut annuler l’appel lorsqu’il n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="10fec-151">For continuous streaming methods, a client can cancel the call when it's no longer needed.</span></span> <span data-ttu-id="10fec-152">Lorsque l’annulation se produit, le client envoie un signal au serveur et [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) est déclenché.</span><span class="sxs-lookup"><span data-stu-id="10fec-152">When cancellation happens the client sends a signal to the server and the [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="10fec-153">Le `CancellationToken` jeton doit être utilisé sur le serveur avec les méthodes Async afin que :</span><span class="sxs-lookup"><span data-stu-id="10fec-153">The `CancellationToken` token should be used on the server with async methods so that:</span></span>

* <span data-ttu-id="10fec-154">Tout travail asynchrone est annulé avec l’appel de diffusion en continu.</span><span class="sxs-lookup"><span data-stu-id="10fec-154">Any asynchronous work is canceled together with the streaming call.</span></span>
* <span data-ttu-id="10fec-155">La méthode s’arrête rapidement.</span><span class="sxs-lookup"><span data-stu-id="10fec-155">The method exits quickly.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

### <a name="client-streaming-method"></a><span data-ttu-id="10fec-156">Méthode de diffusion en continu du client</span><span class="sxs-lookup"><span data-stu-id="10fec-156">Client streaming method</span></span>

<span data-ttu-id="10fec-157">Une méthode de diffusion en continu du client démarre sans que la méthode *ne* reçoive un message.</span><span class="sxs-lookup"><span data-stu-id="10fec-157">A client streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="10fec-158">Le `requestStream` paramètre est utilisé pour lire les messages du client.</span><span class="sxs-lookup"><span data-stu-id="10fec-158">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="10fec-159">Un appel de diffusion en continu du client est terminé lorsqu’un message de réponse est retourné :</span><span class="sxs-lookup"><span data-stu-id="10fec-159">A client streaming call is complete when a response message is returned:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    while (await requestStream.MoveNext())
    {
        var message = requestStream.Current;
        // ...
    }
    return new ExampleResponse();
}
```

<span data-ttu-id="10fec-160">Si vous utilisez C# 8 ou une version ultérieure, la `await foreach` syntaxe peut être utilisée pour lire les messages.</span><span class="sxs-lookup"><span data-stu-id="10fec-160">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="10fec-161">La `IAsyncStreamReader<T>.ReadAllAsync()` méthode d’extension lit tous les messages du flux de requête :</span><span class="sxs-lookup"><span data-stu-id="10fec-161">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the request stream:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        // ...
    }
    return new ExampleResponse();
}
```

### <a name="bi-directional-streaming-method"></a><span data-ttu-id="10fec-162">Méthode de diffusion bidirectionnelle</span><span class="sxs-lookup"><span data-stu-id="10fec-162">Bi-directional streaming method</span></span>

<span data-ttu-id="10fec-163">Une méthode de diffusion en continu bidirectionnelle démarre *sans* la méthode qui reçoit un message.</span><span class="sxs-lookup"><span data-stu-id="10fec-163">A bi-directional streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="10fec-164">Le `requestStream` paramètre est utilisé pour lire les messages du client.</span><span class="sxs-lookup"><span data-stu-id="10fec-164">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="10fec-165">La méthode peut choisir d’envoyer des messages avec `responseStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="10fec-165">The method can choose to send messages with `responseStream.WriteAsync`.</span></span> <span data-ttu-id="10fec-166">Un appel de streaming bidirectionnel est terminé lorsque la méthode retourne :</span><span class="sxs-lookup"><span data-stu-id="10fec-166">A bi-directional streaming call is complete when the the method returns:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        await responseStream.WriteAsync(new ExampleResponse());
    }
}
```

<span data-ttu-id="10fec-167">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="10fec-167">The preceding code:</span></span>

* <span data-ttu-id="10fec-168">Envoie une réponse pour chaque demande.</span><span class="sxs-lookup"><span data-stu-id="10fec-168">Sends a response for each request.</span></span>
* <span data-ttu-id="10fec-169">Est une utilisation de base de la diffusion bidirectionnelle.</span><span class="sxs-lookup"><span data-stu-id="10fec-169">Is a basic usage of bi-directional streaming.</span></span>

<span data-ttu-id="10fec-170">Il est possible de prendre en charge des scénarios plus complexes, tels que la lecture des demandes et l’envoi simultané de réponses :</span><span class="sxs-lookup"><span data-stu-id="10fec-170">It is possible to support more complex scenarios, such as reading requests and sending responses simultaneously:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    // Read requests in a background task.
    var readTask = Task.Run(async () =>
    {
        await foreach (var message in requestStream.ReadAllAsync())
        {
            // Process request.
        }
    });
    
    // Send responses until the client signals that it is complete.
    while (!readTask.IsCompleted)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

<span data-ttu-id="10fec-171">Dans une méthode de streaming bidirectionnel, le client et le service peuvent envoyer des messages entre eux à tout moment.</span><span class="sxs-lookup"><span data-stu-id="10fec-171">In a bi-directional streaming method, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="10fec-172">La meilleure implémentation d’une méthode bidirectionnelle varie en fonction des exigences.</span><span class="sxs-lookup"><span data-stu-id="10fec-172">The best implementation of a bi-directional method varies depending upon requirements.</span></span>

## <a name="access-grpc-request-headers"></a><span data-ttu-id="10fec-173">Accéder aux en-têtes de requête gRPC</span><span class="sxs-lookup"><span data-stu-id="10fec-173">Access gRPC request headers</span></span>

<span data-ttu-id="10fec-174">Un message de demande n’est pas le seul moyen pour un client d’envoyer des données à un service gRPC.</span><span class="sxs-lookup"><span data-stu-id="10fec-174">A request message is not the only way for a client to send data to a gRPC service.</span></span> <span data-ttu-id="10fec-175">Les valeurs d’en-tête sont disponibles dans un service à l’aide de `ServerCallContext.RequestHeaders` .</span><span class="sxs-lookup"><span data-stu-id="10fec-175">Header values are available in a service using `ServerCallContext.RequestHeaders`.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a><span data-ttu-id="10fec-176">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="10fec-176">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/client>
