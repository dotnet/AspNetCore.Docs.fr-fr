---
title: ASP.NET Core SignalR de l’hébergement et de la mise à l’échelle de production
author: bradygaster
description: Découvrez comment éviter les problèmes de performances et de mise à l’échelle dans les applications qui utilisent ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: e70f3143159a1817e326a95b30e7369a5c9ab025
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564011"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a><span data-ttu-id="3df88-103">SignalRHébergement et mise à l’échelle ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="3df88-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="3df88-104">Par [Andrew Stanton-infirmière](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)et [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="3df88-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="3df88-105">Cet article explique les considérations relatives à l’hébergement et à la mise à l’échelle pour les applications à fort trafic qui utilisent ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="3df88-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="3df88-106">Sessions rémanentes</span><span class="sxs-lookup"><span data-stu-id="3df88-106">Sticky Sessions</span></span>

<span data-ttu-id="3df88-107">SignalR requiert que toutes les requêtes HTTP pour une connexion spécifique soient gérées par le même processus serveur.</span><span class="sxs-lookup"><span data-stu-id="3df88-107">SignalR requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="3df88-108">Lorsque SignalR s’exécute sur une batterie de serveurs (plusieurs serveurs), vous devez utiliser des « sessions rémanentes ».</span><span class="sxs-lookup"><span data-stu-id="3df88-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="3df88-109">Les « sessions rémanentes » sont également appelées « affinité de session » par certains équilibrages de charge.</span><span class="sxs-lookup"><span data-stu-id="3df88-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="3df88-110">Azure App Service utilise [application Request Routing](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (arr) pour acheminer les demandes.</span><span class="sxs-lookup"><span data-stu-id="3df88-110">Azure App Service uses [Application Request Routing](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="3df88-111">L’activation du paramètre « affinité ARR » dans votre Azure App Service permet d’activer les « sessions rémanentes ».</span><span class="sxs-lookup"><span data-stu-id="3df88-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="3df88-112">Les sessions rémanentes ne sont pas nécessaires dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="3df88-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="3df88-113">En cas d’hébergement sur un seul serveur, dans un seul processus.</span><span class="sxs-lookup"><span data-stu-id="3df88-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="3df88-114">Lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="3df88-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="3df88-115">Lorsque tous les clients sont configurés pour utiliser **uniquement** les WebSockets, **et** que le [paramètre SkipNegotiation](xref:signalr/configuration#configure-additional-options) est activé dans la configuration du client.</span><span class="sxs-lookup"><span data-stu-id="3df88-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="3df88-116">Dans toutes les autres circonstances (y compris lorsque le fond de panier ReDim est utilisé), l’environnement du serveur doit être configuré pour les sessions rémanentes.</span><span class="sxs-lookup"><span data-stu-id="3df88-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="3df88-117">Pour obtenir des conseils sur la configuration de Azure App Service pour SignalR , consultez <xref:signalr/publish-to-azure-web-app> .</span><span class="sxs-lookup"><span data-stu-id="3df88-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="3df88-118">Ressources de connexion TCP</span><span class="sxs-lookup"><span data-stu-id="3df88-118">TCP connection resources</span></span>

<span data-ttu-id="3df88-119">Le nombre de connexions TCP simultanées qu’un serveur Web peut prendre en charge est limité.</span><span class="sxs-lookup"><span data-stu-id="3df88-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="3df88-120">Les clients HTTP standard utilisent des connexions *éphémères* .</span><span class="sxs-lookup"><span data-stu-id="3df88-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="3df88-121">Ces connexions peuvent être fermées lorsque le client devient inactif et rouvert ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="3df88-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="3df88-122">En revanche, une SignalR connexion est *persistante*.</span><span class="sxs-lookup"><span data-stu-id="3df88-122">On the other hand, a SignalR connection is *persistent*.</span></span> <span data-ttu-id="3df88-123">SignalR les connexions restent ouvertes même lorsque le client devient inactif.</span><span class="sxs-lookup"><span data-stu-id="3df88-123">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="3df88-124">Dans une application à trafic élevé qui dessert de nombreux clients, ces connexions persistantes peuvent entraîner l’atteinte du nombre maximal de connexions des serveurs.</span><span class="sxs-lookup"><span data-stu-id="3df88-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="3df88-125">Les connexions persistantes consomment également de la mémoire supplémentaire pour effectuer le suivi de chaque connexion.</span><span class="sxs-lookup"><span data-stu-id="3df88-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="3df88-126">L’utilisation intensive des ressources liées à la connexion par SignalR peut affecter les autres applications Web qui sont hébergées sur le même serveur.</span><span class="sxs-lookup"><span data-stu-id="3df88-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="3df88-127">Lorsque SignalR ouvre et contient les dernières connexions TCP disponibles, les autres applications Web sur le même serveur n’ont pas non plus de connexions disponibles.</span><span class="sxs-lookup"><span data-stu-id="3df88-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="3df88-128">Si un serveur est à court de connexions, vous verrez des erreurs de socket aléatoires et des erreurs de réinitialisation de la connexion.</span><span class="sxs-lookup"><span data-stu-id="3df88-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="3df88-129">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="3df88-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="3df88-130">Pour empêcher SignalR l’utilisation des ressources de provoquer des erreurs dans d’autres applications Web, exécutez SignalR sur des serveurs différents de ceux des autres applications Web.</span><span class="sxs-lookup"><span data-stu-id="3df88-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="3df88-131">Pour empêcher l' SignalR utilisation des ressources de provoquer des erreurs dans une SignalR application, augmentez la taille des instances pour limiter le nombre de connexions qu’un serveur doit gérer.</span><span class="sxs-lookup"><span data-stu-id="3df88-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="3df88-132">Scale-out</span><span class="sxs-lookup"><span data-stu-id="3df88-132">Scale out</span></span>

<span data-ttu-id="3df88-133">Une application qui utilise SignalR doit effectuer le suivi de toutes ses connexions, ce qui crée des problèmes pour une batterie de serveurs.</span><span class="sxs-lookup"><span data-stu-id="3df88-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="3df88-134">Ajoutez un serveur et il obtient les nouvelles connexions que les autres serveurs ne connaissent pas.</span><span class="sxs-lookup"><span data-stu-id="3df88-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="3df88-135">Par exemple, SignalR sur chaque serveur du diagramme suivant n’a pas connaissance des connexions sur les autres serveurs.</span><span class="sxs-lookup"><span data-stu-id="3df88-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="3df88-136">Lorsque SignalR sur l’un des serveurs souhaite envoyer un message à tous les clients, le message est envoyé uniquement aux clients connectés à ce serveur.</span><span class="sxs-lookup"><span data-stu-id="3df88-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![Mise à l’échelle ::: No-Loc (Signalr) ::: sans fond de panier](scale/_static/scale-no-backplane.png)

<span data-ttu-id="3df88-138">Les options permettant de résoudre ce problème sont [le SignalR service Azure](#azure-signalr-service) et le [fond de panier ReDim](#redis-backplane).</span><span class="sxs-lookup"><span data-stu-id="3df88-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-signalr-service"></a><span data-ttu-id="3df88-139">SignalRService Azure</span><span class="sxs-lookup"><span data-stu-id="3df88-139">Azure SignalR Service</span></span>

<span data-ttu-id="3df88-140">Le SignalR service Azure est un proxy plutôt qu’un fond de panier.</span><span class="sxs-lookup"><span data-stu-id="3df88-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="3df88-141">Chaque fois qu’un client initie une connexion au serveur, le client est redirigé pour se connecter au service.</span><span class="sxs-lookup"><span data-stu-id="3df88-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="3df88-142">Ce processus est illustré dans le diagramme suivant :</span><span class="sxs-lookup"><span data-stu-id="3df88-142">That process is illustrated in the following diagram:</span></span>

![Établissement d’une connexion à Azure ::: No-Loc (Signalr) ::: service](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="3df88-144">Le résultat est que le service gère toutes les connexions clientes, alors que chaque serveur n’a besoin que d’un petit nombre constant de connexions au service, comme indiqué dans le diagramme suivant :</span><span class="sxs-lookup"><span data-stu-id="3df88-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![Clients connectés au service, serveurs connectés au service](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="3df88-146">Cette approche de la montée en puissance parallèle présente plusieurs avantages par rapport au fond de panier ReDim :</span><span class="sxs-lookup"><span data-stu-id="3df88-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="3df88-147">Les sessions rémanentes, également appelées « [affinité du client](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)», ne sont pas nécessaires, car les clients sont immédiatement redirigés vers le SignalR service Azure lorsqu’ils se connectent.</span><span class="sxs-lookup"><span data-stu-id="3df88-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="3df88-148">Une SignalR application peut évoluer en fonction du nombre de messages envoyés, tandis que le service Azure est mis à l’échelle SignalR pour gérer un nombre quelconque de connexions.</span><span class="sxs-lookup"><span data-stu-id="3df88-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service scales to handle any number of connections.</span></span> <span data-ttu-id="3df88-149">Par exemple, il peut y avoir des milliers de clients, mais si seuls quelques messages par seconde sont envoyés, l' SignalR application n’a pas besoin d’effectuer une montée en charge sur plusieurs serveurs uniquement pour gérer les connexions elles-mêmes.</span><span class="sxs-lookup"><span data-stu-id="3df88-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="3df88-150">Une SignalR application n’utilise pas beaucoup plus de ressources de connexion qu’une application Web sans SignalR .</span><span class="sxs-lookup"><span data-stu-id="3df88-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="3df88-151">Pour ces raisons, nous vous recommandons d’utiliser le SignalR service Azure pour toutes les SignalR applications ASP.net Core hébergées sur Azure, y compris les app service, les machines virtuelles et les conteneurs.</span><span class="sxs-lookup"><span data-stu-id="3df88-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="3df88-152">Pour plus d’informations, consultez la [ SignalR documentation du service Azure](/azure/azure-signalr/signalr-overview).</span><span class="sxs-lookup"><span data-stu-id="3df88-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="3df88-153">Fond de panier Redis</span><span class="sxs-lookup"><span data-stu-id="3df88-153">Redis backplane</span></span>

<span data-ttu-id="3df88-154">[Redims](https://redis.io/) est un magasin de valeurs de clé en mémoire qui prend en charge un système de messagerie avec un modèle de publication/abonnement.</span><span class="sxs-lookup"><span data-stu-id="3df88-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="3df88-155">Le SignalR fond de panier ReDim utilise la fonctionnalité Pub/Sub pour transférer des messages vers d’autres serveurs.</span><span class="sxs-lookup"><span data-stu-id="3df88-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="3df88-156">Lorsqu’un client établit une connexion, les informations de connexion sont transmises au fond de panier.</span><span class="sxs-lookup"><span data-stu-id="3df88-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="3df88-157">Lorsqu’un serveur souhaite envoyer un message à tous les clients, il envoie au fond de panier.</span><span class="sxs-lookup"><span data-stu-id="3df88-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="3df88-158">Le fond de panier connaît tous les clients connectés et les serveurs sur lesquels ils se trouvent.</span><span class="sxs-lookup"><span data-stu-id="3df88-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="3df88-159">Elle envoie le message à tous les clients par le biais de leurs serveurs respectifs.</span><span class="sxs-lookup"><span data-stu-id="3df88-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="3df88-160">Ce processus est illustré dans le diagramme suivant :</span><span class="sxs-lookup"><span data-stu-id="3df88-160">This process is illustrated in the following diagram:</span></span>

![Le fond de panier ReDim, message envoyé d’un serveur à tous les clients](scale/_static/redis-backplane.png)

<span data-ttu-id="3df88-162">Le fond de panier ReDim est l’approche recommandée pour la montée en puissance parallèle pour les applications hébergées dans votre propre infrastructure.</span><span class="sxs-lookup"><span data-stu-id="3df88-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="3df88-163">En cas de latence de connexion importante entre votre centre de données et un centre de données Azure, le SignalR service Azure peut ne pas être une option pratique pour les applications locales avec des exigences de faible latence ou de débit élevé.</span><span class="sxs-lookup"><span data-stu-id="3df88-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="3df88-164">Les SignalR avantages du service Azure mentionnés précédemment sont les inconvénients du fond de panier ReDim :</span><span class="sxs-lookup"><span data-stu-id="3df88-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="3df88-165">Des sessions rémanentes, également appelées « [affinité du client](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)», sont nécessaires, sauf lorsque les **deux** conditions suivantes sont réunies :</span><span class="sxs-lookup"><span data-stu-id="3df88-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="3df88-166">Tous les clients sont configurés pour utiliser **uniquement** les WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3df88-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="3df88-167">Le [paramètre SkipNegotiation](xref:signalr/configuration#configure-additional-options) est activé dans la configuration du client.</span><span class="sxs-lookup"><span data-stu-id="3df88-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="3df88-168">Une fois qu’une connexion est établie sur un serveur, la connexion doit rester sur ce serveur.</span><span class="sxs-lookup"><span data-stu-id="3df88-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="3df88-169">Une SignalR application doit être montée en charge en fonction du nombre de clients, même si peu de messages sont envoyés.</span><span class="sxs-lookup"><span data-stu-id="3df88-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="3df88-170">Une SignalR application utilise beaucoup plus de ressources de connexion qu’une application Web sans SignalR .</span><span class="sxs-lookup"><span data-stu-id="3df88-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="3df88-171">Limitations IIS sur le système d’exploitation client Windows</span><span class="sxs-lookup"><span data-stu-id="3df88-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="3df88-172">Windows 10 et Windows 8. x sont des systèmes d’exploitation clients.</span><span class="sxs-lookup"><span data-stu-id="3df88-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="3df88-173">IIS sur les systèmes d’exploitation clients est limité à 10 connexions simultanées.</span><span class="sxs-lookup"><span data-stu-id="3df88-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> <span data-ttu-id="3df88-174">SignalRles connexions de sont :</span><span class="sxs-lookup"><span data-stu-id="3df88-174">SignalR's connections are:</span></span>

* <span data-ttu-id="3df88-175">Transitoire et fréquemment rétabli.</span><span class="sxs-lookup"><span data-stu-id="3df88-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="3df88-176">**N’est pas** supprimé immédiatement lorsqu’il n’est plus utilisé.</span><span class="sxs-lookup"><span data-stu-id="3df88-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="3df88-177">Les conditions précédentes permettent d’atteindre la limite de 10 connexions sur un système d’exploitation client.</span><span class="sxs-lookup"><span data-stu-id="3df88-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="3df88-178">Lorsqu’un système d’exploitation client est utilisé pour le développement, nous vous recommandons les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="3df88-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="3df88-179">Évitez les services Internet.</span><span class="sxs-lookup"><span data-stu-id="3df88-179">Avoid IIS.</span></span>
* <span data-ttu-id="3df88-180">Utilisez Kestrel ou IIS Express comme cibles de déploiement.</span><span class="sxs-lookup"><span data-stu-id="3df88-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="3df88-181">Linux avec Nginx</span><span class="sxs-lookup"><span data-stu-id="3df88-181">Linux with Nginx</span></span>

<span data-ttu-id="3df88-182">L’exemple suivant contient les paramètres requis minimum pour activer WebSockets, ServerSentEvents et LongPolling pour SignalR :</span><span class="sxs-lookup"><span data-stu-id="3df88-182">The following contains the minimum required settings to enable WebSockets, ServerSentEvents, and LongPolling for SignalR:</span></span>

```nginx
http {
  map $http_connection $connection_upgrade {
    "~*Upgrade" $http_connection;
    default keep-alive;
}

  server {
    listen 80;
    server_name example.com *.example.com;

    # Configure the SignalR Endpoint
    location /hubroute {
      # App server url
      proxy_pass http://localhost:5000;

      # Configuration for WebSockets
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_cache off;

      # Configuration for ServerSentEvents
      proxy_buffering off;

      # Configuration for LongPolling or if your KeepAliveInterval is longer than 60 seconds
      proxy_read_timeout 100s;

      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
```

<span data-ttu-id="3df88-183">Lorsque plusieurs serveurs principaux sont utilisés, des sessions rémanentes doivent être ajoutées pour empêcher les SignalR connexions de changer de serveur lors de la connexion.</span><span class="sxs-lookup"><span data-stu-id="3df88-183">When multiple backend servers are used, sticky sessions must be added to prevent SignalR connections from switching servers when connecting.</span></span> <span data-ttu-id="3df88-184">Il existe plusieurs façons d’ajouter des sessions rémanentes dans nginx.</span><span class="sxs-lookup"><span data-stu-id="3df88-184">There are multiple ways to add sticky sessions in Nginx.</span></span> <span data-ttu-id="3df88-185">Deux approches sont présentées ci-dessous en fonction de ce que vous avez disponible.</span><span class="sxs-lookup"><span data-stu-id="3df88-185">Two approaches are shown below depending on what you have available.</span></span>

<span data-ttu-id="3df88-186">Les éléments suivants sont ajoutés en plus de la configuration précédente.</span><span class="sxs-lookup"><span data-stu-id="3df88-186">The following is added in addition to the previous configuration.</span></span> <span data-ttu-id="3df88-187">Dans les exemples suivants, `backend` est le nom du groupe de serveurs.</span><span class="sxs-lookup"><span data-stu-id="3df88-187">In the following examples, `backend` is the name of the group of servers.</span></span>

<span data-ttu-id="3df88-188">Avec [Nginx Open source](https://nginx.org/en/), utilisez `ip_hash` pour acheminer les connexions à un serveur en fonction de l’adresse IP du client :</span><span class="sxs-lookup"><span data-stu-id="3df88-188">With [Nginx Open Source](https://nginx.org/en/), use `ip_hash` to route connections to a server based on the client's IP address:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    ip_hash;
  }
}
```

<span data-ttu-id="3df88-189">Avec [Nginx plus](https://www.nginx.com/products/nginx), utilisez `sticky` pour ajouter un cookie à des demandes et épingler les demandes de l’utilisateur à un serveur :</span><span class="sxs-lookup"><span data-stu-id="3df88-189">With [Nginx Plus](https://www.nginx.com/products/nginx), use `sticky` to add a cookie to requests and pin the user's requests to a server:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    sticky cookie srv_id expires=max domain=.example.com path=/ httponly;
  }
}
```

<span data-ttu-id="3df88-190">Enfin, remplacez `proxy_pass http://localhost:5000` la `server` section par `proxy_pass http://backend` .</span><span class="sxs-lookup"><span data-stu-id="3df88-190">Finally, change `proxy_pass http://localhost:5000` in the `server` section to `proxy_pass http://backend`.</span></span>

<span data-ttu-id="3df88-191">Pour plus d’informations sur les WebSockets sur nginx, consultez [Nginx en tant que proxy WebSocket](https://www.nginx.com/blog/websocket-nginx).</span><span class="sxs-lookup"><span data-stu-id="3df88-191">For more information on WebSockets over Nginx, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx).</span></span>

<span data-ttu-id="3df88-192">Pour plus d’informations sur l’équilibrage de charge et les sessions rémanentes, consultez [équilibrage de charge Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).</span><span class="sxs-lookup"><span data-stu-id="3df88-192">For more information on load balancing and sticky sessions, see [NGINX load balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).</span></span>

<span data-ttu-id="3df88-193">Pour plus d’informations sur les ASP.NET Core avec nginx, consultez l’article suivant :</span><span class="sxs-lookup"><span data-stu-id="3df88-193">For more information about ASP.NET Core with Nginx see the following article:</span></span>
* <xref:host-and-deploy/linux-nginx>

## <a name="third-party-signalr-backplane-providers"></a><span data-ttu-id="3df88-194">SignalRFournisseurs de fond de panier tiers</span><span class="sxs-lookup"><span data-stu-id="3df88-194">Third-party SignalR backplane providers</span></span>

* [<span data-ttu-id="3df88-195">NCache</span><span class="sxs-lookup"><span data-stu-id="3df88-195">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="3df88-196">[Orléans](https://github.com/OrleansContrib/SignalR.Orleans)</span><span class="sxs-lookup"><span data-stu-id="3df88-196">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span></span>
* <span data-ttu-id="3df88-197">[Rebus](https://github.com/rebus-org/Rebus.SignalR)</span><span class="sxs-lookup"><span data-stu-id="3df88-197">[Rebus](https://github.com/rebus-org/Rebus.SignalR)</span></span>

## <a name="next-steps"></a><span data-ttu-id="3df88-198">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="3df88-198">Next steps</span></span>

<span data-ttu-id="3df88-199">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="3df88-199">For more information, see the following resources:</span></span>

* [<span data-ttu-id="3df88-200">SignalRDocumentation du service Azure</span><span class="sxs-lookup"><span data-stu-id="3df88-200">Azure SignalR Service documentation</span></span>](/azure/azure-signalr/signalr-overview)
* [<span data-ttu-id="3df88-201">Configurer un backplane ReDim</span><span class="sxs-lookup"><span data-stu-id="3df88-201">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
