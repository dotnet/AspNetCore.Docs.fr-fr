---
title: 'Héberger et déployer des ASP.NET Core :::no-loc(Blazor Server):::'
author: guardrex
description: 'Découvrez comment héberger et déployer une :::no-loc(Blazor Server)::: application à l’aide de ASP.net core.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: 74473eb5c0efcd8798d260b765c848d7e621e534
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055761"
---
# <a name="host-and-deploy-no-locblazor-server"></a><span data-ttu-id="fbc1f-103">Héberger et déployer :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="fbc1f-103">Host and deploy :::no-loc(Blazor Server):::</span></span>

<span data-ttu-id="fbc1f-104">Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="fbc1f-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="fbc1f-105">Valeurs de configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="fbc1f-105">Host configuration values</span></span>

<span data-ttu-id="fbc1f-106">les [ :::no-loc(Blazor Server)::: applications](xref:blazor/hosting-models#blazor-server) peuvent accepter des [valeurs de configuration d’hôte génériques](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="fbc1f-106">[:::no-loc(Blazor Server)::: apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="fbc1f-107">Déploiement</span><span class="sxs-lookup"><span data-stu-id="fbc1f-107">Deployment</span></span>

<span data-ttu-id="fbc1f-108">À l’aide du [ :::no-loc(Blazor Server)::: modèle d’hébergement](xref:blazor/hosting-models#blazor-server), :::no-loc(Blazor)::: est exécuté sur le serveur à partir d’une application ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-108">Using the [:::no-loc(Blazor Server)::: hosting model](xref:blazor/hosting-models#blazor-server), :::no-loc(Blazor)::: is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="fbc1f-109">Les mises à jour de l’interface utilisateur, la gestion des événements et les appels JavaScript sont gérés sur une [:::no-loc(SignalR):::](xref:signalr/introduction) connexion.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-109">UI updates, event handling, and JavaScript calls are handled over a [:::no-loc(SignalR):::](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="fbc1f-110">Un serveur web capable d’héberger une application ASP.NET Core est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="fbc1f-111">Visual Studio comprend le modèle de projet d' **:::no-loc(Blazor Server)::: application** ( `blazorserverside` modèle lors de l’utilisation de la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande).</span><span class="sxs-lookup"><span data-stu-id="fbc1f-111">Visual Studio includes the **:::no-loc(Blazor Server)::: App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="fbc1f-112">Extensibilité</span><span class="sxs-lookup"><span data-stu-id="fbc1f-112">Scalability</span></span>

<span data-ttu-id="fbc1f-113">Planifiez un déploiement pour tirer le meilleur parti de l’infrastructure disponible pour une :::no-loc(Blazor Server)::: application.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-113">Plan a deployment to make the best use of the available infrastructure for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="fbc1f-114">Consultez les ressources suivantes pour résoudre l' :::no-loc(Blazor Server)::: évolutivité des applications :</span><span class="sxs-lookup"><span data-stu-id="fbc1f-114">See the following resources to address :::no-loc(Blazor Server)::: app scalability:</span></span>

* [<span data-ttu-id="fbc1f-115">Notions de base des :::no-loc(Blazor Server)::: applications</span><span class="sxs-lookup"><span data-stu-id="fbc1f-115">Fundamentals of :::no-loc(Blazor Server)::: apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="fbc1f-116">Serveur de déploiement</span><span class="sxs-lookup"><span data-stu-id="fbc1f-116">Deployment server</span></span>

<span data-ttu-id="fbc1f-117">Lorsque vous envisagez l’évolutivité d’un serveur unique (montée en puissance), la mémoire disponible pour une application est probablement la première ressource que l’application épuisera en fonction des demandes des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="fbc1f-118">La mémoire disponible sur le serveur affecte les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="fbc1f-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="fbc1f-119">Nombre de circuits actifs qu’un serveur peut prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="fbc1f-120">Latence de l’interface utilisateur sur le client.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-120">UI latency on the client.</span></span>

<span data-ttu-id="fbc1f-121">Pour obtenir des conseils sur la création d’applications serveur sécurisées et évolutives :::no-loc(Blazor)::: , consultez <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="fbc1f-121">For guidance on building secure and scalable :::no-loc(Blazor)::: server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="fbc1f-122">Chaque circuit utilise environ 250 Ko de mémoire pour une application de type *Hello World* minimale.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World* -style app.</span></span> <span data-ttu-id="fbc1f-123">La taille d’un circuit dépend du code de l’application et des exigences de maintenance d’état associées à chaque composant.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="fbc1f-124">Nous vous recommandons de mesurer les demandes de ressources pendant le développement de votre application et de votre infrastructure, mais la ligne de base suivante peut être un point de départ pour la planification de votre cible de déploiement : Si vous pensez que votre application prend en charge 5 000 utilisateurs simultanés, envisagez de budgétiser au moins 1,3 Go de mémoire serveur vers l’application (ou ~ 273 Ko par utilisateur)</span><span class="sxs-lookup"><span data-stu-id="fbc1f-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="no-locsignalr-configuration"></a><span data-ttu-id="fbc1f-125">:::no-loc(SignalR)::: configuré</span><span class="sxs-lookup"><span data-stu-id="fbc1f-125">:::no-loc(SignalR)::: configuration</span></span>

<span data-ttu-id="fbc1f-126">:::no-loc(Blazor Server)::: les applications utilisent ASP.NET Core :::no-loc(SignalR)::: pour communiquer avec le navigateur.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-126">:::no-loc(Blazor Server)::: apps use ASP.NET Core :::no-loc(SignalR)::: to communicate with the browser.</span></span> <span data-ttu-id="fbc1f-127">[ :::no-loc(SignalR)::: les conditions d’hébergement et de mise à l’échelle de](xref:signalr/publish-to-azure-web-app) s’appliquent aux :::no-loc(Blazor Server)::: applications.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-127">[:::no-loc(SignalR):::'s hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to :::no-loc(Blazor Server)::: apps.</span></span>

<span data-ttu-id="fbc1f-128">:::no-loc(Blazor)::: fonctionne mieux lorsque vous utilisez WebSocket en tant que :::no-loc(SignalR)::: transport en raison d’une latence, d’une fiabilité et d’une [sécurité](xref:signalr/security)moindres.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-128">:::no-loc(Blazor)::: works best when using WebSockets as the :::no-loc(SignalR)::: transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="fbc1f-129">L’interrogation longue est utilisée par :::no-loc(SignalR)::: lorsque WebSocket n’est pas disponible ou lorsque l’application est configurée explicitement pour utiliser une interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-129">Long Polling is used by :::no-loc(SignalR)::: when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="fbc1f-130">Lors du déploiement sur Azure App Service, configurez l’application pour qu’elle utilise WebSockets dans les paramètres Portail Azure pour le service.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="fbc1f-131">Pour plus d’informations sur la configuration de l’application pour Azure App Service, consultez les [ :::no-loc(SignalR)::: instructions de publication](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="fbc1f-131">For details on configuring the app for Azure App Service, see the [:::no-loc(SignalR)::: publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-no-locsignalr-service"></a><span data-ttu-id="fbc1f-132">:::no-loc(SignalR):::Service Azure</span><span class="sxs-lookup"><span data-stu-id="fbc1f-132">Azure :::no-loc(SignalR)::: Service</span></span>

<span data-ttu-id="fbc1f-133">Nous vous recommandons d’utiliser le [ :::no-loc(SignalR)::: service Azure](xref:signalr/scale#azure-signalr-service) pour les :::no-loc(Blazor Server)::: applications.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-133">We recommend using the [Azure :::no-loc(SignalR)::: Service](xref:signalr/scale#azure-signalr-service) for :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="fbc1f-134">Le service permet la mise à l’échelle d’une :::no-loc(Blazor Server)::: application vers un grand nombre de connexions simultanées :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="fbc1f-134">The service allows for scaling up a :::no-loc(Blazor Server)::: app to a large number of concurrent :::no-loc(SignalR)::: connections.</span></span> <span data-ttu-id="fbc1f-135">En outre, la :::no-loc(SignalR)::: portée mondiale et les centres de données haute performance du service contribuent de manière significative à réduire la latence en raison de la géographie.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-135">In addition, the :::no-loc(SignalR)::: service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fbc1f-136">Lorsque les [WebSockets](https://wikipedia.org/wiki/WebSocket) sont désactivés, Azure App service simule une connexion en temps réel à l’aide de l’interrogation longue http.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-136">When [WebSockets](https://wikipedia.org/wiki/WebSocket) are disabled, Azure App Service simulates a real-time connection using HTTP long-polling.</span></span> <span data-ttu-id="fbc1f-137">L’interrogation longue HTTP est sensiblement plus lente que l’exécution avec WebSockets activé, qui n’utilise pas l’interrogation pour simuler une connexion client-serveur.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-137">HTTP long-polling is noticeably slower than running with WebSockets enabled, which doesn't use polling to simulate a client-server connection.</span></span>
>
> <span data-ttu-id="fbc1f-138">Nous vous recommandons d’utiliser WebSockets pour les :::no-loc(Blazor Server)::: applications déployées sur Azure App service.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-138">We recommend using WebSockets for :::no-loc(Blazor Server)::: apps deployed to Azure App Service.</span></span> <span data-ttu-id="fbc1f-139">Par défaut, le [ :::no-loc(SignalR)::: service Azure](xref:signalr/scale#azure-signalr-service) utilise WebSocket.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-139">The [Azure :::no-loc(SignalR)::: Service](xref:signalr/scale#azure-signalr-service) uses WebSockets by default.</span></span> <span data-ttu-id="fbc1f-140">Si l’application n’utilise pas le :::no-loc(SignalR)::: service Azure, consultez <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> .</span><span class="sxs-lookup"><span data-stu-id="fbc1f-140">If the app doesn't use the Azure :::no-loc(SignalR)::: Service, see <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>.</span></span>
>
> <span data-ttu-id="fbc1f-141">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="fbc1f-141">For more information, see:</span></span>
>
> * [<span data-ttu-id="fbc1f-142">Qu’est-ce que le :::no-loc(SignalR)::: service Azure ?</span><span class="sxs-lookup"><span data-stu-id="fbc1f-142">What is Azure :::no-loc(SignalR)::: Service?</span></span>](/azure/azure-signalr/signalr-overview)
> * [<span data-ttu-id="fbc1f-143">Guide des performances pour le :::no-loc(SignalR)::: service Azure</span><span class="sxs-lookup"><span data-stu-id="fbc1f-143">Performance guide for Azure :::no-loc(SignalR)::: Service</span></span>](/azure-signalr/signalr-concept-performance#performance-factors)

<span data-ttu-id="fbc1f-144">Pour configurer une application (et éventuellement approvisionner) le :::no-loc(SignalR)::: service Azure :</span><span class="sxs-lookup"><span data-stu-id="fbc1f-144">To configure an app (and optionally provision) the Azure :::no-loc(SignalR)::: Service:</span></span>

1. <span data-ttu-id="fbc1f-145">Activez le service pour prendre en charge les *sessions rémanentes* , où les clients sont [redirigés vers le même serveur lors du prérendu](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="fbc1f-145">Enable the service to support *sticky sessions* , where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="fbc1f-146">Définissez l' `ServerStickyMode` option ou la valeur de configuration sur `Required` .</span><span class="sxs-lookup"><span data-stu-id="fbc1f-146">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="fbc1f-147">En règle générale, une application crée la configuration à l’aide de l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="fbc1f-147">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="fbc1f-148">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fbc1f-148">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.Add:::no-loc(SignalR):::().AddAzure:::no-loc(SignalR):::(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.:::no-loc(SignalR):::.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="fbc1f-149">Configuration (utilisez l' **une** des approches suivantes) :</span><span class="sxs-lookup"><span data-stu-id="fbc1f-149">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="fbc1f-150">`:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="fbc1f-150">`:::no-loc(appsettings.json):::`:</span></span>

       ```json
       "Azure::::no-loc(SignalR)::::ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="fbc1f-151">Les paramètres d’application de **configuration** de l’app service  >  **Application settings** dans le portail Azure ( **nom** : `Azure::::no-loc(SignalR)::::ServerStickyMode` , **valeur** : `Required` ).</span><span class="sxs-lookup"><span data-stu-id="fbc1f-151">The app service's **Configuration** > **Application settings** in the Azure portal ( **Name** : `Azure::::no-loc(SignalR)::::ServerStickyMode`, **Value** : `Required`).</span></span>

1. <span data-ttu-id="fbc1f-152">Créez un profil de publication Azure Apps dans Visual Studio pour l' :::no-loc(Blazor Server)::: application.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-152">Create an Azure Apps publish profile in Visual Studio for the :::no-loc(Blazor Server)::: app.</span></span>
1. <span data-ttu-id="fbc1f-153">Ajoutez la dépendance du **:::no-loc(SignalR)::: service Azure** au profil.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-153">Add the **Azure :::no-loc(SignalR)::: Service** dependency to the profile.</span></span> <span data-ttu-id="fbc1f-154">Si l’abonnement Azure n’a pas d’instance de service Azure préexistante :::no-loc(SignalR)::: à attribuer à l’application, sélectionnez **créer une nouvelle instance de :::no-loc(SignalR)::: service Azure** pour approvisionner une nouvelle instance de service.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-154">If the Azure subscription doesn't have a pre-existing Azure :::no-loc(SignalR)::: Service instance to assign to the app, select **Create a new Azure :::no-loc(SignalR)::: Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="fbc1f-155">Publiez l’application dans Azure.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-155">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="fbc1f-156">IIS</span><span class="sxs-lookup"><span data-stu-id="fbc1f-156">IIS</span></span>

<span data-ttu-id="fbc1f-157">Lorsque vous utilisez IIS, activez :</span><span class="sxs-lookup"><span data-stu-id="fbc1f-157">When using IIS, enable:</span></span>

* <span data-ttu-id="fbc1f-158">[WebSocket sur IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="fbc1f-158">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="fbc1f-159">[Sessions rémanentes avec application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="fbc1f-159">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="fbc1f-160">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="fbc1f-160">Kubernetes</span></span>

<span data-ttu-id="fbc1f-161">Créez une définition d’entrée avec les [Annotations Kubernetes suivantes pour les sessions rémanentes](https://kubernetes.github.io/ingress-nginx/examples/affinity/:::no-loc(cookie):::/):</span><span class="sxs-lookup"><span data-stu-id="fbc1f-161">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/:::no-loc(cookie):::/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: ":::no-loc(cookie):::"
    nginx.ingress.kubernetes.io/session-:::no-loc(cookie):::-name: "affinity"
    nginx.ingress.kubernetes.io/session-:::no-loc(cookie):::-expires: "14400"
    nginx.ingress.kubernetes.io/session-:::no-loc(cookie):::-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="fbc1f-162">Linux avec Nginx</span><span class="sxs-lookup"><span data-stu-id="fbc1f-162">Linux with Nginx</span></span>

<span data-ttu-id="fbc1f-163">Pour que :::no-loc(SignalR)::: WebSocket fonctionne correctement, vérifiez que les `Upgrade` `Connection` en-têtes et du proxy sont définis sur les valeurs suivantes et qu’ils sont `$connection_upgrade` mappés à l’un ou l’autre des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="fbc1f-163">For :::no-loc(SignalR)::: WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="fbc1f-164">Valeur d’en-tête de mise à niveau par défaut.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-164">The Upgrade header value by default.</span></span>
* <span data-ttu-id="fbc1f-165">`close` Lorsque l’en-tête de mise à niveau est manquant ou vide.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-165">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="fbc1f-166">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="fbc1f-166">For more information, see the following articles:</span></span>

* [<span data-ttu-id="fbc1f-167">NGINX en tant que proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="fbc1f-167">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="fbc1f-168">Proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="fbc1f-168">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="fbc1f-169">Linux avec Apache</span><span class="sxs-lookup"><span data-stu-id="fbc1f-169">Linux with Apache</span></span>

<span data-ttu-id="fbc1f-170">Pour héberger une :::no-loc(Blazor)::: application derrière Apache sur Linux, configurez `ProxyPass` pour le trafic HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-170">To host a :::no-loc(Blazor)::: app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="fbc1f-171">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="fbc1f-171">In the following example:</span></span>

* <span data-ttu-id="fbc1f-172">Le serveur Kestrel est en cours d’exécution sur l’ordinateur hôte.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-172">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="fbc1f-173">L’application écoute le trafic sur le port 5000.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-173">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="fbc1f-174">Activez les modules suivants :</span><span class="sxs-lookup"><span data-stu-id="fbc1f-174">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="fbc1f-175">Consultez la console du navigateur pour les erreurs WebSocket.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-175">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="fbc1f-176">Exemples d’erreurs :</span><span class="sxs-lookup"><span data-stu-id="fbc1f-176">Example errors:</span></span>

* <span data-ttu-id="fbc1f-177">Firefox ne peut pas établir de connexion au serveur sur ws://the-domain-name.tld/_blazor ?id=XXX.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-177">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="fbc1f-178">Erreur : échec du démarrage du transport’WebSockets' : erreur : une erreur s’est produite avec le transport.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-178">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="fbc1f-179">Erreur : impossible de démarrer le transport’LongPolling' : TypeError : This. transport n’est pas défini</span><span class="sxs-lookup"><span data-stu-id="fbc1f-179">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="fbc1f-180">Erreur : impossible de se connecter au serveur avec l’un des transports disponibles.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-180">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="fbc1f-181">Échec de WebSocket</span><span class="sxs-lookup"><span data-stu-id="fbc1f-181">WebSockets failed</span></span>
* <span data-ttu-id="fbc1f-182">Erreur : impossible d’envoyer des données si la connexion n’est pas dans l’état « connecté ».</span><span class="sxs-lookup"><span data-stu-id="fbc1f-182">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="fbc1f-183">Pour plus d’informations, consultez la [documentation Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="fbc1f-183">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="fbc1f-184">Mesurer la latence du réseau</span><span class="sxs-lookup"><span data-stu-id="fbc1f-184">Measure network latency</span></span>

<span data-ttu-id="fbc1f-185">L' [interopérabilité js](xref:blazor/call-javascript-from-dotnet) peut être utilisée pour mesurer la latence du réseau, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="fbc1f-185">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="fbc1f-186">Pour une expérience d’interface utilisateur raisonnable, nous recommandons une latence d’interface utilisateur soutenue de 250 ms ou moins.</span><span class="sxs-lookup"><span data-stu-id="fbc1f-186">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
