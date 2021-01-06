---
title: Héberger et déployer des ASP.NET Core Blazor Server
author: guardrex
description: Découvrez comment héberger et déployer une Blazor Server application à l’aide de ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: a209109210ef5e335734a974ceb0c2af7cb8e1a1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94595439"
---
# <a name="host-and-deploy-no-locblazor-server"></a><span data-ttu-id="648f1-103">Héberger et déployer Blazor Server</span><span class="sxs-lookup"><span data-stu-id="648f1-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="648f1-104">Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="648f1-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="648f1-105">Valeurs de configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="648f1-105">Host configuration values</span></span>

<span data-ttu-id="648f1-106">les [ Blazor Server applications](xref:blazor/hosting-models#blazor-server) peuvent accepter des [valeurs de configuration d’hôte génériques](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="648f1-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="648f1-107">Déploiement</span><span class="sxs-lookup"><span data-stu-id="648f1-107">Deployment</span></span>

<span data-ttu-id="648f1-108">À l’aide du [ Blazor Server modèle d’hébergement](xref:blazor/hosting-models#blazor-server), Blazor est exécuté sur le serveur à partir d’une application ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="648f1-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="648f1-109">Les mises à jour de l’interface utilisateur, la gestion des événements et les appels JavaScript sont gérés sur une [SignalR](xref:signalr/introduction) connexion.</span><span class="sxs-lookup"><span data-stu-id="648f1-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="648f1-110">Un serveur web capable d’héberger une application ASP.NET Core est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="648f1-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="648f1-111">Visual Studio comprend le modèle de projet d' **Blazor Server application** ( `blazorserverside` modèle lors de l’utilisation de la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande).</span><span class="sxs-lookup"><span data-stu-id="648f1-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="648f1-112">Extensibilité</span><span class="sxs-lookup"><span data-stu-id="648f1-112">Scalability</span></span>

<span data-ttu-id="648f1-113">Planifiez un déploiement pour tirer le meilleur parti de l’infrastructure disponible pour une Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="648f1-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="648f1-114">Consultez les ressources suivantes pour résoudre l' Blazor Server évolutivité des applications :</span><span class="sxs-lookup"><span data-stu-id="648f1-114">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="648f1-115">Notions de base des Blazor Server applications</span><span class="sxs-lookup"><span data-stu-id="648f1-115">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="648f1-116">Serveur de déploiement</span><span class="sxs-lookup"><span data-stu-id="648f1-116">Deployment server</span></span>

<span data-ttu-id="648f1-117">Lorsque vous envisagez l’évolutivité d’un serveur unique (montée en puissance), la mémoire disponible pour une application est probablement la première ressource que l’application épuisera en fonction des demandes des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="648f1-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="648f1-118">La mémoire disponible sur le serveur affecte les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="648f1-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="648f1-119">Nombre de circuits actifs qu’un serveur peut prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="648f1-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="648f1-120">Latence de l’interface utilisateur sur le client.</span><span class="sxs-lookup"><span data-stu-id="648f1-120">UI latency on the client.</span></span>

<span data-ttu-id="648f1-121">Pour obtenir des conseils sur la création d’applications serveur sécurisées et évolutives Blazor , consultez <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="648f1-121">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="648f1-122">Chaque circuit utilise environ 250 Ko de mémoire pour une application de type *Hello World* minimale.</span><span class="sxs-lookup"><span data-stu-id="648f1-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="648f1-123">La taille d’un circuit dépend du code de l’application et des exigences de maintenance d’état associées à chaque composant.</span><span class="sxs-lookup"><span data-stu-id="648f1-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="648f1-124">Nous vous recommandons de mesurer les demandes de ressources pendant le développement de votre application et de votre infrastructure, mais la ligne de base suivante peut être un point de départ pour la planification de votre cible de déploiement : Si vous pensez que votre application prend en charge 5 000 utilisateurs simultanés, envisagez de budgétiser au moins 1,3 Go de mémoire serveur vers l’application (ou ~ 273 Ko par utilisateur)</span><span class="sxs-lookup"><span data-stu-id="648f1-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="no-locsignalr-configuration"></a><span data-ttu-id="648f1-125">SignalR configuré</span><span class="sxs-lookup"><span data-stu-id="648f1-125">SignalR configuration</span></span>

<span data-ttu-id="648f1-126">Blazor Server les applications utilisent ASP.NET Core SignalR pour communiquer avec le navigateur.</span><span class="sxs-lookup"><span data-stu-id="648f1-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="648f1-127">[ SignalR les conditions d’hébergement et de mise à l’échelle de](xref:signalr/publish-to-azure-web-app) s’appliquent aux Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="648f1-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="648f1-128">Blazor fonctionne mieux lorsque vous utilisez WebSocket en tant que SignalR transport en raison d’une latence, d’une fiabilité et d’une [sécurité](xref:signalr/security)moindres.</span><span class="sxs-lookup"><span data-stu-id="648f1-128">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="648f1-129">L’interrogation longue est utilisée par SignalR lorsque WebSocket n’est pas disponible ou lorsque l’application est configurée explicitement pour utiliser une interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="648f1-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="648f1-130">Lors du déploiement sur Azure App Service, configurez l’application pour qu’elle utilise WebSockets dans les paramètres Portail Azure pour le service.</span><span class="sxs-lookup"><span data-stu-id="648f1-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="648f1-131">Pour plus d’informations sur la configuration de l’application pour Azure App Service, consultez les [ SignalR instructions de publication](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="648f1-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-no-locsignalr-service"></a><span data-ttu-id="648f1-132">SignalRService Azure</span><span class="sxs-lookup"><span data-stu-id="648f1-132">Azure SignalR Service</span></span>

<span data-ttu-id="648f1-133">Nous vous recommandons d’utiliser le [ SignalR service Azure](xref:signalr/scale#azure-signalr-service) pour les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="648f1-133">We recommend using the [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) for Blazor Server apps.</span></span> <span data-ttu-id="648f1-134">Le service permet la mise à l’échelle d’une Blazor Server application vers un grand nombre de connexions simultanées SignalR .</span><span class="sxs-lookup"><span data-stu-id="648f1-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="648f1-135">En outre, la SignalR portée mondiale et les centres de données haute performance du service contribuent de manière significative à réduire la latence en raison de la géographie.</span><span class="sxs-lookup"><span data-stu-id="648f1-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="648f1-136">Lorsque les [WebSockets](https://wikipedia.org/wiki/WebSocket) sont désactivés, Azure App service simule une connexion en temps réel à l’aide d’une longue interrogation http.</span><span class="sxs-lookup"><span data-stu-id="648f1-136">When [WebSockets](https://wikipedia.org/wiki/WebSocket) are disabled, Azure App Service simulates a real-time connection using HTTP Long Polling.</span></span> <span data-ttu-id="648f1-137">L’interrogation HTTP longue est sensiblement plus lente que l’exécution avec WebSockets activé, qui n’utilise pas l’interrogation pour simuler une connexion client-serveur.</span><span class="sxs-lookup"><span data-stu-id="648f1-137">HTTP Long Polling is noticeably slower than running with WebSockets enabled, which doesn't use polling to simulate a client-server connection.</span></span>
>
> <span data-ttu-id="648f1-138">Nous vous recommandons d’utiliser WebSockets pour les Blazor Server applications déployées sur Azure App service.</span><span class="sxs-lookup"><span data-stu-id="648f1-138">We recommend using WebSockets for Blazor Server apps deployed to Azure App Service.</span></span> <span data-ttu-id="648f1-139">Par défaut, le [ SignalR service Azure](xref:signalr/scale#azure-signalr-service) utilise WebSocket.</span><span class="sxs-lookup"><span data-stu-id="648f1-139">The [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) uses WebSockets by default.</span></span> <span data-ttu-id="648f1-140">Si l’application n’utilise pas le SignalR service Azure, consultez <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> .</span><span class="sxs-lookup"><span data-stu-id="648f1-140">If the app doesn't use the Azure SignalR Service, see <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>.</span></span>
>
> <span data-ttu-id="648f1-141">Pour plus d’informations, consultez :</span><span class="sxs-lookup"><span data-stu-id="648f1-141">For more information, see:</span></span>
>
> * [<span data-ttu-id="648f1-142">Qu’est-ce que le SignalR service Azure ?</span><span class="sxs-lookup"><span data-stu-id="648f1-142">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
> * [<span data-ttu-id="648f1-143">Guide des performances pour le SignalR service Azure</span><span class="sxs-lookup"><span data-stu-id="648f1-143">Performance guide for Azure SignalR Service</span></span>](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a><span data-ttu-id="648f1-144">Configuration</span><span class="sxs-lookup"><span data-stu-id="648f1-144">Configuration</span></span>

<span data-ttu-id="648f1-145">Pour configurer une application pour le SignalR service Azure, l’application doit prendre en charge les *sessions rémanentes*, où les clients sont [redirigés vers le même serveur lors du prérendu](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="648f1-145">To configure an app for the Azure SignalR Service, the app must support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="648f1-146">L' `ServerStickyMode` option ou la valeur de configuration est définie sur `Required` .</span><span class="sxs-lookup"><span data-stu-id="648f1-146">The `ServerStickyMode` option or configuration value is set to `Required`.</span></span> <span data-ttu-id="648f1-147">En règle générale, une application crée la configuration à l’aide de l' **_une_** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="648f1-147">Typically, an app creates the configuration using **_ONE_** of the following approaches:</span></span>

   * <span data-ttu-id="648f1-148">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="648f1-148">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="648f1-149">Configuration (utilisez l' **_une_** des approches suivantes) :</span><span class="sxs-lookup"><span data-stu-id="648f1-149">Configuration (use **_ONE_** of the following approaches):</span></span>
  
     * <span data-ttu-id="648f1-150">Dans `appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="648f1-150">In `appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * <span data-ttu-id="648f1-151">Les paramètres d’application de **configuration** de l’app service  >   dans le portail Azure (**nom**: `Azure__SignalR__StickyServerMode` , **valeur**: `Required` ).</span><span class="sxs-lookup"><span data-stu-id="648f1-151">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure__SignalR__StickyServerMode`, **Value**: `Required`).</span></span> <span data-ttu-id="648f1-152">Cette approche est adoptée automatiquement pour l’application si vous [approvisionnez le SignalR service Azure](#provision-the-azure-signalr-service).</span><span class="sxs-lookup"><span data-stu-id="648f1-152">This approach is adopted for the app automatically if you [provision the Azure SignalR Service](#provision-the-azure-signalr-service).</span></span>

### <a name="provision-the-azure-no-locsignalr-service"></a><span data-ttu-id="648f1-153">Approvisionner le SignalR service Azure</span><span class="sxs-lookup"><span data-stu-id="648f1-153">Provision the Azure SignalR Service</span></span>

<span data-ttu-id="648f1-154">Pour approvisionner le SignalR service Azure pour une application dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="648f1-154">To provision the Azure SignalR Service for an app in Visual Studio:</span></span>

1. <span data-ttu-id="648f1-155">Créez un profil de publication Azure Apps dans Visual Studio pour l' Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="648f1-155">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="648f1-156">Ajoutez la dépendance du **SignalR service Azure** au profil.</span><span class="sxs-lookup"><span data-stu-id="648f1-156">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="648f1-157">Si l’abonnement Azure n’a pas d’instance de service Azure préexistante SignalR à attribuer à l’application, sélectionnez **créer une nouvelle instance de SignalR service Azure** pour approvisionner une nouvelle instance de service.</span><span class="sxs-lookup"><span data-stu-id="648f1-157">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="648f1-158">Publiez l’application dans Azure.</span><span class="sxs-lookup"><span data-stu-id="648f1-158">Publish the app to Azure.</span></span>

<span data-ttu-id="648f1-159">L’approvisionnement du service Azure SignalR dans Visual Studio active automatiquement [les *sessions rémanentes*](#configuration) et ajoute SignalR la chaîne de connexion à la configuration de l’app service.</span><span class="sxs-lookup"><span data-stu-id="648f1-159">Provisioning the Azure SignalR Service in Visual Studio automatically [enables *sticky sessions*](#configuration) and adds the SignalR connection string to the app service's configuration.</span></span>

#### <a name="iis"></a><span data-ttu-id="648f1-160">IIS</span><span class="sxs-lookup"><span data-stu-id="648f1-160">IIS</span></span>

<span data-ttu-id="648f1-161">Lorsque vous utilisez IIS, activez :</span><span class="sxs-lookup"><span data-stu-id="648f1-161">When using IIS, enable:</span></span>

* <span data-ttu-id="648f1-162">[WebSocket sur IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="648f1-162">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="648f1-163">[Sessions rémanentes avec application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="648f1-163">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="648f1-164">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="648f1-164">Kubernetes</span></span>

<span data-ttu-id="648f1-165">Créez une définition d’entrée avec les [Annotations Kubernetes suivantes pour les sessions rémanentes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="648f1-165">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="648f1-166">Linux avec Nginx</span><span class="sxs-lookup"><span data-stu-id="648f1-166">Linux with Nginx</span></span>

<span data-ttu-id="648f1-167">Pour que SignalR WebSocket fonctionne correctement, vérifiez que les `Upgrade` `Connection` en-têtes et du proxy sont définis sur les valeurs suivantes et qu’ils sont `$connection_upgrade` mappés à l’un ou l’autre des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="648f1-167">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="648f1-168">Valeur d’en-tête de mise à niveau par défaut.</span><span class="sxs-lookup"><span data-stu-id="648f1-168">The Upgrade header value by default.</span></span>
* <span data-ttu-id="648f1-169">`close` Lorsque l’en-tête de mise à niveau est manquant ou vide.</span><span class="sxs-lookup"><span data-stu-id="648f1-169">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="648f1-170">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="648f1-170">For more information, see the following articles:</span></span>

* [<span data-ttu-id="648f1-171">NGINX en tant que proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="648f1-171">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="648f1-172">Proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="648f1-172">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="648f1-173">Linux avec Apache</span><span class="sxs-lookup"><span data-stu-id="648f1-173">Linux with Apache</span></span>

<span data-ttu-id="648f1-174">Pour héberger une Blazor application derrière Apache sur Linux, configurez `ProxyPass` pour le trafic HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="648f1-174">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="648f1-175">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="648f1-175">In the following example:</span></span>

* <span data-ttu-id="648f1-176">Le serveur Kestrel est en cours d’exécution sur l’ordinateur hôte.</span><span class="sxs-lookup"><span data-stu-id="648f1-176">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="648f1-177">L’application écoute le trafic sur le port 5000.</span><span class="sxs-lookup"><span data-stu-id="648f1-177">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="648f1-178">Activez les modules suivants :</span><span class="sxs-lookup"><span data-stu-id="648f1-178">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="648f1-179">Consultez la console du navigateur pour les erreurs WebSocket.</span><span class="sxs-lookup"><span data-stu-id="648f1-179">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="648f1-180">Exemples d’erreurs :</span><span class="sxs-lookup"><span data-stu-id="648f1-180">Example errors:</span></span>

* <span data-ttu-id="648f1-181">Firefox ne peut pas établir de connexion au serveur sur ws://the-domain-name.tld/_blazor ?id=XXX.</span><span class="sxs-lookup"><span data-stu-id="648f1-181">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="648f1-182">Erreur : échec du démarrage du transport’WebSockets' : erreur : une erreur s’est produite avec le transport.</span><span class="sxs-lookup"><span data-stu-id="648f1-182">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="648f1-183">Erreur : impossible de démarrer le transport’LongPolling' : TypeError : This. transport n’est pas défini</span><span class="sxs-lookup"><span data-stu-id="648f1-183">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="648f1-184">Erreur : impossible de se connecter au serveur avec l’un des transports disponibles.</span><span class="sxs-lookup"><span data-stu-id="648f1-184">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="648f1-185">Échec de WebSocket</span><span class="sxs-lookup"><span data-stu-id="648f1-185">WebSockets failed</span></span>
* <span data-ttu-id="648f1-186">Erreur : impossible d’envoyer des données si la connexion n’est pas dans l’état « connecté ».</span><span class="sxs-lookup"><span data-stu-id="648f1-186">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="648f1-187">Pour plus d’informations, consultez la [documentation Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="648f1-187">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="648f1-188">Mesurer la latence du réseau</span><span class="sxs-lookup"><span data-stu-id="648f1-188">Measure network latency</span></span>

<span data-ttu-id="648f1-189">L' [interopérabilité js](xref:blazor/call-javascript-from-dotnet) peut être utilisée pour mesurer la latence du réseau, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="648f1-189">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code {
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            startTime = DateTime.UtcNow;
            var _ = await JS.InvokeAsync<string>("toString");
            latency = DateTime.UtcNow - startTime;
            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="648f1-190">Pour une expérience d’interface utilisateur raisonnable, nous recommandons une latence d’interface utilisateur soutenue de 250 ms ou moins.</span><span class="sxs-lookup"><span data-stu-id="648f1-190">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
