---
title: Nouveautés d’ASP.NET Core 2.2
author: tdykstra
description: Découvrez les nouvelles fonctionnalités d’ASP.NET Core 2.2.
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/18/2018
uid: aspnetcore-2.2
ms.openlocfilehash: 13d7dec834a5661b445b4fc0c0be8be9b7b41b9e
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637727"
---
# <a name="whats-new-in-aspnet-core-22"></a><span data-ttu-id="3ec9a-103">Nouveautés d’ASP.NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="3ec9a-103">What's new in ASP.NET Core 2.2</span></span>

<span data-ttu-id="3ec9a-104">Cet article met en évidence les modifications les plus importantes dans ASP.NET Core 2.2 et fournit des liens vers la documentation appropriée.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-104">This article highlights the most significant changes in ASP.NET Core 2.2, with links to relevant documentation.</span></span>

## <a name="open-api-analyzers--conventions"></a><span data-ttu-id="3ec9a-105">Analyseurs et conventions d’Open API</span><span class="sxs-lookup"><span data-stu-id="3ec9a-105">Open API Analyzers & Conventions</span></span>

<span data-ttu-id="3ec9a-106">Open API (également appelé Swagger) est une spécification indépendante du langage pour décrire des API REST.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-106">Open API (also known as Swagger) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="3ec9a-107">L’écosystème Open API a des outils qui permettent de découvrir, de tester et de produire du code client avec la spécification.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-107">The Open API ecosystem has tools that allow for discovering, testing, and producing client code using the specification.</span></span> <span data-ttu-id="3ec9a-108">La prise en charge de la génération et de la visualisation de documents Open API dans ASP.NET Core MVC est fournie via des projets portés par la communauté, comme [NSwag](https://github.com/RSuter/NSwag) et [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="3ec9a-108">Support for generating and visualizing Open API documents in ASP.NET Core MVC is provided via community driven projects such as [NSwag](https://github.com/RSuter/NSwag), and [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span></span> <span data-ttu-id="3ec9a-109">ASP.NET Core 2.2 fournit des outils et des expériences de runtime améliorés pour la création de documents Open API.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-109">ASP.NET Core 2.2 provides improved tooling and runtime experiences for creating Open API documents.</span></span>

<span data-ttu-id="3ec9a-110">Pour plus d'informations, reportez-vous aux ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="3ec9a-110">For more information, see the following resources:</span></span>

* <xref:web-api/advanced/analyzers>
* <xref:web-api/advanced/conventions>
* [<span data-ttu-id="3ec9a-111">ASP.NET Core 2.2.0-preview1: Analyseurs et conventions d’Open API</span><span class="sxs-lookup"><span data-stu-id="3ec9a-111">ASP.NET Core 2.2.0-preview1: Open API Analyzers & Conventions</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/08/23/asp-net-core-2-20-preview1-open-api-analyzers-conventions/)

## <a name="problem-details-support"></a><span data-ttu-id="3ec9a-112">Prise en charge des détails des problèmes</span><span class="sxs-lookup"><span data-stu-id="3ec9a-112">Problem details support</span></span>

<span data-ttu-id="3ec9a-113">ASP.NET Core 2.1 a introduit `ProblemDetails`, basé sur la spécification RFC 7807 pour convoyer les détails d’une erreur avec une réponse HTTP.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-113">ASP.NET Core 2.1 introduced `ProblemDetails`, based on the RFC 7807 specification for carrying details of an error with an HTTP Response.</span></span> <span data-ttu-id="3ec9a-114">Dans la version 2.2, `ProblemDetails` est la réponse standard pour les codes d’erreur des clients dans les contrôleurs avec l’attribut `ApiControllerAttribute`.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-114">In 2.2, `ProblemDetails` is the standard response for client error codes in controllers attributed with `ApiControllerAttribute`.</span></span> <span data-ttu-id="3ec9a-115">Un `IActionResult` retournant un code d’état d’erreur du client (4xx) retourne maintenant un corps `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-115">An `IActionResult` returning a client error status code (4xx) now returns a `ProblemDetails` body.</span></span> <span data-ttu-id="3ec9a-116">Le résultat inclut également un ID de corrélation, qui peut être utilisé pour mettre en corrélation l’erreur en utilisant les journaux des requêtes.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-116">The result also includes a correlation ID that can be used to correlate the error using request logs.</span></span> <span data-ttu-id="3ec9a-117">Pour les erreurs des clients, `ProducesResponseType` utilise par défaut `ProblemDetails` comme type de réponse.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-117">For client errors, `ProducesResponseType` defaults to using `ProblemDetails` as the response type.</span></span> <span data-ttu-id="3ec9a-118">Cela est documenté dans la sortie Open API / Swagger générée avec NSwag ou Swashbuckle.AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-118">This is documented in Open API / Swagger output generated using NSwag or Swashbuckle.AspNetCore.</span></span>

## <a name="endpoint-routing"></a><span data-ttu-id="3ec9a-119">Routage de point de terminaison</span><span class="sxs-lookup"><span data-stu-id="3ec9a-119">Endpoint Routing</span></span>

<span data-ttu-id="3ec9a-120">ASP.NET Core 2.2 utilise un nouveau système de *routage de point de terminaison* pour améliorer la distribution des requêtes.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-120">ASP.NET Core 2.2 uses a new *endpoint routing* system for improved dispatching of requests.</span></span> <span data-ttu-id="3ec9a-121">Les changements sont notamment de nouveaux membres d’API de génération de liens et les transformateurs de paramètres de route.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-121">The changes include new link generation API members and route parameter transformers.</span></span>

<span data-ttu-id="3ec9a-122">Pour plus d'informations, reportez-vous aux ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="3ec9a-122">For more information, see the following resources:</span></span>

* [<span data-ttu-id="3ec9a-123">Routage de point de terminaison dans la version 2.2</span><span class="sxs-lookup"><span data-stu-id="3ec9a-123">Endpoint routing in 2.2</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/08/27/asp-net-core-2-2-0-preview1-endpoint-routing/)
* <span data-ttu-id="3ec9a-124">[Transformateurs de paramètres de route](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (consultez la section **Routage**)</span><span class="sxs-lookup"><span data-stu-id="3ec9a-124">[Route parameter transformers](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (see **Routing** section)</span></span>
* [<span data-ttu-id="3ec9a-125">Différences entre le routage IRouter et le routage de point de terminaison</span><span class="sxs-lookup"><span data-stu-id="3ec9a-125">Differences between IRouter- and endpoint-based routing</span></span>](xref:fundamentals/routing?view=aspnetcore-2.2#differences-from-earlier-versions-of-routing)

## <a name="health-checks"></a><span data-ttu-id="3ec9a-126">Contrôles d’intégrité</span><span class="sxs-lookup"><span data-stu-id="3ec9a-126">Health checks</span></span>

<span data-ttu-id="3ec9a-127">Un nouveau service de contrôles d’intégrité facilite l’utilisation d’ASP.NET Core dans les environnements qui nécessitent des contrôles d’intégrité, comme Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-127">A new health checks service makes it easier to use ASP.NET Core in environments that require health checks, such as Kubernetes.</span></span> <span data-ttu-id="3ec9a-128">Les contrôles d’intégrité incluent un middleware (intergiciel), et un ensemble de bibliothèques qui définissent une abstraction et un service `IHealthCheck`.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-128">Health checks includes middleware and a set of libraries that define an `IHealthCheck` abstraction and service.</span></span>

<span data-ttu-id="3ec9a-129">Les contrôles d’intégrité sont utilisés par un orchestrateur de conteneurs ou un équilibreur de charge pour déterminer rapidement si un système répond normalement aux requêtes.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-129">Health checks are used by a container orchestrator or load balancer to quickly determine if a system is responding to requests normally.</span></span> <span data-ttu-id="3ec9a-130">Un orchestrateur de conteneurs peut répondre à un contrôle d’intégrité en échec en arrêtant un déploiement en cours ou en redémarrant un conteneur.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-130">A container orchestrator might respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="3ec9a-131">Un équilibreur de charge peut répondre à un contrôle d’intégrité en routant le trafic en dehors d’une instance du service en échec.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-131">A load balancer might respond to a health check by routing traffic away from the failing instance of the service.</span></span>

<span data-ttu-id="3ec9a-132">Les contrôles d’intégrité sont exposés par une application comme point de terminaison HTTP utilisé par les systèmes de surveillance.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-132">Health checks are exposed by an application as an HTTP endpoint used by monitoring systems.</span></span> <span data-ttu-id="3ec9a-133">Les contrôles d’intégrité peuvent être configurés pour une grande variété de scénarios de surveillance en temps réel et de systèmes de surveillance.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-133">Health checks can be configured for a variety of real-time monitoring scenarios and monitoring systems.</span></span> <span data-ttu-id="3ec9a-134">Le service de contrôles d’intégrité s’intègre au [projet BeatPulse](https://github.com/Xabaril/BeatPulse),</span><span class="sxs-lookup"><span data-stu-id="3ec9a-134">The health checks service integrates with the [BeatPulse project](https://github.com/Xabaril/BeatPulse).</span></span> <span data-ttu-id="3ec9a-135">ce qui facilite l’ajout de contrôles pour des dizaines de systèmes et de dépendances les plus courants.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-135">which makes it easier to add checks for dozens of popular systems and dependencies.</span></span>

<span data-ttu-id="3ec9a-136">Pour plus d’informations, consultez [Contrôles d’intégrité dans ASP.NET Core](xref:host-and-deploy/health-checks).</span><span class="sxs-lookup"><span data-stu-id="3ec9a-136">For more information, see [Health checks in ASP.NET Core](xref:host-and-deploy/health-checks).</span></span>

## <a name="http2-in-kestrel"></a><span data-ttu-id="3ec9a-137">HTTP/2 dans Kestrel</span><span class="sxs-lookup"><span data-stu-id="3ec9a-137">HTTP/2 in Kestrel</span></span>

<span data-ttu-id="3ec9a-138">ASP.NET Core 2.2 ajoute la prise en charge de HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-138">ASP.NET Core 2.2 adds support for HTTP/2.</span></span> 

<span data-ttu-id="3ec9a-139">HTTP/2 est une révision majeure du protocole HTTP.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-139">HTTP/2 is a major revision of the HTTP protocol.</span></span> <span data-ttu-id="3ec9a-140">Certaines des principales fonctionnalités de HTTP/2 sont la prise en charge de la compression des en-têtes et le flux entièrement multiplexés sur une même connexion.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-140">Some of the notable features of HTTP/2 are support for header compression and fully multiplexed streams over a single connection.</span></span> <span data-ttu-id="3ec9a-141">Si HTTP/2 préserve la sémantique de HTTP (en-têtes HTTP, méthodes, etc.), il s’agit d’un changement majeur par rapport à HTTP/1.x quant à la façon dont les données sont structurées en trames et envoyées sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-141">While HTTP/2 preserves HTTP’s semantics (HTTP headers, methods, etc) it's a breaking change from HTTP/1.x on how this data is framed and sent over the wire.</span></span>

<span data-ttu-id="3ec9a-142">En raison de ce changement de tramage, les serveurs et les clients doivent négocier la version du protocole utilisée.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-142">As a consequence of this change in framing, servers and clients need to negotiate the protocol version used.</span></span> <span data-ttu-id="3ec9a-143">APLN (Application-Layer Protocol Negotiation) est une extension de TLS qui permet au serveur et au client de négocier la version du protocole utilisé dans le cadre de leur négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-143">Application-Layer Protocol Negotiation (ALPN) is a TLS extension that allows the server and client negotiate the protocol version used as part of their TLS handshake.</span></span> <span data-ttu-id="3ec9a-144">Bien que le serveur et le client puissent partager une connaissance préalable quant au protocole, tous les principaux navigateurs prennent en charge ALPN comme unique moyen d’établir une connexion HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-144">While it is possible to have prior knowledge between the server and the client on the protocol, all major browsers support ALPN as the only way to establish an HTTP/2 connection.</span></span>

<span data-ttu-id="3ec9a-145">Pour plus d’informations, consultez [Prise en charge de HTTP/2](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support).</span><span class="sxs-lookup"><span data-stu-id="3ec9a-145">For more information, see [HTTP/2 support](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support).</span></span>

## <a name="kestrel-configuration"></a><span data-ttu-id="3ec9a-146">Configuration de Kestrel</span><span class="sxs-lookup"><span data-stu-id="3ec9a-146">Kestrel configuration</span></span>

<span data-ttu-id="3ec9a-147">Dans les versions antérieures d’ASP.NET Core, les options de Kestrel sont configurées en appelant `UseKestrel`.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-147">In earlier versions of ASP.NET Core, Kestrel options are configured by calling `UseKestrel`.</span></span> <span data-ttu-id="3ec9a-148">Dans la version 2.2, les options de Kestrel sont configurées en appelant `ConfigureKestrel` sur le générateur de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-148">In 2.2, Kestrel options are configured by calling `ConfigureKestrel` on the host builder.</span></span> <span data-ttu-id="3ec9a-149">Cette modification résout un problème quant à l’ordre des inscriptions `IServer` pour l’hébergement in-process.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-149">This change resolves an issue with the order of `IServer` registrations for in-process hosting.</span></span> <span data-ttu-id="3ec9a-150">Pour plus d'informations, reportez-vous aux ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="3ec9a-150">For more information, see the following resources:</span></span>

* [<span data-ttu-id="3ec9a-151">Réduire les conflits liés à UseIIS</span><span class="sxs-lookup"><span data-stu-id="3ec9a-151">Mitigate UseIIS conflict</span></span>](https://github.com/aspnet/KestrelHttpServer/issues/2760)
* [<span data-ttu-id="3ec9a-152">Configurer les options du serveur Kestrel avec ConfigureKestrel</span><span class="sxs-lookup"><span data-stu-id="3ec9a-152">Configure Kestrel server options with ConfigureKestrel</span></span>](xref:fundamentals/servers/kestrel?view=aspnetcore-2.2#how-to-use-kestrel-in-aspnet-core-apps)

## <a name="iis-in-process-hosting"></a><span data-ttu-id="3ec9a-153">Hébergement in-process d’IIS</span><span class="sxs-lookup"><span data-stu-id="3ec9a-153">IIS in-process hosting</span></span>

<span data-ttu-id="3ec9a-154">Dans les versions antérieures d’ASP.NET Core, IIS sert de proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-154">In earlier versions of ASP.NET Core, IIS serves as a reverse proxy.</span></span> <span data-ttu-id="3ec9a-155">Dans la version 2.2, le module ASP.NET Core peut démarrer CoreCLR et héberger une application au sein du processus worker IIS (*w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="3ec9a-155">In 2.2, the ASP.NET Core Module can boot the CoreCLR and host an app inside the IIS worker process (*w3wp.exe*).</span></span> <span data-ttu-id="3ec9a-156">L’hébergement in-process offre des gains en matière de performances et de diagnostic lors de l’exécution avec IIS.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-156">In-process hosting provides performance and diagnostic gains when running with IIS.</span></span>

<span data-ttu-id="3ec9a-157">Pour plus d’informations, consultez [Hébergement in-process pour IIS](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="3ec9a-157">For more information, see [in-process hosting for IIS](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model).</span></span>

## <a name="signalr-java-client"></a><span data-ttu-id="3ec9a-158">Client Java SignalR</span><span class="sxs-lookup"><span data-stu-id="3ec9a-158">SignalR Java client</span></span>

<span data-ttu-id="3ec9a-159">ASP.NET Core 2.2 introduit un client Java pour SignalR.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-159">ASP.NET Core 2.2 introduces a Java Client for SignalR.</span></span> <span data-ttu-id="3ec9a-160">Ce client prend en charge la connexion à un serveur ASP.NET Core SignalR à partir de code Java, notamment dans des applications Android.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-160">This client supports connecting to an ASP.NET Core SignalR Server from Java code, including Android apps.</span></span>

<span data-ttu-id="3ec9a-161">Pour plus d’informations, consultez [Client Java ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="3ec9a-161">For more information, see [ASP.NET Core SignalR Java client](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2).</span></span>

## <a name="cors-improvements"></a><span data-ttu-id="3ec9a-162">Améliorations apportées à CORS</span><span class="sxs-lookup"><span data-stu-id="3ec9a-162">CORS improvements</span></span>

<span data-ttu-id="3ec9a-163">Dans les versions antérieures d’ASP.NET Core, le middleware (intergiciel) CORS permet l’envoi des en-têtes `Accept`, `Accept-Language`, `Content-Language` et `Origin` quelles que soient les valeurs configurées dans `CorsPolicy.Headers`.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-163">In earlier versions of ASP.NET Core, CORS Middleware allows `Accept`, `Accept-Language`, `Content-Language`, and `Origin` headers to be sent regardless of the values configured in `CorsPolicy.Headers`.</span></span> <span data-ttu-id="3ec9a-164">Dans la version 2.2, une correspondance de la stratégie de middleware CORS est possible seulement quand les en-têtes envoyés dans `Access-Control-Request-Headers` correspondent exactement aux en-têtes indiqués dans `WithHeaders`.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-164">In 2.2, a CORS Middleware policy match is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="3ec9a-165">Pour plus d’informations, consultez [Middleware CORS](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers).</span><span class="sxs-lookup"><span data-stu-id="3ec9a-165">For more information, see [CORS Middleware](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers).</span></span>

## <a name="response-compression"></a><span data-ttu-id="3ec9a-166">Compression des réponses</span><span class="sxs-lookup"><span data-stu-id="3ec9a-166">Response compression</span></span>

<span data-ttu-id="3ec9a-167">ASP.NET Core 2.2 peut compresser les réponses avec le [format de compression Brotli](https://tools.ietf.org/html/rfc7932).</span><span class="sxs-lookup"><span data-stu-id="3ec9a-167">ASP.NET Core 2.2 can compress responses with the [Brotli compression format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="3ec9a-168">Pour plus d’informations, consultez [Prise en charge de la compression Brotli par le middleware de compression des réponses](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="3ec9a-168">For more information, see [Response Compression Middleware supports Brotli compression](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider).</span></span>

## <a name="project-templates"></a><span data-ttu-id="3ec9a-169">Modèles de projet</span><span class="sxs-lookup"><span data-stu-id="3ec9a-169">Project templates</span></span>

<span data-ttu-id="3ec9a-170">Les modèles de projet web ASP.NET Core ont été mis à jour vers [Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) et [Angular 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4).</span><span class="sxs-lookup"><span data-stu-id="3ec9a-170">ASP.NET Core web project templates were updated to [Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) and [Angular 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4).</span></span> <span data-ttu-id="3ec9a-171">La nouvelle apparence est visuellement plus simple et facilite la visualisation des structures importantes de l’application.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-171">The new look is visually simpler and makes it easier to see the important structures of the app.</span></span>

![Page d’accueil ou page d’index](~/tutorials/razor-pages/razor-pages-start/_static/home2.2.png)

## <a name="validation-performance"></a><span data-ttu-id="3ec9a-173">Performances de la validation</span><span class="sxs-lookup"><span data-stu-id="3ec9a-173">Validation performance</span></span>

<span data-ttu-id="3ec9a-174">Le système de validation de MVC est conçu pour être extensible et flexible, vous permettant de déterminer pour chaque requête les validateurs à appliquer à un modèle donné.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-174">MVC’s validation system is designed to be extensible and flexible, allowing you to determine on a per request basis which validators apply to a given model.</span></span> <span data-ttu-id="3ec9a-175">Ceci convient bien à la création de fournisseurs de validation complexe.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-175">This is great for authoring complex validation providers.</span></span> <span data-ttu-id="3ec9a-176">Cependant, dans la plupart des cas, une application utilise seulement les validateurs intégrés et ne nécessite pas cette flexibilité supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-176">However, in the most common case an application only uses the built-in validators and don’t require this extra flexibility.</span></span> <span data-ttu-id="3ec9a-177">Les validateurs intégrés incluent des DataAnnotations comme [Required] et [StringLength], et `IValidatableObject`.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-177">Built-in validators include DataAnnotations such as [Required] and [StringLength], and `IValidatableObject`.</span></span>

<span data-ttu-id="3ec9a-178">Dans ASP.NET Core 2.2, MVC peut court-circuiter la validation s’il détermine que le graphe d’un modèle donné ne nécessite pas de validation.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-178">In ASP.NET Core 2.2, MVC can short-circuit validation if it determines that a given model graph doesn't require validation.</span></span> <span data-ttu-id="3ec9a-179">Ignorer les résultats de la validation permet des améliorations significatives lors de la validation de modèles qui ne peuvent pas avoir ou qui n’ont pas de validateurs.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-179">Skipping validation results in significant improvements when validating models that can't or don't have any validators.</span></span> <span data-ttu-id="3ec9a-180">Ceci inclut des objets comme les collections de primitifs (`byte[]`, `string[]`, `Dictionary<string, string>`, etc.) ou des graphes d’objets complexes sans beaucoup de validateurs.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-180">This includes objects such as collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`), or complex object graphs without many validators.</span></span>

## <a name="http-client-performance"></a><span data-ttu-id="3ec9a-181">Performances du client HTTP</span><span class="sxs-lookup"><span data-stu-id="3ec9a-181">HTTP Client performance</span></span>

<span data-ttu-id="3ec9a-182">Dans ASP.NET Core 2.2, les performances de `SocketsHttpHandler` ont été améliorées en réduisant la contention du verrouillage des pools de connexions.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-182">In ASP.NET Core 2.2, the performance of `SocketsHttpHandler` was improved by reducing connection pool locking contention.</span></span> <span data-ttu-id="3ec9a-183">Pour les applications qui font de nombreuses requêtes HTTP sortantes, comme certaines architectures de microservices, le débit est amélioré.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-183">For apps that make many outgoing HTTP requests, such as some microservices architectures, throughput is improved.</span></span> <span data-ttu-id="3ec9a-184">Sous une charge intensive, le débit de `HttpClient` peut être amélioré d’un facteur allant jusqu’à 60 % sur Linux et jusqu’à 20 % sur Windows.</span><span class="sxs-lookup"><span data-stu-id="3ec9a-184">Under load, `HttpClient` throughput can be improved by up to 60% on Linux and 20% on Windows.</span></span>

<span data-ttu-id="3ec9a-185">Pour plus d’informations, consultez [la demande de tirage à l’origine de cette amélioration](https://github.com/dotnet/corefx/pull/32568).</span><span class="sxs-lookup"><span data-stu-id="3ec9a-185">For more information, see [the pull request that made this improvement](https://github.com/dotnet/corefx/pull/32568).</span></span>

## <a name="additional-information"></a><span data-ttu-id="3ec9a-186">Informations supplémentaires</span><span class="sxs-lookup"><span data-stu-id="3ec9a-186">Additional information</span></span>

<span data-ttu-id="3ec9a-187">Pour obtenir la liste complète des modifications, consultez les [Notes de publication d’ASP.NET Core 2.2 ](https://github.com/aspnet/Home/releases/tag/2.2.0).</span><span class="sxs-lookup"><span data-stu-id="3ec9a-187">For the complete list of changes, see the [ASP.NET Core 2.2 Release Notes](https://github.com/aspnet/Home/releases/tag/2.2.0).</span></span>