---
title: intégration de la fabrique de clients gRPC dans .NET Core
author: jamesnk
description: Découvrez comment créer des clients gRPC à l’aide de la fabrique de clients.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/clientfactory
ms.openlocfilehash: c63bf495f558237ed801881d378953119791b8ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060948"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>intégration de la fabrique de clients gRPC dans .NET Core

l’intégration de gRPC avec `HttpClientFactory` offre un moyen centralisé de créer des clients gRPC. Il peut être utilisé comme alternative à la [configuration d’instances clientes gRPC autonomes](xref:grpc/client). L’intégration des usines est disponible dans le package NuGet [GRPC .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .

La fabrique offre les avantages suivants :

* Fournit un emplacement central pour la configuration des instances de client gRPC logiques
* Gère la durée de vie du sous-jacent `HttpClientMessageHandler`
* Propagation automatique de l’échéance et de l’annulation dans un service ASP.NET Core gRPC

## <a name="register-grpc-clients"></a>Inscrire les clients gRPC

Pour inscrire un client gRPC, vous `AddGrpcClient` pouvez utiliser la méthode d’extension générique dans `Startup.ConfigureServices` , en spécifiant la classe de client et l’adresse de service gRPC typés :

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

Le type de client gRPC est inscrit comme temporaire avec l’injection de dépendances (DI). Le client peut maintenant être injecté et consommé directement dans les types créés par DI. ASP.NET Core les contrôleurs MVC, les SignalR hubs et les services gRPC sont des emplacements où les clients gRPC peuvent être automatiquement injectés :

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>Configurer HttpClient

`HttpClientFactory` crée le `HttpClient` utilisé par le client gRPC. Les `HttpClientFactory` méthodes standard peuvent être utilisées pour ajouter un intergiciel (middleware) de demandes sortantes ou pour configurer le sous-jacent `HttpClientHandler` du `HttpClient` :

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

Pour plus d’informations, consultez [effectuer des requêtes http à l’aide de IHttpClientFactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Configurer le canal et les intercepteurs

les méthodes spécifiques à gRPC sont disponibles pour :

* Configurez le canal sous-jacent d’un client gRPC.
* Ajoutez `Interceptor` des instances que le client utilisera pour effectuer des appels gRPC.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>Propagation de l’échéance et de l’annulation

les clients gRPC créés par la fabrique dans un service gRPC peuvent être configurés avec `EnableCallContextPropagation()` pour propager automatiquement l’échéance et le jeton d’annulation aux appels enfants. La `EnableCallContextPropagation()` méthode d’extension est disponible dans le package NuGet [GRPC. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .

La propagation du contexte d’appel fonctionne en lisant l’échéance et le jeton d’annulation dans le contexte de requête gRPC actuel et en les propageant automatiquement aux appels sortants effectués par le client gRPC. La propagation du contexte d’appel est un excellent moyen de s’assurer que les scénarios de gRPC complexes et imbriqués propagent toujours l’échéance et l’annulation.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Par défaut, `EnableCallContextPropagation` génère une erreur si le client est utilisé en dehors du contexte d’un appel gRPC. L’erreur est conçue pour vous avertir qu’il n’existe pas de contexte d’appel à propager. Si vous souhaitez utiliser le client en dehors d’un contexte d’appel, supprimez l’erreur quand le client est configuré avec `SuppressContextNotFoundErrors` :

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

Pour plus d’informations sur les échéances et l’annulation RPC, consultez [cycle de vie RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
