---
title: Quand utiliser un proxy inverse avec le serveur Web ASP.NET Core Kestrel
author: rick-anderson
description: En savoir plus sur l’utilisation d’un proxy inverse devant Kestrel, le serveur Web multiplateforme pour ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253939"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="19abf-103">Quand utiliser Kestrel avec un proxy inverse ?</span><span class="sxs-lookup"><span data-stu-id="19abf-103">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="19abf-104">Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse*, comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="19abf-104">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="19abf-105">Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="19abf-105">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="19abf-106">Kestrel utilisé comme serveur web edge (accessible sur Internet) :</span><span class="sxs-lookup"><span data-stu-id="19abf-106">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel communique directement avec Internet sans serveur proxy inverse](_static/kestrel-to-internet2.png)

<span data-ttu-id="19abf-108">Kestrel utilisé dans une configuration de proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="19abf-108">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](_static/kestrel-to-internet.png)

<span data-ttu-id="19abf-110">L’une ou l’autre des configurations, avec ou sans serveur proxy inverse, est une configuration d’hébergement prise en charge.</span><span class="sxs-lookup"><span data-stu-id="19abf-110">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="19abf-111">Lorsque Kestrel est utilisé en tant que serveur Edge sans serveur proxy inverse, le partage de la même adresse IP et du même port entre plusieurs processus n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="19abf-111">When Kestrel is used as an edge server without a reverse proxy server, sharing of the same IP address and port among multiple processes is unsupported.</span></span> <span data-ttu-id="19abf-112">Lorsque Kestrel est configuré pour écouter sur un port, Kestrel gère tout le trafic pour ce port, quels que soient les `Host` en-têtes des demandes.</span><span class="sxs-lookup"><span data-stu-id="19abf-112">When Kestrel is configured to listen on a port, Kestrel handles all traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="19abf-113">Un proxy inverse pouvant partager des ports peut transférer les demandes à Kestrel sur une adresse IP et un port uniques.</span><span class="sxs-lookup"><span data-stu-id="19abf-113">A reverse proxy that can share ports can forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="19abf-114">Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.</span><span class="sxs-lookup"><span data-stu-id="19abf-114">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="19abf-115">Un proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="19abf-115">A reverse proxy:</span></span>

* <span data-ttu-id="19abf-116">Peut limiter la surface publique exposée des applications qu’il héberge.</span><span class="sxs-lookup"><span data-stu-id="19abf-116">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="19abf-117">Fournit une couche supplémentaire de configuration et de défense.</span><span class="sxs-lookup"><span data-stu-id="19abf-117">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="19abf-118">Peut mieux s’intégrer à l’infrastructure existante.</span><span class="sxs-lookup"><span data-stu-id="19abf-118">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="19abf-119">Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="19abf-119">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="19abf-120">Seul le serveur proxy inverse requiert un certificat X. 509 et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide du protocole HTTP simple.</span><span class="sxs-lookup"><span data-stu-id="19abf-120">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="19abf-121">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](xref:fundamentals/servers/kestrel/host-filtering).</span><span class="sxs-lookup"><span data-stu-id="19abf-121">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="19abf-122">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="19abf-122">Additional resources</span></span>

<xref:host-and-deploy/proxy-load-balancer>

