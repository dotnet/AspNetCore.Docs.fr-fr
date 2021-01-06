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
# <a name="call-grpc-services-with-the-net-client"></a>Appeler les services gRPC avec le client .NET

Une bibliothèque cliente .NET gRPC est disponible dans le package NuGet [gRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) . Ce document explique comment effectuer les opérations suivantes :

* Configurez un client gRPC pour appeler les services gRPC.
* Effectuez des appels gRPC à des méthodes unaire, de streaming de serveur, de diffusion de client et de diffusion bidirectionnelle.

## <a name="configure-grpc-client"></a>Configurer le client gRPC

les clients gRPC sont des types de client concrets qui sont [générés à partir de fichiers *\* . proto*](xref:grpc/basics#generated-c-assets). Le client gRPC concret possède des méthodes qui traduisent le service gRPC dans le fichier *\* . proto* . Par exemple, un service appelé `Greeter` génère un `GreeterClient` type avec des méthodes pour appeler le service.

Un client gRPC est créé à partir d’un canal. Commencez par utiliser `GrpcChannel.ForAddress` pour créer un canal, puis utilisez le canal pour créer un client gRPC :

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Un canal représente une connexion à long terme à un service gRPC. Lorsqu’un canal est créé, il est configuré avec les options relatives à l’appel d’un service. Par exemple, le `HttpClient` utilisé pour effectuer des appels, la taille maximale des messages d’envoi et de réception, et la journalisation peuvent être spécifiés sur `GrpcChannelOptions` et utilisés avec `GrpcChannel.ForAddress` . Pour obtenir la liste complète des options, consultez [options de configuration du client](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a>Configurer TLS

Un client gRPC doit utiliser la même sécurité au niveau de la connexion que le service appelé. gRPC client Transport Layer Security (TLS) est configuré lors de la création du canal gRPC. Un client gRPC génère une erreur lorsqu’il appelle un service et que la sécurité au niveau de la connexion du canal et du service ne correspond pas.

Pour configurer un canal gRPC pour utiliser TLS, assurez-vous que l’adresse du serveur commence par `https` . Par exemple, `GrpcChannel.ForAddress("https://localhost:5001")` utilise le protocole HTTPS. Le canal gRPC négocie automatiquement une connexion sécurisée par TLS et utilise une connexion sécurisée pour effectuer des appels gRPC.

> [!TIP]
> gRPC prend en charge l’authentification par certificat client sur TLS. Pour plus d’informations sur la configuration des certificats clients avec un canal gRPC, consultez <xref:grpc/authn-and-authz#client-certificate-authentication> .

Pour appeler des services gRPC non sécurisés, assurez-vous que l’adresse du serveur commence par `http` . Par exemple, `GrpcChannel.ForAddress("http://localhost:5000")` utilise le protocole http. Dans .NET Core 3,1 ou version ultérieure, une configuration supplémentaire est requise pour [appeler des services gRPC non sécurisés avec le client .net](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).

### <a name="client-performance"></a>Performances du client

Performances et utilisation des canaux et des clients :

* La création d’un canal peut être une opération coûteuse. La réutilisation d’un canal pour les appels gRPC offre des avantages en matière de performances.
* les clients gRPC sont créés avec des canaux. les clients gRPC sont des objets légers et n’ont pas besoin d’être mis en cache ou réutilisés.
* Plusieurs clients gRPC peuvent être créés à partir d’un canal, y compris différents types de clients.
* Un canal et des clients créés à partir du canal peuvent être utilisés en toute sécurité par plusieurs threads.
* Les clients créés à partir du canal peuvent effectuer plusieurs appels simultanés.

`GrpcChannel.ForAddress` n’est pas la seule option pour créer un client gRPC. Si vous appelez gRPC services à partir d’une application ASP.NET Core, envisagez l’intégration de la [fabrique de clients gRPC](xref:grpc/clientfactory). l’intégration de gRPC avec `HttpClientFactory` offre une alternative centralisée à la création de clients gRPC.

> [!NOTE]
> L’appel de gRPC sur HTTP/2 avec `Grpc.Net.Client` n’est pas pris en charge actuellement sur Xamarin. Nous nous efforçons d’améliorer la prise en charge de HTTP/2 dans une prochaine version de Xamarin. [GRPC. Core](https://www.nuget.org/packages/Grpc.Core) et [GRPC-Web](xref:grpc/browser) sont des alternatives viables qui fonctionnent aujourd’hui.

## <a name="make-grpc-calls"></a>Effectuer des appels gRPC

Un appel gRPC est initié par l’appel d’une méthode sur le client. Le client gRPC gère la sérialisation des messages et traite l’appel gRPC au service approprié.

gRPC a différents types de méthodes. La façon dont le client est utilisé pour effectuer un appel gRPC dépend du type de méthode appelé. Les types de méthode gRPC sont les suivants :

* Unaire
* Streaming de serveur
* Diffusion en continu du client
* Streaming bidirectionnel

### <a name="unary-call"></a>Appel unaire

Un appel unaire commence par le client envoyant un message de demande. Un message de réponse est retourné lorsque le service se termine.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Chaque méthode de service unaire du fichier *\* . proto* se traduira par deux méthodes .net sur le type de client gRPC concret pour l’appel de la méthode : une méthode asynchrone et une méthode de blocage. Par exemple, `GreeterClient` il existe deux façons d’appeler `SayHello` :

* `GreeterClient.SayHelloAsync` -appelle le `Greeter.SayHello` service de façon asynchrone. Peut être attendu.
* `GreeterClient.SayHello` -appelle le `Greeter.SayHello` service et bloque jusqu’à la fin. N’utilisez pas dans le code asynchrone.

### <a name="server-streaming-call"></a>Appel de streaming de serveur

Un appel de streaming de serveur commence par le client qui envoie un message de demande. `ResponseStream.MoveNext()` lit les messages diffusés en continu à partir du service. L’appel de streaming de serveur est terminé lorsque `ResponseStream.MoveNext()` retourne `false` .

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

Si vous utilisez C# 8 ou une version ultérieure, la `await foreach` syntaxe peut être utilisée pour lire les messages. La `IAsyncStreamReader<T>.ReadAllAsync()` méthode d’extension lit tous les messages du flux de réponse :

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a>Appel de diffusion en continu du client

Un appel de diffusion en continu du client démarre *sans* que le client envoie un message. Le client peut choisir d’envoyer des messages avec `RequestStream.WriteAsync` . Lorsque le client a terminé l’envoi des messages, `RequestStream.CompleteAsync()` doit être appelé pour notifier le service. L’appel est terminé lorsque le service renvoie un message de réponse.

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

### <a name="bi-directional-streaming-call"></a>Appel de streaming bidirectionnel

Un appel de streaming bidirectionnel démarre *sans* que le client envoie un message. Le client peut choisir d’envoyer des messages avec `RequestStream.WriteAsync` . Les messages transmis en continu à partir du service sont accessibles avec `ResponseStream.MoveNext()` ou `ResponseStream.ReadAllAsync()` . L’appel de streaming bidirectionnel est terminé lorsque le n' `ResponseStream` a plus de messages.

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

Pour des performances optimales et pour éviter les erreurs inutiles dans le client et le service, essayez d’effectuer des appels de diffusion en continu bidirectionnelle de manière appropriée. Un appel bidirectionnel se termine correctement lorsque le serveur a fini de lire le flux de requête et que le client a terminé la lecture du flux de réponse. L’exemple d’appel précédent est un exemple d’appel bidirectionnel qui se termine normalement. Dans l’appel, le client :

1. Démarre un nouvel appel de streaming bidirectionnel en appelant `EchoClient.Echo` .
2. Crée une tâche en arrière-plan pour lire les messages du service à l’aide de `ResponseStream.ReadAllAsync()` .
3. Envoie des messages au serveur avec `RequestStream.WriteAsync` .
4. Indique au serveur qu’il a terminé d’envoyer des messages avec `RequestStream.CompleteAsync()` .
5. Attend que la tâche en arrière-plan ait lu tous les messages entrants.

Pendant un appel de streaming bidirectionnel, le client et le service peuvent envoyer des messages entre eux à tout moment. La meilleure logique cliente pour interagir avec un appel bidirectionnel varie en fonction de la logique du service.

## <a name="access-grpc-headers"></a>Accéder aux en-têtes gRPC

les appels gRPC retournent des en-têtes de réponse. Les en-têtes de réponse HTTP transmettent les métadonnées de nom/valeur relatives à un appel qui n’est pas lié au message retourné.

Les en-têtes sont accessibles à l’aide de `ResponseHeadersAsync` , qui retourne une collection de métadonnées. Les en-têtes sont généralement retournés avec le message de réponse ; par conséquent, vous devez les attendre.

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });

var headers = await call.ResponseHeadersAsync;
var myValue = headers.GetValue("my-trailer-name");

var response = await call.ResponseAsync;
```

`ResponseHeadersAsync` syntaxe

* Doit attendre le résultat de `ResponseHeadersAsync` pour obtenir la collection d’en-têtes.
* Ne doit pas être accessible avant `ResponseAsync` (ou le flux de réponse lors de la diffusion en continu). Si une réponse a été retournée, `ResponseHeadersAsync` retourne des en-têtes instantanément.
* Lèvera une exception en cas d’erreur de connexion ou de serveur et si les en-têtes n’ont pas été retournés pour l’appel gRPC.

## <a name="access-grpc-trailers"></a>Accéder aux codes de fin gRPC

les appels gRPC peuvent retourner des codes de fin de réponse. Les codes de fin sont utilisés pour fournir les métadonnées nom/valeur relatives à un appel. Les codes de fin offrent des fonctionnalités similaires aux en-têtes HTTP, mais sont reçus à la fin de l’appel.

Les codes de fin sont accessibles à l’aide de `GetTrailers()` , qui retourne une collection de métadonnées. Les codes de fin sont retournés une fois la réponse terminée. Par conséquent, vous devez attendre tous les messages de réponse avant d’accéder aux codes de fin.

Les appels unaires et de streaming client doivent attendre `ResponseAsync` avant d’appeler `GetTrailers()` :

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

Les appels de diffusion en continu bidirectionnelle et serveur doivent finir par attendre le flux de réponse avant d’appeler `GetTrailers()` :

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

Les codes de fin sont également accessibles à partir de `RpcException` . Un service peut retourner des codes de fin avec un État gRPC non OK. Dans ce cas, les codes de fin sont récupérés à partir de l’exception levée par le client gRPC :

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

## <a name="configure-deadline"></a>Configurer l’échéance

La configuration d’une échéance d’appel gRPC est recommandée, car elle fournit une limite supérieure à la durée pendant laquelle un appel peut s’exécuter. Il empêche les services incorrects de s’exécuter de façon permanente et d’épuiser les ressources du serveur. Les échéances sont un outil utile pour créer des applications fiables.

Configurez `CallOptions.Deadline` pour définir une échéance pour un appel gRPC :

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

Pour plus d’informations, consultez <xref:grpc/deadlines-cancellation#deadlines>.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
