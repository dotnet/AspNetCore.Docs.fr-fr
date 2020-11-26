---
title: Implémentations de serveurs web dans ASP.NET Core
author: rick-anderson
description: Découvrez les serveurs web Kestrel et HTTP.sys pour ASP.NET Core. Découvrez comment choisir un serveur et quand utiliser un serveur proxy inverse.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: a27fdd70963830d22b3501972d6150dde5e1ea54
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174595"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="dcc61-104">Implémentations de serveurs web dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dcc61-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="dcc61-105">De [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) et [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="dcc61-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="dcc61-106">Une application ASP.NET Core s’exécute avec une implémentation de serveur HTTP in-process.</span><span class="sxs-lookup"><span data-stu-id="dcc61-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="dcc61-107">L’implémentation du serveur écoute les requêtes HTTP et les expose à l’application sous forme d’ensemble de [fonctionnalités de requêtes](xref:fundamentals/request-features) composées dans un <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="dcc61-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="dcc61-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="dcc61-108">Kestrel</span></span>

<span data-ttu-id="dcc61-109">Kestrel est le serveur Web par défaut spécifié par les modèles de projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dcc61-109">Kestrel is the default web server specified by the ASP.NET Core project templates.</span></span>

<span data-ttu-id="dcc61-110">Utilisez Kestrel :</span><span class="sxs-lookup"><span data-stu-id="dcc61-110">Use Kestrel:</span></span>

* <span data-ttu-id="dcc61-111">Par lui même, c’est-à-dire en tant que serveur de périphérie traitant les requêtes en provenance directe d’un réseau, notamment d’Internet.</span><span class="sxs-lookup"><span data-stu-id="dcc61-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="dcc61-113">Avec un *serveur proxy inverse*, comme [IIS (Internet Information Services)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="dcc61-113">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="dcc61-114">Un serveur proxy inverse reçoit les requêtes HTTP en provenance d’Internet et les transmet à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="dcc61-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="dcc61-116">L’hébergement &mdash; de la configuration avec ou sans serveur proxy inverse &mdash; est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="dcc61-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="dcc61-117">Pour des conseils de configuration de Kestrel et des informations sur le moment d’utiliser Kestrel dans une configuration de proxy inverse, consultez <xref:fundamentals/servers/kestrel>.</span><span class="sxs-lookup"><span data-stu-id="dcc61-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="dcc61-118">Windows</span><span class="sxs-lookup"><span data-stu-id="dcc61-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="dcc61-119">ASP.NET Core est fourni avec les composants suivants :</span><span class="sxs-lookup"><span data-stu-id="dcc61-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="dcc61-120">[Serveur Kestrel](xref:fundamentals/servers/kestrel) : implémentation de serveur HTTP multiplateforme par défaut.</span><span class="sxs-lookup"><span data-stu-id="dcc61-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="dcc61-121">Le serveur HTTP IIS est un [serveur in-process](#hosting-models) pour IIS.</span><span class="sxs-lookup"><span data-stu-id="dcc61-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="dcc61-122">[Serveur HTTP.sys](xref:fundamentals/servers/httpsys) : serveur HTTP exclusivement Windows basé sur le [pilote de noyau HTTP.Sys et l’API Serveur HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="dcc61-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="dcc61-123">Lorsque vous utilisez [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), l’application s’exécute :</span><span class="sxs-lookup"><span data-stu-id="dcc61-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="dcc61-124">Dans le même processus que le processus de travail IIS (le [modèle d’hébergement in-process](#hosting-models)) avec le serveur http IIS.</span><span class="sxs-lookup"><span data-stu-id="dcc61-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="dcc61-125">*In-process* est la configuration recommandée.</span><span class="sxs-lookup"><span data-stu-id="dcc61-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="dcc61-126">Ou dans un processus séparé du processus Worker IIS (le [mode d’hébergement out-of-process](#hosting-models)) avec le [serveur Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="dcc61-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="dcc61-127">Le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) est un module IIS natif qui gère les requêtes IIS natives entre IIS et le serveur HTTP IIS in-process ou Kestrel.</span><span class="sxs-lookup"><span data-stu-id="dcc61-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="dcc61-128">Pour plus d’informations, consultez <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="dcc61-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="dcc61-129">Modèles d'hébergement</span><span class="sxs-lookup"><span data-stu-id="dcc61-129">Hosting models</span></span>

<span data-ttu-id="dcc61-130">En utilisant l’hébergement in-process, une application ASP.NET Core s’exécute dans le même processus que son processus de travail IIS.</span><span class="sxs-lookup"><span data-stu-id="dcc61-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="dcc61-131">L’hébergement in-process offre de meilleures performances que l’hébergement out-of-process parce que les requêtes n’ont pas de proxy sur l’adaptateur de bouclage, interface réseau qui retourne du trafic réseau sortant à la même machine.</span><span class="sxs-lookup"><span data-stu-id="dcc61-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="dcc61-132">IIS s’occupe de la gestion des processus par l’intermédiaire du [service d’activation des processus Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="dcc61-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="dcc61-133">Avec l’hébergement out-of-process, les applications ASP.NET Core s’exécutent dans un processus distinct du processus de travail IIS, et le module s’occupe de la gestion de processus.</span><span class="sxs-lookup"><span data-stu-id="dcc61-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="dcc61-134">Le module démarre le processus pour l’application ASP.NET Core quand la première requête arrive, et il redémarre l’application si elle s’arrête ou se bloque.</span><span class="sxs-lookup"><span data-stu-id="dcc61-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="dcc61-135">Il s’agit essentiellement du même comportement que celui des applications s’exécutant in-process, et qui sont gérées par le [service d’activation des processus Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="dcc61-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="dcc61-136">Pour plus d’informations et pour obtenir de l’aide sur la configuration, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="dcc61-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="dcc61-137">macOS</span><span class="sxs-lookup"><span data-stu-id="dcc61-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="dcc61-138">ASP.NET Core est fourni avec le [serveur Kestrel](xref:fundamentals/servers/kestrel) qui est le serveur HTTP multiplateforme par défaut.</span><span class="sxs-lookup"><span data-stu-id="dcc61-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="dcc61-139">Linux</span><span class="sxs-lookup"><span data-stu-id="dcc61-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="dcc61-140">ASP.NET Core est fourni avec le [serveur Kestrel](xref:fundamentals/servers/kestrel) qui est le serveur HTTP multiplateforme par défaut.</span><span class="sxs-lookup"><span data-stu-id="dcc61-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="dcc61-141">Windows</span><span class="sxs-lookup"><span data-stu-id="dcc61-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="dcc61-142">ASP.NET Core est fourni avec les composants suivants :</span><span class="sxs-lookup"><span data-stu-id="dcc61-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="dcc61-143">[Serveur Kestrel](xref:fundamentals/servers/kestrel) : serveur HTTP multiplateforme par défaut.</span><span class="sxs-lookup"><span data-stu-id="dcc61-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="dcc61-144">[Serveur HTTP.sys](xref:fundamentals/servers/httpsys) : serveur HTTP exclusivement Windows basé sur le [pilote de noyau HTTP.Sys et l’API Serveur HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="dcc61-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="dcc61-145">Lorsqu’[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) sont utilisés, l’application s’exécute dans un processus séparé du processus Worker IIS (le mode d’hébergement *out-of-process*) avec le [serveur Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="dcc61-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="dcc61-146">Étant donné que les applications ASP.NET Core s’exécutent dans un processus distinct du processus de travail IIS, le module s’occupe de la gestion du processus.</span><span class="sxs-lookup"><span data-stu-id="dcc61-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="dcc61-147">Le module démarre le processus pour l’application ASP.NET Core quand la première requête arrive, et il redémarre l’application si elle s’arrête ou se bloque.</span><span class="sxs-lookup"><span data-stu-id="dcc61-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="dcc61-148">Il s’agit essentiellement du même comportement que celui des applications s’exécutant in-process, et qui sont gérées par le [service d’activation des processus Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="dcc61-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="dcc61-149">Le schéma suivant illustre la relation entre IIS, le module ASP.NET Core et une application hébergée hors processus :</span><span class="sxs-lookup"><span data-stu-id="dcc61-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Module ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="dcc61-151">Les requêtes arrivent du web au pilote HTTP.sys en mode noyau.</span><span class="sxs-lookup"><span data-stu-id="dcc61-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="dcc61-152">Le pilote route les requêtes vers IIS sur le port configuré du site web, généralement 80 (HTTP) ou 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="dcc61-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="dcc61-153">Le module transfère les requêtes à Kestrel sur un port aléatoire pour l’application, qui n’est ni le port 80 ni le port 443.</span><span class="sxs-lookup"><span data-stu-id="dcc61-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="dcc61-154">Le module spécifie le port via une variable d’environnement au démarrage, et l' [intergiciel (middleware) d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configure le serveur pour l’écoute `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="dcc61-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="dcc61-155">Des vérifications supplémentaires sont effectuées, et les requêtes qui ne proviennent pas du module sont rejetées.</span><span class="sxs-lookup"><span data-stu-id="dcc61-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="dcc61-156">Le module ne prend pas en charge le transfert HTTPS : les requêtes sont donc transférées via HTTP, même si IIS les reçoit via HTTPS.</span><span class="sxs-lookup"><span data-stu-id="dcc61-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="dcc61-157">Dès que Kestrel sélectionne la requête dans le module, celle-ci est envoyée (push) dans le pipeline de middlewares d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dcc61-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="dcc61-158">Le pipeline de middlewares traite la requête et la passe en tant qu’instance de `HttpContext` à la logique de l’application.</span><span class="sxs-lookup"><span data-stu-id="dcc61-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="dcc61-159">L’intergiciel (middleware) ajouté par l’intégration d’IIS met à jour le schéma, l’adresse IP distante et la base du chemin pour prendre en compte le transfert de la requête à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="dcc61-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="dcc61-160">La réponse de l’application est ensuite repassée à IIS, qui la renvoie au client HTTP à l’origine de la requête.</span><span class="sxs-lookup"><span data-stu-id="dcc61-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="dcc61-161">Pour des conseils de configuration d’IIS et du module ASP.NET Core, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="dcc61-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="dcc61-162">macOS</span><span class="sxs-lookup"><span data-stu-id="dcc61-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="dcc61-163">ASP.NET Core est fourni avec le [serveur Kestrel](xref:fundamentals/servers/kestrel) qui est le serveur HTTP multiplateforme par défaut.</span><span class="sxs-lookup"><span data-stu-id="dcc61-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="dcc61-164">Linux</span><span class="sxs-lookup"><span data-stu-id="dcc61-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="dcc61-165">ASP.NET Core est fourni avec le [serveur Kestrel](xref:fundamentals/servers/kestrel) qui est le serveur HTTP multiplateforme par défaut.</span><span class="sxs-lookup"><span data-stu-id="dcc61-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="dcc61-166">Nginx avec Kestrel</span><span class="sxs-lookup"><span data-stu-id="dcc61-166">Nginx with Kestrel</span></span>

<span data-ttu-id="dcc61-167">Pour plus d’informations sur l’utilisation de Nginx sur Linux comme serveur proxy inverse pour Kestrel, consultez <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="dcc61-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="dcc61-168">Apache avec Kestrel</span><span class="sxs-lookup"><span data-stu-id="dcc61-168">Apache with Kestrel</span></span>

<span data-ttu-id="dcc61-169">Pour plus d’informations sur l’utilisation d’Apache sur Linux comme serveur proxy inverse pour Kestrel, consultez <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="dcc61-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="dcc61-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="dcc61-170">HTTP.sys</span></span>

<span data-ttu-id="dcc61-171">Si vos applications ASP.NET Core sont exécutées sur Windows, HTTP.sys est une alternative à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="dcc61-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="dcc61-172">Kestrel est généralement recommandé pour de meilleures performances.</span><span class="sxs-lookup"><span data-stu-id="dcc61-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="dcc61-173">HTTP.sys peut être utilisé dans les scénarios où l’application est exposée à Internet et où des fonctionnalités requises sont prises en charge par HTTP.sys, mais pas par Kestrel.</span><span class="sxs-lookup"><span data-stu-id="dcc61-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="dcc61-174">Pour plus d’informations, consultez <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="dcc61-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys communique directement avec Internet](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="dcc61-176">HTTP.sys peut également être utilisé pour les applications qui sont exposées seulement à un réseau interne.</span><span class="sxs-lookup"><span data-stu-id="dcc61-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys communique directement avec le réseau interne](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="dcc61-178">Pour obtenir des conseils de configuration de HTTP.sys, consultez <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="dcc61-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="dcc61-179">Infrastructure serveur d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dcc61-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="dcc61-180">Le <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> disponible dans la méthode `Startup.Configure` expose la propriété <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> du type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span><span class="sxs-lookup"><span data-stu-id="dcc61-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="dcc61-181">Kestrel et HTTP.sys exposent une seule fonctionnalité chacun, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, mais des implémentations serveur différentes peuvent exposer des fonctionnalités supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="dcc61-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="dcc61-182">`IServerAddressesFeature` permet de déterminer quel est le port lié à l’implémentation serveur au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="dcc61-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="dcc61-183">Serveurs personnalisés</span><span class="sxs-lookup"><span data-stu-id="dcc61-183">Custom servers</span></span>

<span data-ttu-id="dcc61-184">Si les serveurs intégrés ne répondent pas aux spécifications de l’application, vous pouvez créer une implémentation serveur personnalisée.</span><span class="sxs-lookup"><span data-stu-id="dcc61-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="dcc61-185">Le [guide OWIN (Open Web Interface pour .NET)](xref:fundamentals/owin) montre comment écrire une implémentation <xref:Microsoft.AspNetCore.Hosting.Server.IServer> basée sur [Nowin](https://github.com/Bobris/Nowin).</span><span class="sxs-lookup"><span data-stu-id="dcc61-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="dcc61-186">Seules les interfaces des fonctionnalités utilisées par l’application nécessitent une implémentation, bien qu’au minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> et <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> doivent être pris en charge.</span><span class="sxs-lookup"><span data-stu-id="dcc61-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="dcc61-187">Démarrage du serveur</span><span class="sxs-lookup"><span data-stu-id="dcc61-187">Server startup</span></span>

<span data-ttu-id="dcc61-188">Le serveur est lancé lorsque l’environnement de développement intégré (IDE) ou l’éditeur démarre l’application :</span><span class="sxs-lookup"><span data-stu-id="dcc61-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="dcc61-189">[Visual Studio](https://visualstudio.microsoft.com): les profils de lancement peuvent être utilisés pour démarrer l’application et le serveur avec [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [module ASP.net Core](xref:host-and-deploy/aspnet-core-module) ou la console.</span><span class="sxs-lookup"><span data-stu-id="dcc61-189">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="dcc61-190">[Visual Studio code](https://code.visualstudio.com/): l’application et le serveur sont démarrés par [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), ce qui active le débogueur CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="dcc61-190">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="dcc61-191">[Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/): l’application et le serveur sont démarrés par le [débogueur mono Soft-Mode](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="dcc61-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="dcc61-192">Lors du lancement de l’application à partir d’une invite de commandes dans le dossier du projet, [dotnet run](/dotnet/core/tools/dotnet-run) lance l’application et le serveur (Kestrel et HTTP.sys uniquement).</span><span class="sxs-lookup"><span data-stu-id="dcc61-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="dcc61-193">La configuration est spécifiée par l’option `-c|--configuration`, qui est définie sur `Debug` (par défaut) ou sur `Release`.</span><span class="sxs-lookup"><span data-stu-id="dcc61-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="dcc61-194">Un *launchSettings.jssur* fichier fournit une configuration lors du lancement d’une application avec `dotnet run` ou avec un débogueur intégré à des outils, tels que Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dcc61-194">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="dcc61-195">Si des profils de lancement sont présents dans un *launchSettings.jssur* le fichier, utilisez l' `--launch-profile {PROFILE NAME}` option avec la `dotnet run` commande ou sélectionnez le profil dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dcc61-195">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="dcc61-196">Pour plus d’informations, consultez les rubriques [dotnet run](/dotnet/core/tools/dotnet-run) et [Package de distribution de .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="dcc61-196">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="dcc61-197">Assistance HTTP/2</span><span class="sxs-lookup"><span data-stu-id="dcc61-197">HTTP/2 support</span></span>

<span data-ttu-id="dcc61-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) est pris en charge avec ASP.NET Core dans les scénarios de déploiement suivants :</span><span class="sxs-lookup"><span data-stu-id="dcc61-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="dcc61-199">Kestrel</span><span class="sxs-lookup"><span data-stu-id="dcc61-199">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="dcc61-200">Système d'exploitation</span><span class="sxs-lookup"><span data-stu-id="dcc61-200">Operating system</span></span>
    * <span data-ttu-id="dcc61-201">Windows Server 2016/Windows 10 ou version ultérieure&dagger;</span><span class="sxs-lookup"><span data-stu-id="dcc61-201">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="dcc61-202">Linux avec OpenSSL 1.0.2 ou version ultérieure (par exemple, Ubuntu 16.04 ou version ultérieure)</span><span class="sxs-lookup"><span data-stu-id="dcc61-202">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="dcc61-203">HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="dcc61-203">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="dcc61-204">Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="dcc61-204">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="dcc61-205">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="dcc61-205">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="dcc61-206">Windows Server 2016/Windows 10 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="dcc61-206">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="dcc61-207">Version cible de .Net Framework : non applicable aux déploiements HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="dcc61-207">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="dcc61-208">IIS (in-process)</span><span class="sxs-lookup"><span data-stu-id="dcc61-208">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="dcc61-209">Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="dcc61-209">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="dcc61-210">Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="dcc61-210">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="dcc61-211">IIS (out-of-process)</span><span class="sxs-lookup"><span data-stu-id="dcc61-211">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="dcc61-212">Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="dcc61-212">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="dcc61-213">Les connexions au serveur périphérique public utilisent HTTP/2, mais la connexion de proxy inverse à Kestrel utilise HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="dcc61-213">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="dcc61-214">Version cible de .Net Framework : non applicable aux déploiements IIS out-of-process.</span><span class="sxs-lookup"><span data-stu-id="dcc61-214">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="dcc61-215">&dagger;Kestrel propose une prise en charge limitée de HTTP/2 sous Windows Server 2012 R2 et Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="dcc61-215">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="dcc61-216">La prise en charge est limitée car la liste des suites de chiffrement TLS prises en charge sur ces systèmes d’exploitation est limitée.</span><span class="sxs-lookup"><span data-stu-id="dcc61-216">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="dcc61-217">Un certificat généré à l’aide d’Elliptic Curve Digital Signature algorithme (ECDSA) peut être requis pour sécuriser les connexions TLS.</span><span class="sxs-lookup"><span data-stu-id="dcc61-217">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="dcc61-218">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="dcc61-218">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="dcc61-219">Windows Server 2016/Windows 10 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="dcc61-219">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="dcc61-220">Version cible de .Net Framework : non applicable aux déploiements HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="dcc61-220">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="dcc61-221">IIS (out-of-process)</span><span class="sxs-lookup"><span data-stu-id="dcc61-221">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="dcc61-222">Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="dcc61-222">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="dcc61-223">Les connexions au serveur périphérique public utilisent HTTP/2, mais la connexion de proxy inverse à Kestrel utilise HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="dcc61-223">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="dcc61-224">Version cible de .Net Framework : non applicable aux déploiements IIS out-of-process.</span><span class="sxs-lookup"><span data-stu-id="dcc61-224">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="dcc61-225">Une connexion HTTP/2 doit utiliser la [négociation du protocole de la couche d’application (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) et TLS 1.2 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="dcc61-225">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="dcc61-226">Pour plus d’informations, consultez les rubriques qui se rapportent à vos scénarios de déploiement de serveur.</span><span class="sxs-lookup"><span data-stu-id="dcc61-226">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dcc61-227">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="dcc61-227">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
