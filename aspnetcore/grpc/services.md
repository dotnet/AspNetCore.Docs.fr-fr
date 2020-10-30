---
title: Créer des services et méthodes gRPC
author: jamesnk
description: Découvrez comment créer des services et méthodes gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
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
uid: grpc/services
ms.openlocfilehash: cc9fc50871cbad1f2ddf63d3c13c3253f24a995b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058738"
---
# <a name="create-grpc-services-and-methods"></a>Créer des services et méthodes gRPC

Par [James Newton-King](https://twitter.com/jamesnk)

Ce document explique comment créer des services et des méthodes gRPC en C#. Les sujets abordés sont les suivants :

* Comment définir des services et des méthodes dans des fichiers *. proto* .
* Code généré à l’aide des outils C# gRPC.
* Implémentation des méthodes et des services gRPC.

## <a name="create-new-grpc-services"></a>Créer de nouveaux services gRPC

[les services gRPC avec C# ont](xref:grpc/basics) introduit la première approche « Contract-First » de gRPC pour le développement d’API. Les services et les messages sont définis dans les fichiers *. proto* . Les outils C# génèrent ensuite du code à partir de fichiers *. proto* . Pour les ressources côté serveur, un type de base abstrait est généré pour chaque service, ainsi que des classes pour tous les messages.

Le fichier *. proto* suivant :

* Définit un `Greeter` service.
* Le `Greeter` service définit un `SayHello` appel.
* `SayHello` envoie un `HelloRequest` message et reçoit un `HelloReply` message

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

Les outils c# génèrent le `GreeterBase` type de base c# :

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

Par défaut, le généré `GreeterBase` ne fait rien. Sa `SayHello` méthode virtuelle renverra une `UNIMPLEMENTED` erreur aux clients qui l’appellent. Pour que le service soit utile, une application doit créer une implémentation concrète de `GreeterBase` :

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

L’implémentation du service est inscrite auprès de l’application. Si le service est hébergé par ASP.NET Core gRPC, il doit être ajouté au pipeline de routage à l’aide de la `MapGrpcService` méthode.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Pour plus d'informations, voir <xref:grpc/aspnetcore>.

## <a name="implement-grpc-methods"></a>Implémenter des méthodes gRPC

Un service gRPC peut avoir différents types de méthodes. La façon dont les messages sont envoyés et reçus par un service dépend du type de méthode défini. Les types de méthode gRPC sont les suivants :

* Unaire
* Streaming de serveur
* Diffusion en continu du client
* Streaming bidirectionnel

Les appels de diffusion en continu sont spécifiés avec le `stream` mot clé dans le fichier *. proto* . `stream` peut être placé sur le message de demande d’un appel, sur un message de réponse, ou sur les deux.

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

Chaque type d’appel a une signature de méthode différente. La substitution de méthodes générées à partir du type de service de base abstrait dans une implémentation concrète garantit que les arguments et le type de retour corrects sont utilisés.

### <a name="unary-method"></a>Unaire, méthode

Une méthode unaire obtient le message de requête en tant que paramètre et retourne la réponse. Un appel unaire est terminé lorsque la réponse est retournée.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

Les appels unaires sont les plus similaires aux [actions sur les contrôleurs d’API Web](xref:web-api/index). Une différence importante entre les méthodes gRPC et les actions est que les méthodes gRPC ne sont pas en mesure de lier des parties d’une requête à différents arguments de méthode. les méthodes gRPC ont toujours un argument de message pour les données de la demande entrante. Il est toujours possible d’envoyer plusieurs valeurs à un service gRPC en les mettant à la disposition des champs du message de demande :

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a>Méthode de diffusion en continu du serveur

Une méthode de diffusion en continu du serveur obtient le message de requête en tant que paramètre. Étant donné que plusieurs messages peuvent être transmis en continu à l’appelant, `responseStream.WriteAsync` est utilisé pour envoyer des messages de réponse. Un appel de streaming de serveur est terminé lorsque la méthode est retournée.

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

Le client n’a aucun moyen d’envoyer des messages ou des données supplémentaires une fois que la méthode de diffusion en continu du serveur a démarré. Certaines méthodes de streaming sont conçues pour s’exécuter indéfiniment. Pour les méthodes de streaming continues, un client peut annuler l’appel lorsqu’il n’est plus nécessaire. Lorsque l’annulation se produit, le client envoie un signal au serveur et [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) est déclenché. Le `CancellationToken` jeton doit être utilisé sur le serveur avec les méthodes Async afin que :

* Tout travail asynchrone est annulé avec l’appel de diffusion en continu.
* La méthode s’arrête rapidement.

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

### <a name="client-streaming-method"></a>Méthode de diffusion en continu du client

Une méthode de diffusion en continu du client démarre sans que la méthode *ne* reçoive un message. Le `requestStream` paramètre est utilisé pour lire les messages du client. Un appel de diffusion en continu du client est terminé lorsqu’un message de réponse est retourné :

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

Si vous utilisez C# 8 ou une version ultérieure, la `await foreach` syntaxe peut être utilisée pour lire les messages. La `IAsyncStreamReader<T>.ReadAllAsync()` méthode d’extension lit tous les messages du flux de requête :

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

### <a name="bi-directional-streaming-method"></a>Méthode de diffusion bidirectionnelle

Une méthode de diffusion en continu bidirectionnelle démarre *sans* la méthode qui reçoit un message. Le `requestStream` paramètre est utilisé pour lire les messages du client. La méthode peut choisir d’envoyer des messages avec `responseStream.WriteAsync` . Un appel de streaming bidirectionnel est terminé lorsque la méthode retourne :

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

Le code précédent :

* Envoie une réponse pour chaque demande.
* Est une utilisation de base de la diffusion bidirectionnelle.

Il est possible de prendre en charge des scénarios plus complexes, tels que la lecture des demandes et l’envoi simultané de réponses :

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

Dans une méthode de streaming bidirectionnel, le client et le service peuvent envoyer des messages entre eux à tout moment. La meilleure implémentation d’une méthode bidirectionnelle varie en fonction des exigences.

## <a name="access-grpc-request-headers"></a>Accéder aux en-têtes de requête gRPC

Un message de demande n’est pas le seul moyen pour un client d’envoyer des données à un service gRPC. Les valeurs d’en-tête sont disponibles dans un service à l’aide de `ServerCallContext.RequestHeaders` .

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/basics>
* <xref:grpc/client>
