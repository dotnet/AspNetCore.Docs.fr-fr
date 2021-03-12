---
title: Meilleures pratiques en matière de performances avec gRPC
author: jamesnk
description: Découvrez les meilleures pratiques pour créer des services gRPC à hautes performances.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: 5d19ace2e844f2159c1ba0e8bc92960bcf00d54e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586993"
---
# <a name="performance-best-practices-with-grpc"></a><span data-ttu-id="e7ce2-103">Meilleures pratiques en matière de performances avec gRPC</span><span class="sxs-lookup"><span data-stu-id="e7ce2-103">Performance best practices with gRPC</span></span>

<span data-ttu-id="e7ce2-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="e7ce2-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="e7ce2-105">gRPC est conçu pour les services à hautes performances.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="e7ce2-106">Ce document explique comment obtenir les meilleures performances possibles à partir de gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-grpc-channels"></a><span data-ttu-id="e7ce2-107">Réutiliser les canaux gRPC</span><span class="sxs-lookup"><span data-stu-id="e7ce2-107">Reuse gRPC channels</span></span>

<span data-ttu-id="e7ce2-108">Un canal gRPC doit être réutilisé pour effectuer des appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="e7ce2-109">La réutilisation d’un canal permet de multiplexer les appels par le biais d’une connexion HTTP/2 existante.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="e7ce2-110">Si un canal est créé pour chaque appel gRPC, le temps nécessaire à l’exécution peut augmenter de manière significative.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="e7ce2-111">Chaque appel nécessite plusieurs allers-retours sur le réseau entre le client et le serveur pour créer une nouvelle connexion HTTP/2 :</span><span class="sxs-lookup"><span data-stu-id="e7ce2-111">Each call will require multiple network round-trips between the client and the server to create a new HTTP/2 connection:</span></span>

1. <span data-ttu-id="e7ce2-112">Ouverture d’un socket</span><span class="sxs-lookup"><span data-stu-id="e7ce2-112">Opening a socket</span></span>
2. <span data-ttu-id="e7ce2-113">Établissement d’une connexion TCP</span><span class="sxs-lookup"><span data-stu-id="e7ce2-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="e7ce2-114">Négociation de TLS</span><span class="sxs-lookup"><span data-stu-id="e7ce2-114">Negotiating TLS</span></span>
4. <span data-ttu-id="e7ce2-115">Démarrage de la connexion HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e7ce2-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="e7ce2-116">Exécution de l’appel gRPC</span><span class="sxs-lookup"><span data-stu-id="e7ce2-116">Making the gRPC call</span></span>

<span data-ttu-id="e7ce2-117">Les canaux peuvent être partagés et réutilisés en toute sécurité entre les appels gRPC :</span><span class="sxs-lookup"><span data-stu-id="e7ce2-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="e7ce2-118">les clients gRPC sont créés avec des canaux.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="e7ce2-119">les clients gRPC sont des objets légers et n’ont pas besoin d’être mis en cache ou réutilisés.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="e7ce2-120">Plusieurs clients gRPC peuvent être créés à partir d’un canal, y compris différents types de clients.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="e7ce2-121">Un canal et des clients créés à partir du canal peuvent être utilisés en toute sécurité par plusieurs threads.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="e7ce2-122">Les clients créés à partir du canal peuvent effectuer plusieurs appels simultanés.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="e7ce2-123">la fabrique de clients gRPC offre une méthode centralisée pour configurer les canaux.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-123">gRPC client factory offers a centralized way to configure channels.</span></span> <span data-ttu-id="e7ce2-124">Il réutilise automatiquement les canaux sous-jacents.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-124">It automatically reuses underlying channels.</span></span> <span data-ttu-id="e7ce2-125">Pour plus d’informations, consultez <xref:grpc/clientfactory>.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-125">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="e7ce2-126">Accès concurrentiel de la connexion</span><span class="sxs-lookup"><span data-stu-id="e7ce2-126">Connection concurrency</span></span>

<span data-ttu-id="e7ce2-127">Les connexions HTTP/2 ont généralement une limite du nombre [maximal de flux simultanés (requêtes http actives)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) sur une connexion à la fois.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-127">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="e7ce2-128">Par défaut, la plupart des serveurs définissent cette limite sur 100 flux simultanés.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-128">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="e7ce2-129">Un canal gRPC utilise une seule connexion HTTP/2, et les appels simultanés sont multiplexés sur cette connexion.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-129">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="e7ce2-130">Lorsque le nombre d’appels actifs atteint la limite du flux de connexion, les appels supplémentaires sont mis en file d’attente dans le client.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-130">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="e7ce2-131">Les appels en file d’attente attendent que les appels actifs se terminent avant d’être envoyés.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-131">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="e7ce2-132">Les applications avec une charge élevée, ou des appels de diffusion en continu à long terme, peuvent voir des problèmes de performances dus à la mise en file d’attente des appels en raison de cette limite.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-132">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e7ce2-133">.NET 5 introduit la `SocketsHttpHandler.EnableMultipleHttp2Connections` propriété.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-133">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="e7ce2-134">Quand la valeur `true` est, les connexions http/2 supplémentaires sont créées par un canal lorsque la limite de flux simultanée est atteinte.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-134">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="e7ce2-135">Lorsqu’un `GrpcChannel` est créé, son interne `SocketsHttpHandler` est automatiquement configuré pour créer des connexions http/2 supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-135">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="e7ce2-136">Si une application configure son propre gestionnaire, envisagez `EnableMultipleHttp2Connections` de définir sur `true` :</span><span class="sxs-lookup"><span data-stu-id="e7ce2-136">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="e7ce2-137">Il existe deux solutions de contournement pour les applications .NET Core 3,1 :</span><span class="sxs-lookup"><span data-stu-id="e7ce2-137">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="e7ce2-138">Créez des canaux gRPC distincts pour les zones de l’application avec une charge élevée.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-138">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="e7ce2-139">Par exemple, le `Logger` service gRPC peut avoir une charge élevée.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-139">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="e7ce2-140">Utilisez un canal distinct pour créer le `LoggerClient` dans l’application.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-140">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="e7ce2-141">Utilisez un pool de canaux gRPC, par exemple, créer une liste de canaux gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-141">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="e7ce2-142">`Random` est utilisé pour choisir un canal dans la liste chaque fois qu’un canal gRPC est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-142">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="e7ce2-143">L’utilisation de `Random` distribue aléatoirement des appels sur plusieurs connexions.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-143">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e7ce2-144">L’amélioration du nombre maximal de flux simultanés sur le serveur est une autre façon de résoudre ce problème.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-144">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="e7ce2-145">Dans Kestrel, ce est configuré avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .</span><span class="sxs-lookup"><span data-stu-id="e7ce2-145">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="e7ce2-146">L’amélioration de la limite maximale du flux simultané n’est pas recommandée.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-146">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="e7ce2-147">Un trop grand nombre de flux sur une seule connexion HTTP/2 introduit de nouveaux problèmes de performances :</span><span class="sxs-lookup"><span data-stu-id="e7ce2-147">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="e7ce2-148">Conflit de threads entre les flux tentant d’écrire dans la connexion.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-148">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="e7ce2-149">La perte de paquets de connexion entraîne le blocage de tous les appels au niveau de la couche TCP.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-149">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

## <a name="load-balancing"></a><span data-ttu-id="e7ce2-150">Équilibrage de la charge</span><span class="sxs-lookup"><span data-stu-id="e7ce2-150">Load balancing</span></span>

<span data-ttu-id="e7ce2-151">Certains équilibrages de charge ne fonctionnent pas efficacement avec gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-151">Some load balancers don't work effectively with gRPC.</span></span> <span data-ttu-id="e7ce2-152">Les équilibreurs de charge L4 (transport) fonctionnent à un niveau de connexion, en répartissant les connexions TCP entre les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-152">L4 (transport) load balancers operate at a connection level, by distributing TCP connections across endpoints.</span></span> <span data-ttu-id="e7ce2-153">Cette approche fonctionne bien pour le chargement des appels d’API d’équilibrage effectués avec HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-153">This approach works well for loading balancing API calls made with HTTP/1.1.</span></span> <span data-ttu-id="e7ce2-154">Les appels simultanés effectués avec HTTP/1.1 sont envoyés sur des connexions différentes, ce qui permet d’équilibrer la charge des appels entre les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-154">Concurrent calls made with HTTP/1.1 are sent on different connections, allowing calls to be load balanced across endpoints.</span></span>

<span data-ttu-id="e7ce2-155">Étant donné que les équilibreurs de charge L4 fonctionnent au niveau de la connexion, ils ne fonctionnent pas correctement avec gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-155">Because L4 load balancers operate at a connection level, they don't work well with gRPC.</span></span> <span data-ttu-id="e7ce2-156">gRPC utilise le protocole HTTP/2, qui multiplexe plusieurs appels sur une seule connexion TCP.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-156">gRPC uses HTTP/2, which multiplexes multiple calls on a single TCP connection.</span></span> <span data-ttu-id="e7ce2-157">Tous les appels gRPC sur cette connexion sont dirigés vers un point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-157">All gRPC calls over that connection go to one endpoint.</span></span>

<span data-ttu-id="e7ce2-158">Il existe deux options pour équilibrer la charge de manière efficace gRPC :</span><span class="sxs-lookup"><span data-stu-id="e7ce2-158">There are two options to effectively load balance gRPC:</span></span>

* <span data-ttu-id="e7ce2-159">Équilibrage de charge côté client</span><span class="sxs-lookup"><span data-stu-id="e7ce2-159">Client-side load balancing</span></span>
* <span data-ttu-id="e7ce2-160">Équilibrage de charge du proxy L7 (application)</span><span class="sxs-lookup"><span data-stu-id="e7ce2-160">L7 (application) proxy load balancing</span></span>

> [!NOTE]
> <span data-ttu-id="e7ce2-161">Seuls les appels gRPC peuvent faire l’équilibrage de charge entre les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-161">Only gRPC calls can be load balanced between endpoints.</span></span> <span data-ttu-id="e7ce2-162">Une fois qu’un appel gRPC de diffusion en continu est établi, tous les messages envoyés via le flux sont dirigés vers un point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-162">Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.</span></span>

### <a name="client-side-load-balancing"></a><span data-ttu-id="e7ce2-163">Équilibrage de charge côté client</span><span class="sxs-lookup"><span data-stu-id="e7ce2-163">Client-side load balancing</span></span>

<span data-ttu-id="e7ce2-164">Avec l’équilibrage de charge côté client, le client connaît les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-164">With client-side load balancing, the client knows about endpoints.</span></span> <span data-ttu-id="e7ce2-165">Pour chaque appel gRPC, il sélectionne un autre point de terminaison auquel envoyer l’appel.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-165">For each gRPC call, it selects a different endpoint to send the call to.</span></span> <span data-ttu-id="e7ce2-166">L’équilibrage de charge côté client est un bon choix lorsque la latence est importante.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-166">Client-side load balancing is a good choice when latency is important.</span></span> <span data-ttu-id="e7ce2-167">Il n’existe aucun proxy entre le client et le service. l’appel est donc envoyé directement au service.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-167">There's no proxy between the client and the service, so the call is sent to the service directly.</span></span> <span data-ttu-id="e7ce2-168">L’inconvénient de l’équilibrage de charge côté client est que chaque client doit effectuer le suivi des points de terminaison disponibles qu’il doit utiliser.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-168">The downside to client-side load balancing is that each client must keep track of the available endpoints that it should use.</span></span>

<span data-ttu-id="e7ce2-169">L’équilibrage de la charge du client en parallèle est une technique dans laquelle l’état de l’équilibrage de charge est stocké dans un emplacement central.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-169">Lookaside client load balancing is a technique where load balancing state is stored in a central location.</span></span> <span data-ttu-id="e7ce2-170">Les clients interrogent régulièrement l’emplacement central pour rechercher les informations à utiliser lors de la prise de décision d’équilibrage de charge.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-170">Clients periodically query the central location for information to use when making load balancing decisions.</span></span>

<span data-ttu-id="e7ce2-171">`Grpc.Net.Client` actuellement, ne prend pas en charge l’équilibrage de charge côté client.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-171">`Grpc.Net.Client` currently doesn't support client-side load balancing.</span></span> <span data-ttu-id="e7ce2-172">[GRPC. Core](https://www.nuget.org/packages/Grpc.Core) est un bon choix si l’équilibrage de charge côté client est requis dans .net.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) is a good choice if client-side load balancing is required in .NET.</span></span>

### <a name="proxy-load-balancing"></a><span data-ttu-id="e7ce2-173">Équilibrage de charge du proxy</span><span class="sxs-lookup"><span data-stu-id="e7ce2-173">Proxy load balancing</span></span>

<span data-ttu-id="e7ce2-174">Un proxy L7 (application) fonctionne à un niveau supérieur à celui d’un proxy L4 (transport).</span><span class="sxs-lookup"><span data-stu-id="e7ce2-174">An L7 (application) proxy works at a higher level than an L4 (transport) proxy.</span></span> <span data-ttu-id="e7ce2-175">Les proxys L7 comprennent HTTP/2 et peuvent distribuer les appels gRPC multiplexés au proxy sur une connexion HTTP/2 sur plusieurs points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-175">L7 proxies understand HTTP/2, and are able to distribute gRPC calls multiplexed to the proxy on one HTTP/2 connection across multiple endpoints.</span></span> <span data-ttu-id="e7ce2-176">L’utilisation d’un proxy est plus simple que l’équilibrage de charge côté client, mais peut ajouter une latence supplémentaire aux appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-176">Using a proxy is simpler than client-side load balancing, but can add extra latency to gRPC calls.</span></span>

<span data-ttu-id="e7ce2-177">De nombreux proxys L7 sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-177">There are many L7 proxies available.</span></span> <span data-ttu-id="e7ce2-178">Certaines options sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="e7ce2-178">Some options are:</span></span>

* <span data-ttu-id="e7ce2-179">[Envoy](https://www.envoyproxy.io/) -un proxy Open source populaire.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-179">[Envoy](https://www.envoyproxy.io/) - A popular open source proxy.</span></span>
* <span data-ttu-id="e7ce2-180">[Linkerd](https://linkerd.io/) -service Mesh pour Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-180">[Linkerd](https://linkerd.io/) - Service mesh for Kubernetes.</span></span>
* <span data-ttu-id="e7ce2-181">[YARP : proxy inversé](https://microsoft.github.io/reverse-proxy/) -un proxy Open source en version préliminaire écrit en .net.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/) - A preview open source proxy written in .NET.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a><span data-ttu-id="e7ce2-182">Communication entre processus</span><span class="sxs-lookup"><span data-stu-id="e7ce2-182">Inter-process communication</span></span>

<span data-ttu-id="e7ce2-183">les appels gRPC entre un client et un service sont généralement envoyés via des sockets TCP.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-183">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="e7ce2-184">TCP est idéal pour la communication sur un réseau, mais la [communication entre processus (IPC)](https://wikipedia.org/wiki/Inter-process_communication) est plus efficace lorsque le client et le service se trouvent sur le même ordinateur.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-184">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span>

<span data-ttu-id="e7ce2-185">Envisagez d’utiliser un transport comme les sockets de domaine UNIX ou les canaux nommés pour les appels gRPC entre les processus sur le même ordinateur.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-185">Consider using a transport like Unix domain sockets or named pipes for gRPC calls between processes on the same machine.</span></span> <span data-ttu-id="e7ce2-186">Pour plus d’informations, consultez <xref:grpc/interprocess>.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-186">For more information, see <xref:grpc/interprocess>.</span></span>

## <a name="keep-alive-pings"></a><span data-ttu-id="e7ce2-187">Conserver les pings actifs</span><span class="sxs-lookup"><span data-stu-id="e7ce2-187">Keep alive pings</span></span>

<span data-ttu-id="e7ce2-188">Les commandes ping Keep Alive peuvent être utilisées pour maintenir les connexions HTTP/2 actives pendant les périodes d’inactivité.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-188">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="e7ce2-189">Le fait de disposer d’une connexion HTTP/2 existante quand une application reprend l’activité permet aux appels gRPC initiaux de se faire rapidement, sans délai provoqué par la réinitialisation de la connexion.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-189">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="e7ce2-190">Les pings Keep Alive sont configurés sur <xref:System.Net.Http.SocketsHttpHandler> :</span><span class="sxs-lookup"><span data-stu-id="e7ce2-190">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="e7ce2-191">Le code précédent configure un canal qui envoie une commande ping Keep Alive au serveur toutes les 60 secondes pendant les périodes d’inactivité.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-191">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="e7ce2-192">La commande ping garantit que le serveur et les proxys en cours d’utilisation ne ferment pas la connexion en raison d’une inactivité.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-192">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="e7ce2-193">Diffusion en continu</span><span class="sxs-lookup"><span data-stu-id="e7ce2-193">Streaming</span></span>

<span data-ttu-id="e7ce2-194">la diffusion en continu bidirectionnelle gRPC peut être utilisée pour remplacer les appels gRPC unaires dans des scénarios à hautes performances.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-194">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="e7ce2-195">Une fois qu’un flux bidirectionnel a démarré, la diffusion en continu des messages est plus rapide que l’envoi de messages avec plusieurs appels unaires gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-195">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="e7ce2-196">Les messages diffusés en continu sont envoyés en tant que données sur une requête HTTP/2 existante et éliminent la surcharge liée à la création d’une nouvelle requête HTTP/2 pour chaque appel unaire.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-196">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="e7ce2-197">Exemple de service :</span><span class="sxs-lookup"><span data-stu-id="e7ce2-197">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="e7ce2-198">Exemple de client :</span><span class="sxs-lookup"><span data-stu-id="e7ce2-198">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="e7ce2-199">Le remplacement des appels unaires par la diffusion bidirectionnelle pour des raisons de performances est une technique avancée qui n’est pas approprié dans de nombreuses situations.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-199">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="e7ce2-200">L’utilisation des appels en diffusion en continu est un bon choix lorsque :</span><span class="sxs-lookup"><span data-stu-id="e7ce2-200">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="e7ce2-201">Un débit élevé ou une latence faible est requis.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-201">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="e7ce2-202">gRPC et HTTP/2 sont identifiés comme un goulot d’étranglement au niveau des performances.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-202">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="e7ce2-203">Un Worker du client envoie ou reçoit des messages standard avec un service gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-203">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="e7ce2-204">Tenez compte de la complexité et des limitations supplémentaires liées à l’utilisation des appels de diffusion en continu au lieu de unaire :</span><span class="sxs-lookup"><span data-stu-id="e7ce2-204">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="e7ce2-205">Un flux peut être interrompu par un service ou une erreur de connexion.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-205">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="e7ce2-206">Une logique est nécessaire pour redémarrer le flux en cas d’erreur.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-206">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="e7ce2-207">`RequestStream.WriteAsync` n’est pas sécurisé pour le Multi-Threading.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-207">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="e7ce2-208">Un seul message à la fois peut être écrit dans un flux.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-208">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="e7ce2-209">L’envoi de messages à partir de plusieurs threads sur un seul flux nécessite une file d’attente de producteur/consommateur telle que le <xref:System.Threading.Channels.Channel%601> marshalling des messages.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-209">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="e7ce2-210">Une méthode de diffusion en continu gRPC est limitée à la réception d’un type de message et à l’envoi d’un type de message.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-210">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="e7ce2-211">Par exemple, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` reçoit `RequestMessage` et envoie `ResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="e7ce2-211">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="e7ce2-212">La prise en charge par Protobuf des messages inconnus ou conditionnels utilisant `Any` et peut contourner `oneof` cette limitation.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-212">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>

## <a name="send-binary-payloads"></a><span data-ttu-id="e7ce2-213">Envoyer des charges utiles binaires</span><span class="sxs-lookup"><span data-stu-id="e7ce2-213">Send binary payloads</span></span>

<span data-ttu-id="e7ce2-214">Les charges utiles binaires sont prises en charge dans Protobuf avec le `bytes` type de valeur scalaire.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-214">Binary payloads are supported in Protobuf with the `bytes` scalar value type.</span></span> <span data-ttu-id="e7ce2-215">Une propriété générée en C# utilise `ByteString` comme type de propriété.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-215">A generated property in C# uses `ByteString` as the property type.</span></span>

```protobuf
syntax = "proto3";

message PayloadResponse {
    bytes data = 1;
}  
```

<span data-ttu-id="e7ce2-216">`ByteString` les instances sont créées à l’aide de `ByteString.CopyFrom(byte[] data)` .</span><span class="sxs-lookup"><span data-stu-id="e7ce2-216">`ByteString` instances are created using `ByteString.CopyFrom(byte[] data)`.</span></span> <span data-ttu-id="e7ce2-217">Cette méthode alloue un nouveau `ByteString` et un nouveau `byte[]` .</span><span class="sxs-lookup"><span data-stu-id="e7ce2-217">This method allocates a new `ByteString` and a new `byte[]`.</span></span> <span data-ttu-id="e7ce2-218">Les données sont copiées dans le nouveau tableau d’octets.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-218">Data is copied into the new byte array.</span></span>

<span data-ttu-id="e7ce2-219">Des allocations et des copies supplémentaires peuvent être évitées à l’aide `UnsafeByteOperations.UnsafeWrap(ReadOnlyMemory<byte> bytes)` de pour créer des `ByteString` instances.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-219">Additional allocations and copies can be avoided by using `UnsafeByteOperations.UnsafeWrap(ReadOnlyMemory<byte> bytes)` to create `ByteString` instances.</span></span>

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = UnsafeByteOperations.UnsafeWrap(data);
```

<span data-ttu-id="e7ce2-220">Les octets ne sont pas copiés avec `UnsafeByteOperations.UnsafeWrap` . ils ne doivent donc pas être modifiés tant que `ByteString` est en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-220">Bytes are not copied with `UnsafeByteOperations.UnsafeWrap` so they must not be modified while the `ByteString` is in use.</span></span>

<span data-ttu-id="e7ce2-221">`UnsafeByteOperations.UnsafeWrap` nécessite [Google. Protobuf](https://www.nuget.org/packages/Google.Protobuf/) version 3.15.0 ou ultérieure.</span><span class="sxs-lookup"><span data-stu-id="e7ce2-221">`UnsafeByteOperations.UnsafeWrap` requires [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) version 3.15.0 or later.</span></span>
