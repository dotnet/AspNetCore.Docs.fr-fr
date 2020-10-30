---
title: intégration de la fabrique de clients gRPC dans .NET Core
author: jamesnk
description: Découvrez comment créer des clients gRPC à l’aide de la fabrique de clients.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/clientfactory
ms.openlocfilehash: c63bf495f558237ed801881d378953119791b8ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060948"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="c35d2-103">intégration de la fabrique de clients gRPC dans .NET Core</span><span class="sxs-lookup"><span data-stu-id="c35d2-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="c35d2-104">l’intégration de gRPC avec `HttpClientFactory` offre un moyen centralisé de créer des clients gRPC.</span><span class="sxs-lookup"><span data-stu-id="c35d2-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="c35d2-105">Il peut être utilisé comme alternative à la [configuration d’instances clientes gRPC autonomes](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="c35d2-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="c35d2-106">L’intégration des usines est disponible dans le package NuGet [GRPC .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="c35d2-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="c35d2-107">La fabrique offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="c35d2-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="c35d2-108">Fournit un emplacement central pour la configuration des instances de client gRPC logiques</span><span class="sxs-lookup"><span data-stu-id="c35d2-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="c35d2-109">Gère la durée de vie du sous-jacent `HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="c35d2-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="c35d2-110">Propagation automatique de l’échéance et de l’annulation dans un service ASP.NET Core gRPC</span><span class="sxs-lookup"><span data-stu-id="c35d2-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="c35d2-111">Inscrire les clients gRPC</span><span class="sxs-lookup"><span data-stu-id="c35d2-111">Register gRPC clients</span></span>

<span data-ttu-id="c35d2-112">Pour inscrire un client gRPC, vous `AddGrpcClient` pouvez utiliser la méthode d’extension générique dans `Startup.ConfigureServices` , en spécifiant la classe de client et l’adresse de service gRPC typés :</span><span class="sxs-lookup"><span data-stu-id="c35d2-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="c35d2-113">Le type de client gRPC est inscrit comme temporaire avec l’injection de dépendances (DI).</span><span class="sxs-lookup"><span data-stu-id="c35d2-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="c35d2-114">Le client peut maintenant être injecté et consommé directement dans les types créés par DI.</span><span class="sxs-lookup"><span data-stu-id="c35d2-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="c35d2-115">ASP.NET Core les contrôleurs MVC, les :::no-loc(SignalR)::: hubs et les services gRPC sont des emplacements où les clients gRPC peuvent être automatiquement injectés :</span><span class="sxs-lookup"><span data-stu-id="c35d2-115">ASP.NET Core MVC controllers, :::no-loc(SignalR)::: hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

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

## <a name="configure-httpclient"></a><span data-ttu-id="c35d2-116">Configurer HttpClient</span><span class="sxs-lookup"><span data-stu-id="c35d2-116">Configure HttpClient</span></span>

<span data-ttu-id="c35d2-117">`HttpClientFactory` crée le `HttpClient` utilisé par le client gRPC.</span><span class="sxs-lookup"><span data-stu-id="c35d2-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="c35d2-118">Les `HttpClientFactory` méthodes standard peuvent être utilisées pour ajouter un intergiciel (middleware) de demandes sortantes ou pour configurer le sous-jacent `HttpClientHandler` du `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="c35d2-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

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

<span data-ttu-id="c35d2-119">Pour plus d’informations, consultez [effectuer des requêtes http à l’aide de IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="c35d2-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="c35d2-120">Configurer le canal et les intercepteurs</span><span class="sxs-lookup"><span data-stu-id="c35d2-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="c35d2-121">les méthodes spécifiques à gRPC sont disponibles pour :</span><span class="sxs-lookup"><span data-stu-id="c35d2-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="c35d2-122">Configurez le canal sous-jacent d’un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="c35d2-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="c35d2-123">Ajoutez `Interceptor` des instances que le client utilisera pour effectuer des appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="c35d2-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

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

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="c35d2-124">Propagation de l’échéance et de l’annulation</span><span class="sxs-lookup"><span data-stu-id="c35d2-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="c35d2-125">les clients gRPC créés par la fabrique dans un service gRPC peuvent être configurés avec `EnableCallContextPropagation()` pour propager automatiquement l’échéance et le jeton d’annulation aux appels enfants.</span><span class="sxs-lookup"><span data-stu-id="c35d2-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="c35d2-126">La `EnableCallContextPropagation()` méthode d’extension est disponible dans le package NuGet [GRPC. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="c35d2-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="c35d2-127">La propagation du contexte d’appel fonctionne en lisant l’échéance et le jeton d’annulation dans le contexte de requête gRPC actuel et en les propageant automatiquement aux appels sortants effectués par le client gRPC.</span><span class="sxs-lookup"><span data-stu-id="c35d2-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="c35d2-128">La propagation du contexte d’appel est un excellent moyen de s’assurer que les scénarios de gRPC complexes et imbriqués propagent toujours l’échéance et l’annulation.</span><span class="sxs-lookup"><span data-stu-id="c35d2-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="c35d2-129">Par défaut, `EnableCallContextPropagation` génère une erreur si le client est utilisé en dehors du contexte d’un appel gRPC.</span><span class="sxs-lookup"><span data-stu-id="c35d2-129">By default, `EnableCallContextPropagation` raises an error if the client is used outside the context of a gRPC call.</span></span> <span data-ttu-id="c35d2-130">L’erreur est conçue pour vous avertir qu’il n’existe pas de contexte d’appel à propager.</span><span class="sxs-lookup"><span data-stu-id="c35d2-130">The error is designed to alert you that there isn't a call context to propagate.</span></span> <span data-ttu-id="c35d2-131">Si vous souhaitez utiliser le client en dehors d’un contexte d’appel, supprimez l’erreur quand le client est configuré avec `SuppressContextNotFoundErrors` :</span><span class="sxs-lookup"><span data-stu-id="c35d2-131">If you want to use the client outside of a call context, suppress the error when the client is configured with `SuppressContextNotFoundErrors`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

<span data-ttu-id="c35d2-132">Pour plus d’informations sur les échéances et l’annulation RPC, consultez [cycle de vie RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="c35d2-132">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c35d2-133">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c35d2-133">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
