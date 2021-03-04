---
title: Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge
author: rick-anderson
description: Découvrez la configuration des applications hébergées derrière des serveurs proxy et des équilibreurs de charge, qui masquent souvent des informations de requête importantes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 28a802414fd59f684a56e2b735140438d33be740
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109947"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="96ded-103">Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge</span><span class="sxs-lookup"><span data-stu-id="96ded-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="96ded-104">Par [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="96ded-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="96ded-105">Dans la configuration recommandée d’ASP.NET Core, l’application est hébergée à l’aide du module IIS/ASP.NET Core, de Nginx ou d’Apache.</span><span class="sxs-lookup"><span data-stu-id="96ded-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="96ded-106">Les serveurs proxy, les équilibreurs de charge et autres dispositifs réseau masquent souvent les informations sur la requête avant qu’elle n’atteigne l’application :</span><span class="sxs-lookup"><span data-stu-id="96ded-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="96ded-107">Quand les requêtes HTTPS sont transmises par proxy via HTTP, le schéma d’origine (HTTPS) est perdu et doit être transféré dans un en-tête.</span><span class="sxs-lookup"><span data-stu-id="96ded-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="96ded-108">Étant donné qu’une requête adressée à une application transite par le proxy au lieu de provenir directement de sa source sur Internet ou le réseau d’entreprise, l’adresse IP cliente d’origine doit également être transférée dans un en-tête.</span><span class="sxs-lookup"><span data-stu-id="96ded-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="96ded-109">Ces informations peuvent être importantes pour traiter les requêtes, par exemple dans les redirections, l’authentification, la génération de lien, l’évaluation des stratégies et la géolocalisation des clients.</span><span class="sxs-lookup"><span data-stu-id="96ded-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="96ded-110">En-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="96ded-110">Forwarded headers</span></span>

<span data-ttu-id="96ded-111">Par convention, les proxys transfèrent les informations dans des en-têtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="96ded-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="96ded-112">En-tête</span><span class="sxs-lookup"><span data-stu-id="96ded-112">Header</span></span> | <span data-ttu-id="96ded-113">Description</span><span class="sxs-lookup"><span data-stu-id="96ded-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="96ded-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="96ded-114">X-Forwarded-For</span></span> | <span data-ttu-id="96ded-115">Contient des informations sur le client à l’origine de la requête et les proxys suivants dans une chaîne de proxys.</span><span class="sxs-lookup"><span data-stu-id="96ded-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="96ded-116">Ce paramètre peut contenir des adresses IP (et, éventuellement, des numéros de port).</span><span class="sxs-lookup"><span data-stu-id="96ded-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="96ded-117">Dans une chaîne de serveurs proxy, le premier paramètre indique le client sur lequel la requête a été effectuée pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="96ded-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="96ded-118">Viennent ensuite les identificateurs des proxy suivants.</span><span class="sxs-lookup"><span data-stu-id="96ded-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="96ded-119">Le dernier proxy dans la chaîne ne figure pas dans la liste des paramètres.</span><span class="sxs-lookup"><span data-stu-id="96ded-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="96ded-120">L’adresse IP du dernier proxy et éventuellement un numéro de port sont disponibles en tant qu’adresse IP distante au niveau de la couche transport.</span><span class="sxs-lookup"><span data-stu-id="96ded-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="96ded-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="96ded-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="96ded-122">Valeur du schéma d’origine (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="96ded-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="96ded-123">La valeur peut également être une liste de schémas si la requête a franchi plusieurs proxys.</span><span class="sxs-lookup"><span data-stu-id="96ded-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="96ded-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="96ded-124">X-Forwarded-Host</span></span> | <span data-ttu-id="96ded-125">Valeur d’origine du champ d’en-tête de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="96ded-125">The original value of the Host header field.</span></span> <span data-ttu-id="96ded-126">En règle générale, les proxys ne modifient pas l’en-tête de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="96ded-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="96ded-127">Consultez le document [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pour plus d’informations sur une vulnérabilité par élévation de privilèges qui affecte les systèmes où le proxy ne valide pas les en-têtes d’hôte ou ne les limite pas à des valeurs correctes connues.</span><span class="sxs-lookup"><span data-stu-id="96ded-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="96ded-128">L’intergiciel d’en-têtes transféré ( <xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware> ) lit ces en-têtes et remplit les champs associés sur <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="96ded-128">The Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>), reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="96ded-129">Le middleware met à jour les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="96ded-129">The middleware updates:</span></span>

* <span data-ttu-id="96ded-130">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): défini à l’aide de la `X-Forwarded-For` valeur d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="96ded-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="96ded-131">Des paramètres supplémentaires déterminent la façon dont le middleware définit `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="96ded-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="96ded-132">Pour plus d’informations, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="96ded-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="96ded-133">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): défini à l’aide de la `X-Forwarded-Proto` valeur d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="96ded-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="96ded-134">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): défini à l’aide de la `X-Forwarded-Host` valeur d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="96ded-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="96ded-135">Pour plus d’informations sur le précédent, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/21615).</span><span class="sxs-lookup"><span data-stu-id="96ded-135">For more information on the preceding, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21615).</span></span>

<span data-ttu-id="96ded-136">Vous pouvez configurer les [paramètres par défaut](#forwarded-headers-middleware-options) du middleware des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="96ded-136">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="96ded-137">Les paramètres par défaut sont :</span><span class="sxs-lookup"><span data-stu-id="96ded-137">The default settings are:</span></span>

* <span data-ttu-id="96ded-138">Il y a *un seul proxy* entre l’application et la source des requêtes.</span><span class="sxs-lookup"><span data-stu-id="96ded-138">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="96ded-139">Seules des adresses de bouclage sont configurées pour les proxys connus et les réseaux connus.</span><span class="sxs-lookup"><span data-stu-id="96ded-139">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="96ded-140">Les en-têtes transférés sont nommés `X-Forwarded-For` et `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="96ded-140">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="96ded-141">Certaines appliances réseau nécessitent une configuration supplémentaire pour ajouter les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="96ded-141">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="96ded-142">Consultez les instructions du fabricant de votre appliance si des requêtes en proxy ne contiennent pas ces en-têtes quand elles atteignent l’application.</span><span class="sxs-lookup"><span data-stu-id="96ded-142">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="96ded-143">Si l’appliance utilise des noms d’en-têtes autres que `X-Forwarded-For` et `X-Forwarded-Proto`, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par l’appliance.</span><span class="sxs-lookup"><span data-stu-id="96ded-143">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="96ded-144">Pour plus d’informations, consultez [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options) et [Configuration d’un proxy qui utilise des noms d’en-têtes différents](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="96ded-144">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="96ded-145">Module ASP.NET Core et IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="96ded-145">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="96ded-146">Le middleware des en-têtes transférés est activé par défaut par le [middleware d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lorsque l’application est hébergée [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière IIS et le module ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="96ded-146">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="96ded-147">Le middleware des en-têtes transférés est activé pour s’exécuter en premier dans le pipeline des middlewares avec une configuration limitée propre au module ASP.NET Core en raison de problèmes d’approbation liés aux en-têtes transférés (par exemple, [usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="96ded-147">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="96ded-148">Le middleware est configuré pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` et est limité à un proxy localhost unique.</span><span class="sxs-lookup"><span data-stu-id="96ded-148">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="96ded-149">Si une configuration supplémentaire est requise, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="96ded-149">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="96ded-150">Autres scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="96ded-150">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="96ded-151">En dehors de l’utilisation de [l’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lors de l’hébergement [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), le middleware des en-têtes transférés n’est pas activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="96ded-151">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="96ded-152">L’intergiciel des en-têtes transférés doit être activé pour qu’une application puisse traiter les en-têtes transférés avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="96ded-152">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="96ded-153">Une fois l’intergiciel activé, si aucune option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> ne lui est spécifiée, les valeurs par défaut [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) ont pour valeur [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="96ded-153">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="96ded-154">Configurez l’intergiciel avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="96ded-154">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="96ded-155">Ordre intergiciel des en-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="96ded-155">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="96ded-156">L’intergiciel (middleware) d’en-têtes transférés doit s’exécuter avant tout autre intergiciel.</span><span class="sxs-lookup"><span data-stu-id="96ded-156">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="96ded-157">Cet ordre permet au middleware qui repose sur les informations des en-têtes transférés d’utiliser les valeurs d’en-tête pour le traitement.</span><span class="sxs-lookup"><span data-stu-id="96ded-157">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="96ded-158">L’intergiciel d’en-têtes transférés peut s’exécuter après les diagnostics et la gestion des erreurs, mais il doit être exécuté avant d’appeler `UseHsts` :</span><span class="sxs-lookup"><span data-stu-id="96ded-158">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="96ded-159">Vous pouvez également appeler `UseForwardedHeaders` avant les Diagnostics :</span><span class="sxs-lookup"><span data-stu-id="96ded-159">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="96ded-160">Si aucun <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> n’est spécifié dans `Startup.ConfigureServices` ou directement sur la méthode d’extension avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, les en-têtes par défaut à transférer sont [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="96ded-160">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="96ded-161">La propriété <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> doit être configurée avec les en-têtes à transférer.</span><span class="sxs-lookup"><span data-stu-id="96ded-161">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="96ded-162">Configuration de Nginx</span><span class="sxs-lookup"><span data-stu-id="96ded-162">Nginx configuration</span></span>

<span data-ttu-id="96ded-163">Pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="96ded-163">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="96ded-164">Pour plus d’informations, consultez [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX : utilisation de l’en-tête Forwarded).</span><span class="sxs-lookup"><span data-stu-id="96ded-164">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="96ded-165">Configuration d’Apache</span><span class="sxs-lookup"><span data-stu-id="96ded-165">Apache configuration</span></span>

<span data-ttu-id="96ded-166">`X-Forwarded-For` est ajouté automatiquement (consultez [Module Apache mod_proxy : en-têtes de requête du mandataire inverse](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="96ded-166">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="96ded-167">Pour plus d’informations sur la façon de transférer l’en-tête `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="96ded-167">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="96ded-168">Options du middleware des en-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="96ded-168">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="96ded-169"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> contrôlent le comportement de l’intergiciel des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="96ded-169"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="96ded-170">L’exemple suivant change les valeurs par défaut :</span><span class="sxs-lookup"><span data-stu-id="96ded-170">The following example changes the default values:</span></span>

* <span data-ttu-id="96ded-171">Limitez le nombre d’entrées dans les en-têtes transférés à `2`.</span><span class="sxs-lookup"><span data-stu-id="96ded-171">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="96ded-172">Ajoutez l’adresse de proxy connue `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="96ded-172">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="96ded-173">Changez le nom de l’en-tête transféré en remplaçant la valeur par défaut `X-Forwarded-For` par `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="96ded-173">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="96ded-174">Option</span><span class="sxs-lookup"><span data-stu-id="96ded-174">Option</span></span> | <span data-ttu-id="96ded-175">Description</span><span class="sxs-lookup"><span data-stu-id="96ded-175">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="96ded-176">Limite les hôtes par l’en-tête `X-Forwarded-Host` aux valeurs fournies.</span><span class="sxs-lookup"><span data-stu-id="96ded-176">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="96ded-177">Les valeurs sont comparées à l’aide de l’option ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="96ded-177">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="96ded-178">Les numéros de port doivent être exclus.</span><span class="sxs-lookup"><span data-stu-id="96ded-178">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="96ded-179">Si la liste est vide, tous les hôtes sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="96ded-179">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="96ded-180">Un caractère générique de niveau supérieur `*` autorise tous les hôtes non vides.</span><span class="sxs-lookup"><span data-stu-id="96ded-180">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="96ded-181">Les caractères génériques de sous-domaine sont autorisés, mais ne correspondent pas au domaine racine.</span><span class="sxs-lookup"><span data-stu-id="96ded-181">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="96ded-182">Par exemple, `*.contoso.com` correspond au sous-domaine `foo.contoso.com`, mais pas au domaine racine `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="96ded-182">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="96ded-183">Les noms d’hôte Unicode sont autorisés, mais sont convertis en [Punycode](https://tools.ietf.org/html/rfc3492) à des fins de correspondance.</span><span class="sxs-lookup"><span data-stu-id="96ded-183">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="96ded-184">Les [adresses IPv6](https://tools.ietf.org/html/rfc4291) doivent inclure des crochets de délimitation et adopter une [forme conventionnelle](https://tools.ietf.org/html/rfc4291#section-2.2) (par exemple, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="96ded-184">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="96ded-185">Les adresses IPv6 ne sont pas les seules à rechercher une égalité logique entre différents formats, et aucune canonicalisation n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="96ded-185">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="96ded-186">La non-restriction des hôtes autorisés peut permettre à un attaquant d’usurper les liens générés par le service.</span><span class="sxs-lookup"><span data-stu-id="96ded-186">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="96ded-187">La valeur par défaut est un `IList<string>` vide.</span><span class="sxs-lookup"><span data-stu-id="96ded-187">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="96ded-188">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="96ded-188">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="96ded-189">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-For` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="96ded-189">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="96ded-190">Par défaut, il s’agit de `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="96ded-190">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="96ded-191">Identifie les redirecteurs à traiter.</span><span class="sxs-lookup"><span data-stu-id="96ded-191">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="96ded-192">Consultez [l’énumération ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) pour obtenir la liste des champs qui s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="96ded-192">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="96ded-193">En règle générale, les valeurs attribuées à cette propriété sont `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="96ded-193">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="96ded-194">La valeur par défaut est [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="96ded-194">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="96ded-195">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="96ded-195">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="96ded-196">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Host` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="96ded-196">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="96ded-197">Par défaut, il s’agit de `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="96ded-197">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="96ded-198">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="96ded-198">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="96ded-199">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Proto` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="96ded-199">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="96ded-200">Par défaut, il s’agit de `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="96ded-200">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="96ded-201">Limite le nombre d’entrées dans les en-têtes qui sont traités.</span><span class="sxs-lookup"><span data-stu-id="96ded-201">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="96ded-202">Définissez cette option sur `null` pour désactiver la limite, mais seulement si `KnownProxies` ou `KnownNetworks` sont configurés.</span><span class="sxs-lookup"><span data-stu-id="96ded-202">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="96ded-203">Définir une valeur non `null` est une précaution (mais pas une garantie) pour vous prémunir contre les proxys mal configurés et les requêtes malveillantes provenant de canaux latéraux sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="96ded-203">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="96ded-204">Le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="96ded-204">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="96ded-205">Si la valeur par défaut (`1`) est utilisée, seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.</span><span class="sxs-lookup"><span data-stu-id="96ded-205">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="96ded-206">Par défaut, il s’agit de `1`.</span><span class="sxs-lookup"><span data-stu-id="96ded-206">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="96ded-207">Plages d’adresses des réseaux connus à partir desquels les en-têtes transférés peuvent être acceptés.</span><span class="sxs-lookup"><span data-stu-id="96ded-207">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="96ded-208">Indiquez les plages d’adresses IP à l’aide de la notation CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="96ded-208">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="96ded-209">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="96ded-209">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="96ded-210">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="96ded-210">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="96ded-211">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="96ded-211">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="96ded-212">La valeur par défaut est une `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenant une seule entrée pour `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="96ded-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="96ded-213">Adresses des proxy connus à partir desquels les en-têtes transférés peuvent être acceptés.</span><span class="sxs-lookup"><span data-stu-id="96ded-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="96ded-214">Utilisez `KnownProxies` pour spécifier des correspondances d’adresse IP exactes.</span><span class="sxs-lookup"><span data-stu-id="96ded-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="96ded-215">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="96ded-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="96ded-216">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="96ded-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="96ded-217">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="96ded-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="96ded-218">La valeur par défaut est une `IList`\<<xref:System.Net.IPAddress>> contenant une seule entrée pour `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="96ded-218">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="96ded-219">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="96ded-219">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="96ded-220">Par défaut, il s’agit de `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="96ded-220">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="96ded-221">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="96ded-221">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="96ded-222">Par défaut, il s’agit de `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="96ded-222">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="96ded-223">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="96ded-223">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="96ded-224">Par défaut, il s’agit de `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="96ded-224">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="96ded-225">Cette option impose que le nombre de valeurs d’en-tête soit synchronisé avec les [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="96ded-225">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="96ded-226">La valeur par défaut dans ASP.NET Core 1.x est `true`.</span><span class="sxs-lookup"><span data-stu-id="96ded-226">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="96ded-227">La valeur par défaut dans ASP.NET Core versions 2.0 ou ultérieures est `false`.</span><span class="sxs-lookup"><span data-stu-id="96ded-227">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="96ded-228">Scénarios et cas d’usage</span><span class="sxs-lookup"><span data-stu-id="96ded-228">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="96ded-229">Quand il est impossible d’ajouter des en-têtes transférés et que toutes les requêtes sont sécurisées</span><span class="sxs-lookup"><span data-stu-id="96ded-229">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="96ded-230">Dans certains cas, il peut être impossible d’ajouter des en-têtes transférés aux requêtes qui sont transmises par proxy à l’application.</span><span class="sxs-lookup"><span data-stu-id="96ded-230">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="96ded-231">Si le proxy impose que toutes les requêtes externes publiques soient de type HTTPS, vous pouvez définir le schéma manuellement dans `Startup.Configure` avant d’utiliser tout type de middleware :</span><span class="sxs-lookup"><span data-stu-id="96ded-231">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="96ded-232">Vous pouvez désactiver ce code avec une variable d’environnement ou un autre paramètre de configuration dans un environnement de préproduction ou de développement.</span><span class="sxs-lookup"><span data-stu-id="96ded-232">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="96ded-233">Traiter la base du chemin et les proxys qui changent le chemin de la requête</span><span class="sxs-lookup"><span data-stu-id="96ded-233">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="96ded-234">Certains proxys passent le chemin tel quel, mais avec un chemin de base d’application qui doit être supprimé afin que le routage fonctionne correctement.</span><span class="sxs-lookup"><span data-stu-id="96ded-234">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="96ded-235">Le middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) fractionne le chemin en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) et le chemin de base d’application en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="96ded-235">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="96ded-236">Si `/foo` est le chemin de base d’application pour un chemin de proxy passé en tant que `/foo/api/1`, le middleware définit `Request.PathBase` sur `/foo` et `Request.Path` sur `/api/1` avec la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="96ded-236">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="96ded-237">Le chemin et la base du chemin d’origine sont réappliqués quand le middleware est rappelé dans l’ordre inverse.</span><span class="sxs-lookup"><span data-stu-id="96ded-237">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="96ded-238">Pour plus d’informations sur le traitement des commandes de middleware, consultez le site <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="96ded-238">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="96ded-239">Si le proxy supprime le chemin (par exemple, en transférant `/foo/api/1` vers `/api/1`), corrigez les redirections et les liens en définissant la propriété [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la requête :</span><span class="sxs-lookup"><span data-stu-id="96ded-239">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="96ded-240">Si le proxy ajoute des données de chemin, abandonnez la partie du chemin pour corriger les redirections et les liens en utilisant <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> et en l’attribuant à la propriété <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> :</span><span class="sxs-lookup"><span data-stu-id="96ded-240">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="96ded-241">Configuration d’un proxy qui utilise des noms d’en-têtes différents</span><span class="sxs-lookup"><span data-stu-id="96ded-241">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="96ded-242">Si le proxy n’utilise pas d’en-têtes nommés `X-Forwarded-For` et `X-Forwarded-Proto` pour transférer l’adresse/le port du proxy ainsi que les informations du schéma d’origine, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par le proxy :</span><span class="sxs-lookup"><span data-stu-id="96ded-242">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="96ded-243">Transférer le schéma pour les serveurs proxy inverses Linux et non IIS</span><span class="sxs-lookup"><span data-stu-id="96ded-243">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="96ded-244">Les applications qui appellent <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> et <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> permettent de placer un site dans une boucle infinie, s’il est déployé sur Azure Linux App Service, une machine virtuelle Azure Linux ou derrière tout autre proxy inverse en dehors d’IIS.</span><span class="sxs-lookup"><span data-stu-id="96ded-244">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="96ded-245">Le proxy inverse met fin à TLS, et Kestrel n’a pas connaissance du schéma de requête approprié.</span><span class="sxs-lookup"><span data-stu-id="96ded-245">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="96ded-246">OAuth et OIDC sont également défaillants dans cette configuration, car ils génèrent des redirections incorrectes.</span><span class="sxs-lookup"><span data-stu-id="96ded-246">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="96ded-247"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> ajoute et configure le middleware (intergiciel) des en-têtes transférés durant l’exécution avec IIS, mais il n’existe aucune configuration automatique correspondante pour Linux (intégration d’Apache ou de Nginx).</span><span class="sxs-lookup"><span data-stu-id="96ded-247"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="96ded-248">Pour transférer le schéma à partir du proxy dans les scénarios non basés sur IIS, ajoutez et configurez le middleware des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="96ded-248">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="96ded-249">Dans `Startup.ConfigureServices`, utilisez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="96ded-249">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="96ded-250">Transfert de certificat</span><span class="sxs-lookup"><span data-stu-id="96ded-250">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="96ded-251">Azure</span><span class="sxs-lookup"><span data-stu-id="96ded-251">Azure</span></span>

<span data-ttu-id="96ded-252">Pour configurer Azure App Service pour le transfert de certificats, consultez [configurer l’authentification mutuelle TLS pour les Azure App service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="96ded-252">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="96ded-253">Les instructions suivantes concernent la configuration de l’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="96ded-253">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="96ded-254">Dans `Startup.Configure` , ajoutez le code suivant avant l’appel à `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="96ded-254">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="96ded-255">Configurez l’intergiciel (middleware) de transfert de certificats pour spécifier le nom d’en-tête utilisé par Azure.</span><span class="sxs-lookup"><span data-stu-id="96ded-255">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="96ded-256">Dans `Startup.ConfigureServices` , ajoutez le code suivant pour configurer l’en-tête à partir duquel l’intergiciel génère un certificat :</span><span class="sxs-lookup"><span data-stu-id="96ded-256">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="96ded-257">Autres proxys Web</span><span class="sxs-lookup"><span data-stu-id="96ded-257">Other web proxies</span></span>

<span data-ttu-id="96ded-258">Si vous utilisez un proxy qui n’est pas IIS ou le Application Request Routing d’Azure App Service (ARR), configurez le proxy pour transférer le certificat qu’il a reçu dans un en-tête HTTP.</span><span class="sxs-lookup"><span data-stu-id="96ded-258">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="96ded-259">Dans `Startup.Configure` , ajoutez le code suivant avant l’appel à `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="96ded-259">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="96ded-260">Configurez l’intergiciel (middleware) de transfert de certificat pour spécifier le nom d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="96ded-260">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="96ded-261">Dans `Startup.ConfigureServices` , ajoutez le code suivant pour configurer l’en-tête à partir duquel l’intergiciel génère un certificat :</span><span class="sxs-lookup"><span data-stu-id="96ded-261">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="96ded-262">Si le proxy n’encodage pas en base64 du certificat (comme c’est le cas avec Nginx), définissez l' `HeaderConverter` option.</span><span class="sxs-lookup"><span data-stu-id="96ded-262">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="96ded-263">Prenons l’exemple suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="96ded-263">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="96ded-264">Dépanner</span><span class="sxs-lookup"><span data-stu-id="96ded-264">Troubleshoot</span></span>

<span data-ttu-id="96ded-265">Quand des en-têtes ne sont pas transférés comme prévu, activez la [journalisation](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="96ded-265">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="96ded-266">Si les journaux ne fournissent pas suffisamment d’informations pour résoudre le problème, énumérez les en-têtes de requête reçus par le serveur.</span><span class="sxs-lookup"><span data-stu-id="96ded-266">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="96ded-267">Utilisez le middleware inclus pour écrire les en-têtes de requête dans une réponse d’application, ou consignez les en-têtes dans le fichier journal.</span><span class="sxs-lookup"><span data-stu-id="96ded-267">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="96ded-268">Pour écrire les en-têtes dans la réponse de l’application, placez le middleware inline de terminal suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="96ded-268">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="96ded-269">Il est possible d’écrire dans les journaux plutôt que dans le corps de la réponse ;</span><span class="sxs-lookup"><span data-stu-id="96ded-269">You can write to logs instead of the response body.</span></span> <span data-ttu-id="96ded-270">ainsi, le site fonctionnera normalement pendant le débogage.</span><span class="sxs-lookup"><span data-stu-id="96ded-270">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="96ded-271">Pour écrire dans les journaux plutôt que dans le corps de la réponse :</span><span class="sxs-lookup"><span data-stu-id="96ded-271">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="96ded-272">Injectez `ILogger<Startup>` dans la classe `Startup`, en suivant les instructions de [Créer des journaux dans Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="96ded-272">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="96ded-273">Placez le middleware inline suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="96ded-273">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="96ded-274">Lors du traitement, les valeurs `X-Forwarded-{For|Proto|Host}` sont déplacées vers `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="96ded-274">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="96ded-275">S’il existe plusieurs valeurs dans un en-tête donné, le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="96ded-275">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="96ded-276">La valeur par défaut de `ForwardLimit` est `1` si bien que seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.</span><span class="sxs-lookup"><span data-stu-id="96ded-276">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="96ded-277">L’adresse IP distante d’origine de la requête doit correspondre à une entrée dans les listes `KnownProxies` ou `KnownNetworks` pour que les en-têtes transférés soient traités.</span><span class="sxs-lookup"><span data-stu-id="96ded-277">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="96ded-278">L’usurpation des en-têtes s’en trouve limitée, car les redirecteurs émanant de proxys non approuvés ne sont pas acceptés.</span><span class="sxs-lookup"><span data-stu-id="96ded-278">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="96ded-279">Lorsqu’un proxy inconnu est détecté, la journalisation indique l’adresse du proxy :</span><span class="sxs-lookup"><span data-stu-id="96ded-279">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="96ded-280">Dans l’exemple précédent, 10.0.0.100 est un serveur proxy.</span><span class="sxs-lookup"><span data-stu-id="96ded-280">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="96ded-281">Si le serveur est un proxy approuvé, ajoutez l’adresse IP du serveur à la liste `KnownProxies` (ou ajoutez un réseau approuvé à la liste `KnownNetworks`) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="96ded-281">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="96ded-282">Pour plus d’informations, consultez la section [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="96ded-282">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="96ded-283">Autorisez uniquement des réseaux et des proxies approuvés pour transférer des en-têtes.</span><span class="sxs-lookup"><span data-stu-id="96ded-283">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="96ded-284">Sinon, des attaques d’[usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing) sont possibles.</span><span class="sxs-lookup"><span data-stu-id="96ded-284">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96ded-285">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="96ded-285">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="96ded-286">Microsoft Security Advisory CVE-2018-0787 : vulnérabilité d’élévation de privilèges ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="96ded-286">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="96ded-287">Dans la configuration recommandée d’ASP.NET Core, l’application est hébergée à l’aide du module IIS/ASP.NET Core, de Nginx ou d’Apache.</span><span class="sxs-lookup"><span data-stu-id="96ded-287">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="96ded-288">Les serveurs proxy, les équilibreurs de charge et autres dispositifs réseau masquent souvent les informations sur la requête avant qu’elle n’atteigne l’application :</span><span class="sxs-lookup"><span data-stu-id="96ded-288">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="96ded-289">Quand les requêtes HTTPS sont transmises par proxy via HTTP, le schéma d’origine (HTTPS) est perdu et doit être transféré dans un en-tête.</span><span class="sxs-lookup"><span data-stu-id="96ded-289">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="96ded-290">Étant donné qu’une requête adressée à une application transite par le proxy au lieu de provenir directement de sa source sur Internet ou le réseau d’entreprise, l’adresse IP cliente d’origine doit également être transférée dans un en-tête.</span><span class="sxs-lookup"><span data-stu-id="96ded-290">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="96ded-291">Ces informations peuvent être importantes pour traiter les requêtes, par exemple dans les redirections, l’authentification, la génération de lien, l’évaluation des stratégies et la géolocalisation des clients.</span><span class="sxs-lookup"><span data-stu-id="96ded-291">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="96ded-292">En-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="96ded-292">Forwarded headers</span></span>

<span data-ttu-id="96ded-293">Par convention, les proxys transfèrent les informations dans des en-têtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="96ded-293">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="96ded-294">En-tête</span><span class="sxs-lookup"><span data-stu-id="96ded-294">Header</span></span> | <span data-ttu-id="96ded-295">Description</span><span class="sxs-lookup"><span data-stu-id="96ded-295">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="96ded-296">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="96ded-296">X-Forwarded-For</span></span> | <span data-ttu-id="96ded-297">Contient des informations sur le client à l’origine de la requête et les proxys suivants dans une chaîne de proxys.</span><span class="sxs-lookup"><span data-stu-id="96ded-297">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="96ded-298">Ce paramètre peut contenir des adresses IP (et, éventuellement, des numéros de port).</span><span class="sxs-lookup"><span data-stu-id="96ded-298">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="96ded-299">Dans une chaîne de serveurs proxy, le premier paramètre indique le client sur lequel la requête a été effectuée pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="96ded-299">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="96ded-300">Viennent ensuite les identificateurs des proxy suivants.</span><span class="sxs-lookup"><span data-stu-id="96ded-300">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="96ded-301">Le dernier proxy dans la chaîne ne figure pas dans la liste des paramètres.</span><span class="sxs-lookup"><span data-stu-id="96ded-301">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="96ded-302">L’adresse IP du dernier proxy et éventuellement un numéro de port sont disponibles en tant qu’adresse IP distante au niveau de la couche transport.</span><span class="sxs-lookup"><span data-stu-id="96ded-302">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="96ded-303">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="96ded-303">X-Forwarded-Proto</span></span> | <span data-ttu-id="96ded-304">Valeur du schéma d’origine (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="96ded-304">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="96ded-305">La valeur peut également être une liste de schémas si la requête a franchi plusieurs proxys.</span><span class="sxs-lookup"><span data-stu-id="96ded-305">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="96ded-306">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="96ded-306">X-Forwarded-Host</span></span> | <span data-ttu-id="96ded-307">Valeur d’origine du champ d’en-tête de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="96ded-307">The original value of the Host header field.</span></span> <span data-ttu-id="96ded-308">En règle générale, les proxys ne modifient pas l’en-tête de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="96ded-308">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="96ded-309">Consultez le document [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pour plus d’informations sur une vulnérabilité par élévation de privilèges qui affecte les systèmes où le proxy ne valide pas les en-têtes d’hôte ou ne les limite pas à des valeurs correctes connues.</span><span class="sxs-lookup"><span data-stu-id="96ded-309">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="96ded-310">L’intergiciel des en-têtes transférés, dans le package [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lit ces en-têtes et renseigne les champs associés sur <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="96ded-310">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="96ded-311">Le middleware met à jour les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="96ded-311">The middleware updates:</span></span>

* <span data-ttu-id="96ded-312">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): défini à l’aide de la `X-Forwarded-For` valeur d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="96ded-312">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="96ded-313">Des paramètres supplémentaires déterminent la façon dont le middleware définit `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="96ded-313">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="96ded-314">Pour plus d’informations, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="96ded-314">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="96ded-315">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): défini à l’aide de la `X-Forwarded-Proto` valeur d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="96ded-315">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="96ded-316">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): défini à l’aide de la `X-Forwarded-Host` valeur d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="96ded-316">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="96ded-317">Vous pouvez configurer les [paramètres par défaut](#forwarded-headers-middleware-options) du middleware des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="96ded-317">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="96ded-318">Les paramètres par défaut sont :</span><span class="sxs-lookup"><span data-stu-id="96ded-318">The default settings are:</span></span>

* <span data-ttu-id="96ded-319">Il y a *un seul proxy* entre l’application et la source des requêtes.</span><span class="sxs-lookup"><span data-stu-id="96ded-319">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="96ded-320">Seules des adresses de bouclage sont configurées pour les proxys connus et les réseaux connus.</span><span class="sxs-lookup"><span data-stu-id="96ded-320">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="96ded-321">Les en-têtes transférés sont nommés `X-Forwarded-For` et `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="96ded-321">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="96ded-322">Certaines appliances réseau nécessitent une configuration supplémentaire pour ajouter les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="96ded-322">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="96ded-323">Consultez les instructions du fabricant de votre appliance si des requêtes en proxy ne contiennent pas ces en-têtes quand elles atteignent l’application.</span><span class="sxs-lookup"><span data-stu-id="96ded-323">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="96ded-324">Si l’appliance utilise des noms d’en-têtes autres que `X-Forwarded-For` et `X-Forwarded-Proto`, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par l’appliance.</span><span class="sxs-lookup"><span data-stu-id="96ded-324">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="96ded-325">Pour plus d’informations, consultez [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options) et [Configuration d’un proxy qui utilise des noms d’en-têtes différents](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="96ded-325">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="96ded-326">Module ASP.NET Core et IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="96ded-326">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="96ded-327">Le middleware des en-têtes transférés est activé par défaut par le [middleware d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lorsque l’application est hébergée [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière IIS et le module ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="96ded-327">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="96ded-328">Le middleware des en-têtes transférés est activé pour s’exécuter en premier dans le pipeline des middlewares avec une configuration limitée propre au module ASP.NET Core en raison de problèmes d’approbation liés aux en-têtes transférés (par exemple, [usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="96ded-328">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="96ded-329">Le middleware est configuré pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` et est limité à un proxy localhost unique.</span><span class="sxs-lookup"><span data-stu-id="96ded-329">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="96ded-330">Si une configuration supplémentaire est requise, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="96ded-330">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="96ded-331">Autres scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="96ded-331">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="96ded-332">En dehors de l’utilisation de [l’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lors de l’hébergement [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), le middleware des en-têtes transférés n’est pas activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="96ded-332">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="96ded-333">L’intergiciel des en-têtes transférés doit être activé pour qu’une application puisse traiter les en-têtes transférés avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="96ded-333">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="96ded-334">Une fois l’intergiciel activé, si aucune option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> ne lui est spécifiée, les valeurs par défaut [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) ont pour valeur [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="96ded-334">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="96ded-335">Configurez l’intergiciel avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="96ded-335">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="96ded-336">Appelez la méthode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` avant d’appeler d’autres intergiciels :</span><span class="sxs-lookup"><span data-stu-id="96ded-336">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="96ded-337">Si aucun <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> n’est spécifié dans `Startup.ConfigureServices` ou directement sur la méthode d’extension avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, les en-têtes par défaut à transférer sont [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="96ded-337">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="96ded-338">La propriété <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> doit être configurée avec les en-têtes à transférer.</span><span class="sxs-lookup"><span data-stu-id="96ded-338">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="96ded-339">Configuration de Nginx</span><span class="sxs-lookup"><span data-stu-id="96ded-339">Nginx configuration</span></span>

<span data-ttu-id="96ded-340">Pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="96ded-340">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="96ded-341">Pour plus d’informations, consultez [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX : utilisation de l’en-tête Forwarded).</span><span class="sxs-lookup"><span data-stu-id="96ded-341">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="96ded-342">Configuration d’Apache</span><span class="sxs-lookup"><span data-stu-id="96ded-342">Apache configuration</span></span>

<span data-ttu-id="96ded-343">`X-Forwarded-For` est ajouté automatiquement (consultez [Module Apache mod_proxy : en-têtes de requête du mandataire inverse](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="96ded-343">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="96ded-344">Pour plus d’informations sur la façon de transférer l’en-tête `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="96ded-344">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="96ded-345">Options du middleware des en-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="96ded-345">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="96ded-346"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> contrôlent le comportement de l’intergiciel des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="96ded-346"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="96ded-347">L’exemple suivant change les valeurs par défaut :</span><span class="sxs-lookup"><span data-stu-id="96ded-347">The following example changes the default values:</span></span>

* <span data-ttu-id="96ded-348">Limitez le nombre d’entrées dans les en-têtes transférés à `2`.</span><span class="sxs-lookup"><span data-stu-id="96ded-348">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="96ded-349">Ajoutez l’adresse de proxy connue `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="96ded-349">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="96ded-350">Changez le nom de l’en-tête transféré en remplaçant la valeur par défaut `X-Forwarded-For` par `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="96ded-350">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="96ded-351">Option</span><span class="sxs-lookup"><span data-stu-id="96ded-351">Option</span></span> | <span data-ttu-id="96ded-352">Description</span><span class="sxs-lookup"><span data-stu-id="96ded-352">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="96ded-353">Limite les hôtes par l’en-tête `X-Forwarded-Host` aux valeurs fournies.</span><span class="sxs-lookup"><span data-stu-id="96ded-353">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="96ded-354">Les valeurs sont comparées à l’aide de l’option ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="96ded-354">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="96ded-355">Les numéros de port doivent être exclus.</span><span class="sxs-lookup"><span data-stu-id="96ded-355">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="96ded-356">Si la liste est vide, tous les hôtes sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="96ded-356">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="96ded-357">Un caractère générique de niveau supérieur `*` autorise tous les hôtes non vides.</span><span class="sxs-lookup"><span data-stu-id="96ded-357">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="96ded-358">Les caractères génériques de sous-domaine sont autorisés, mais ne correspondent pas au domaine racine.</span><span class="sxs-lookup"><span data-stu-id="96ded-358">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="96ded-359">Par exemple, `*.contoso.com` correspond au sous-domaine `foo.contoso.com`, mais pas au domaine racine `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="96ded-359">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="96ded-360">Les noms d’hôte Unicode sont autorisés, mais sont convertis en [Punycode](https://tools.ietf.org/html/rfc3492) à des fins de correspondance.</span><span class="sxs-lookup"><span data-stu-id="96ded-360">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="96ded-361">Les [adresses IPv6](https://tools.ietf.org/html/rfc4291) doivent inclure des crochets de délimitation et adopter une [forme conventionnelle](https://tools.ietf.org/html/rfc4291#section-2.2) (par exemple, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="96ded-361">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="96ded-362">Les adresses IPv6 ne sont pas les seules à rechercher une égalité logique entre différents formats, et aucune canonicalisation n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="96ded-362">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="96ded-363">La non-restriction des hôtes autorisés peut permettre à un attaquant d’usurper les liens générés par le service.</span><span class="sxs-lookup"><span data-stu-id="96ded-363">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="96ded-364">La valeur par défaut est un `IList<string>` vide.</span><span class="sxs-lookup"><span data-stu-id="96ded-364">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="96ded-365">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="96ded-365">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="96ded-366">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-For` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="96ded-366">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="96ded-367">Par défaut, il s’agit de `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="96ded-367">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="96ded-368">Identifie les redirecteurs à traiter.</span><span class="sxs-lookup"><span data-stu-id="96ded-368">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="96ded-369">Consultez [l’énumération ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) pour obtenir la liste des champs qui s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="96ded-369">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="96ded-370">En règle générale, les valeurs attribuées à cette propriété sont `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="96ded-370">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="96ded-371">La valeur par défaut est [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="96ded-371">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="96ded-372">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="96ded-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="96ded-373">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Host` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="96ded-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="96ded-374">Par défaut, il s’agit de `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="96ded-374">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="96ded-375">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="96ded-375">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="96ded-376">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Proto` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="96ded-376">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="96ded-377">Par défaut, il s’agit de `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="96ded-377">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="96ded-378">Limite le nombre d’entrées dans les en-têtes qui sont traités.</span><span class="sxs-lookup"><span data-stu-id="96ded-378">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="96ded-379">Définissez cette option sur `null` pour désactiver la limite, mais seulement si `KnownProxies` ou `KnownNetworks` sont configurés.</span><span class="sxs-lookup"><span data-stu-id="96ded-379">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="96ded-380">Définir une valeur non `null` est une précaution (mais pas une garantie) pour vous prémunir contre les proxys mal configurés et les requêtes malveillantes provenant de canaux latéraux sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="96ded-380">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="96ded-381">Le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="96ded-381">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="96ded-382">Si la valeur par défaut (`1`) est utilisée, seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.</span><span class="sxs-lookup"><span data-stu-id="96ded-382">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="96ded-383">Par défaut, il s’agit de `1`.</span><span class="sxs-lookup"><span data-stu-id="96ded-383">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="96ded-384">Plages d’adresses des réseaux connus à partir desquels les en-têtes transférés peuvent être acceptés.</span><span class="sxs-lookup"><span data-stu-id="96ded-384">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="96ded-385">Indiquez les plages d’adresses IP à l’aide de la notation CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="96ded-385">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="96ded-386">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="96ded-386">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="96ded-387">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="96ded-387">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="96ded-388">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="96ded-388">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="96ded-389">Pour plus d’informations, consultez la section [Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="96ded-389">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="96ded-390">La valeur par défaut est une `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenant une seule entrée pour `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="96ded-390">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="96ded-391">Adresses des proxy connus à partir desquels les en-têtes transférés peuvent être acceptés.</span><span class="sxs-lookup"><span data-stu-id="96ded-391">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="96ded-392">Utilisez `KnownProxies` pour spécifier des correspondances d’adresse IP exactes.</span><span class="sxs-lookup"><span data-stu-id="96ded-392">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="96ded-393">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="96ded-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="96ded-394">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="96ded-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="96ded-395">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="96ded-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="96ded-396">Pour plus d’informations, consultez la section [Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="96ded-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="96ded-397">La valeur par défaut est une `IList`\<<xref:System.Net.IPAddress>> contenant une seule entrée pour `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="96ded-397">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="96ded-398">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="96ded-398">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="96ded-399">Par défaut, il s’agit de `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="96ded-399">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="96ded-400">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="96ded-400">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="96ded-401">Par défaut, il s’agit de `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="96ded-401">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="96ded-402">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="96ded-402">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="96ded-403">Par défaut, il s’agit de `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="96ded-403">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="96ded-404">Cette option impose que le nombre de valeurs d’en-tête soit synchronisé avec les [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="96ded-404">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="96ded-405">La valeur par défaut dans ASP.NET Core 1.x est `true`.</span><span class="sxs-lookup"><span data-stu-id="96ded-405">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="96ded-406">La valeur par défaut dans ASP.NET Core versions 2.0 ou ultérieures est `false`.</span><span class="sxs-lookup"><span data-stu-id="96ded-406">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="96ded-407">Scénarios et cas d’usage</span><span class="sxs-lookup"><span data-stu-id="96ded-407">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="96ded-408">Quand il est impossible d’ajouter des en-têtes transférés et que toutes les requêtes sont sécurisées</span><span class="sxs-lookup"><span data-stu-id="96ded-408">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="96ded-409">Dans certains cas, il peut être impossible d’ajouter des en-têtes transférés aux requêtes qui sont transmises par proxy à l’application.</span><span class="sxs-lookup"><span data-stu-id="96ded-409">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="96ded-410">Si le proxy impose que toutes les requêtes externes publiques soient de type HTTPS, vous pouvez définir le schéma manuellement dans `Startup.Configure` avant d’utiliser tout type de middleware :</span><span class="sxs-lookup"><span data-stu-id="96ded-410">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="96ded-411">Vous pouvez désactiver ce code avec une variable d’environnement ou un autre paramètre de configuration dans un environnement de préproduction ou de développement.</span><span class="sxs-lookup"><span data-stu-id="96ded-411">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="96ded-412">Traiter la base du chemin et les proxys qui changent le chemin de la requête</span><span class="sxs-lookup"><span data-stu-id="96ded-412">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="96ded-413">Certains proxys passent le chemin tel quel, mais avec un chemin de base d’application qui doit être supprimé afin que le routage fonctionne correctement.</span><span class="sxs-lookup"><span data-stu-id="96ded-413">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="96ded-414">Le middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) fractionne le chemin en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) et le chemin de base d’application en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="96ded-414">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="96ded-415">Si `/foo` est le chemin de base d’application pour un chemin de proxy passé en tant que `/foo/api/1`, le middleware définit `Request.PathBase` sur `/foo` et `Request.Path` sur `/api/1` avec la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="96ded-415">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="96ded-416">Le chemin et la base du chemin d’origine sont réappliqués quand le middleware est rappelé dans l’ordre inverse.</span><span class="sxs-lookup"><span data-stu-id="96ded-416">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="96ded-417">Pour plus d’informations sur le traitement des commandes de middleware, consultez le site <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="96ded-417">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="96ded-418">Si le proxy supprime le chemin (par exemple, en transférant `/foo/api/1` vers `/api/1`), corrigez les redirections et les liens en définissant la propriété [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la requête :</span><span class="sxs-lookup"><span data-stu-id="96ded-418">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="96ded-419">Si le proxy ajoute des données de chemin, abandonnez la partie du chemin pour corriger les redirections et les liens en utilisant <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> et en l’attribuant à la propriété <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> :</span><span class="sxs-lookup"><span data-stu-id="96ded-419">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="96ded-420">Configuration d’un proxy qui utilise des noms d’en-têtes différents</span><span class="sxs-lookup"><span data-stu-id="96ded-420">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="96ded-421">Si le proxy n’utilise pas d’en-têtes nommés `X-Forwarded-For` et `X-Forwarded-Proto` pour transférer l’adresse/le port du proxy ainsi que les informations du schéma d’origine, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par le proxy :</span><span class="sxs-lookup"><span data-stu-id="96ded-421">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="96ded-422">Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6</span><span class="sxs-lookup"><span data-stu-id="96ded-422">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="96ded-423">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1` ou `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="96ded-423">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="96ded-424">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="96ded-424">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="96ded-425">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="96ded-425">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="96ded-426">Dans l’exemple suivant, une adresse réseau qui fournit les en-têtes transférés est ajoutée à la liste `KnownNetworks` au format IPv6.</span><span class="sxs-lookup"><span data-stu-id="96ded-426">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="96ded-427">Adresse IPv4 : `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="96ded-427">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="96ded-428">Adresse IPv6 convertie : `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="96ded-428">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="96ded-429">Longueur de préfixe converti : 104</span><span class="sxs-lookup"><span data-stu-id="96ded-429">Converted prefix length: 104</span></span>

<span data-ttu-id="96ded-430">Vous pouvez également fournir l’adresse au format hexadécimal (`10.11.12.1` représenté dans IPv6 en tant que `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="96ded-430">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="96ded-431">Lors de la conversion d’une adresse IPv4 vers IPv6, ajoutez 96 à la longueur du préfixe CIDR (`8` dans l’exemple) pour prendre en compte le préfixe IPv6 `::ffff:` supplémentaire (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="96ded-431">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="96ded-432">Transférer le schéma pour les serveurs proxy inverses Linux et non IIS</span><span class="sxs-lookup"><span data-stu-id="96ded-432">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="96ded-433">Les applications qui appellent <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> et <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> permettent de placer un site dans une boucle infinie, s’il est déployé sur Azure Linux App Service, une machine virtuelle Azure Linux ou derrière tout autre proxy inverse en dehors d’IIS.</span><span class="sxs-lookup"><span data-stu-id="96ded-433">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="96ded-434">Le proxy inverse met fin à TLS, et Kestrel n’a pas connaissance du schéma de requête approprié.</span><span class="sxs-lookup"><span data-stu-id="96ded-434">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="96ded-435">OAuth et OIDC sont également défaillants dans cette configuration, car ils génèrent des redirections incorrectes.</span><span class="sxs-lookup"><span data-stu-id="96ded-435">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="96ded-436"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> ajoute et configure le middleware (intergiciel) des en-têtes transférés durant l’exécution avec IIS, mais il n’existe aucune configuration automatique correspondante pour Linux (intégration d’Apache ou de Nginx).</span><span class="sxs-lookup"><span data-stu-id="96ded-436"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="96ded-437">Pour transférer le schéma à partir du proxy dans les scénarios non basés sur IIS, ajoutez et configurez le middleware des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="96ded-437">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="96ded-438">Dans `Startup.ConfigureServices`, utilisez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="96ded-438">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="96ded-439">Dépanner</span><span class="sxs-lookup"><span data-stu-id="96ded-439">Troubleshoot</span></span>

<span data-ttu-id="96ded-440">Quand des en-têtes ne sont pas transférés comme prévu, activez la [journalisation](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="96ded-440">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="96ded-441">Si les journaux ne fournissent pas suffisamment d’informations pour résoudre le problème, énumérez les en-têtes de requête reçus par le serveur.</span><span class="sxs-lookup"><span data-stu-id="96ded-441">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="96ded-442">Utilisez le middleware inclus pour écrire les en-têtes de requête dans une réponse d’application, ou consignez les en-têtes dans le fichier journal.</span><span class="sxs-lookup"><span data-stu-id="96ded-442">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="96ded-443">Pour écrire les en-têtes dans la réponse de l’application, placez le middleware inline de terminal suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="96ded-443">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="96ded-444">Il est possible d’écrire dans les journaux plutôt que dans le corps de la réponse ;</span><span class="sxs-lookup"><span data-stu-id="96ded-444">You can write to logs instead of the response body.</span></span> <span data-ttu-id="96ded-445">ainsi, le site fonctionnera normalement pendant le débogage.</span><span class="sxs-lookup"><span data-stu-id="96ded-445">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="96ded-446">Pour écrire dans les journaux plutôt que dans le corps de la réponse :</span><span class="sxs-lookup"><span data-stu-id="96ded-446">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="96ded-447">Injectez `ILogger<Startup>` dans la classe `Startup`, en suivant les instructions de [Créer des journaux dans Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="96ded-447">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="96ded-448">Placez le middleware inline suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="96ded-448">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="96ded-449">Lors du traitement, les valeurs `X-Forwarded-{For|Proto|Host}` sont déplacées vers `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="96ded-449">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="96ded-450">S’il existe plusieurs valeurs dans un en-tête donné, le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="96ded-450">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="96ded-451">La valeur par défaut de `ForwardLimit` est `1` si bien que seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.</span><span class="sxs-lookup"><span data-stu-id="96ded-451">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="96ded-452">L’adresse IP distante d’origine de la requête doit correspondre à une entrée dans les listes `KnownProxies` ou `KnownNetworks` pour que les en-têtes transférés soient traités.</span><span class="sxs-lookup"><span data-stu-id="96ded-452">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="96ded-453">L’usurpation des en-têtes s’en trouve limitée, car les redirecteurs émanant de proxys non approuvés ne sont pas acceptés.</span><span class="sxs-lookup"><span data-stu-id="96ded-453">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="96ded-454">Lorsqu’un proxy inconnu est détecté, la journalisation indique l’adresse du proxy :</span><span class="sxs-lookup"><span data-stu-id="96ded-454">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="96ded-455">Dans l’exemple précédent, 10.0.0.100 est un serveur proxy.</span><span class="sxs-lookup"><span data-stu-id="96ded-455">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="96ded-456">Si le serveur est un proxy approuvé, ajoutez l’adresse IP du serveur à la liste `KnownProxies` (ou ajoutez un réseau approuvé à la liste `KnownNetworks`) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="96ded-456">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="96ded-457">Pour plus d’informations, consultez la section [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="96ded-457">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="96ded-458">Autorisez uniquement des réseaux et des proxies approuvés pour transférer des en-têtes.</span><span class="sxs-lookup"><span data-stu-id="96ded-458">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="96ded-459">Sinon, des attaques d’[usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing) sont possibles.</span><span class="sxs-lookup"><span data-stu-id="96ded-459">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96ded-460">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="96ded-460">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="96ded-461">Microsoft Security Advisory CVE-2018-0787 : vulnérabilité d’élévation de privilèges ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="96ded-461">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
