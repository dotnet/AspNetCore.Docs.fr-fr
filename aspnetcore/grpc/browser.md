---
title: Utiliser gRPC dans les applications de navigateur
author: jamesnk
description: Découvrez comment configurer les services gRPC sur ASP.NET Core à appeler à partir d’applications de navigateur à l’aide de gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 0967a70b498156d9c4ea8818ee1c80b37d9f2d87
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217477"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="ebeab-103">Utiliser gRPC dans les applications de navigateur</span><span class="sxs-lookup"><span data-stu-id="ebeab-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="ebeab-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="ebeab-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="ebeab-105">Découvrez comment configurer un service ASP.NET Core gRPC existant à appeler à partir d’applications de navigateur, à l’aide du protocole [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) .</span><span class="sxs-lookup"><span data-stu-id="ebeab-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="ebeab-106">gRPC-Web permet au navigateur et Blazor aux applications JavaScript d’appeler des services gRPC.</span><span class="sxs-lookup"><span data-stu-id="ebeab-106">gRPC-Web allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="ebeab-107">Il n’est pas possible d’appeler un service gRPC HTTP/2 à partir d’une application basée sur un navigateur.</span><span class="sxs-lookup"><span data-stu-id="ebeab-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="ebeab-108">les services gRPC hébergés dans ASP.NET Core peuvent être configurés pour prendre en charge gRPC-Web parallèlement à HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="ebeab-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="ebeab-109">Pour obtenir des instructions sur l’ajout d’un service gRPC à une application ASP.NET Core existante, consultez [Ajouter des services gRPC à une application ASP.net Core](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span><span class="sxs-lookup"><span data-stu-id="ebeab-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="ebeab-110">Pour obtenir des instructions sur la création d’un projet gRPC, consultez <xref:tutorials/grpc/grpc-start> .</span><span class="sxs-lookup"><span data-stu-id="ebeab-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="ebeab-111">gRPC-Web dans ASP.NET Core vs. Envoy</span><span class="sxs-lookup"><span data-stu-id="ebeab-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="ebeab-112">Il existe deux façons d’ajouter gRPC-Web à une application ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="ebeab-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="ebeab-113">Prenez en charge gRPC-Web en parallèle de gRPC HTTP/2 dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ebeab-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="ebeab-114">Cette option utilise l’intergiciel (middleware) fourni par le `Grpc.AspNetCore.Web` Package.</span><span class="sxs-lookup"><span data-stu-id="ebeab-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="ebeab-115">Utilisez la prise en charge gRPC-Web [du proxy Envoy](https://www.envoyproxy.io/) pour traduire GRPC-Web en gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="ebeab-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="ebeab-116">L’appel traduit est ensuite transféré vers l’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ebeab-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="ebeab-117">Chaque approche présente des avantages et des inconvénients.</span><span class="sxs-lookup"><span data-stu-id="ebeab-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="ebeab-118">Si l’environnement d’une application utilise déjà Envoy en tant que proxy, il peut être utile d’utiliser Envoy pour fournir la prise en charge de gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="ebeab-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="ebeab-119">Pour une solution de base pour gRPC-Web qui nécessite uniquement ASP.NET Core, `Grpc.AspNetCore.Web` est un bon choix.</span><span class="sxs-lookup"><span data-stu-id="ebeab-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="ebeab-120">Configurer gRPC-Web dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebeab-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="ebeab-121">les services gRPC hébergés dans ASP.NET Core peuvent être configurés pour prendre en charge gRPC-Web parallèlement à HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="ebeab-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="ebeab-122">gRPC-Web ne nécessite aucune modification des services.</span><span class="sxs-lookup"><span data-stu-id="ebeab-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="ebeab-123">La seule modification concerne la configuration du démarrage.</span><span class="sxs-lookup"><span data-stu-id="ebeab-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="ebeab-124">Pour activer gRPC-Web avec un service ASP.NET Core gRPC :</span><span class="sxs-lookup"><span data-stu-id="ebeab-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="ebeab-125">Ajoutez une référence au package [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="ebeab-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="ebeab-126">Configurez l’application pour utiliser gRPC-Web en ajoutant `UseGrpcWeb` et `EnableGrpcWeb` à *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ebeab-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="ebeab-127">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="ebeab-127">The preceding code:</span></span>

* <span data-ttu-id="ebeab-128">Ajoute l’intergiciel (middleware) Web gRPC, `UseGrpcWeb` , après le routage et avant les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="ebeab-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="ebeab-129">Spécifie que la `endpoints.MapGrpcService<GreeterService>()` méthode prend en charge gRPC-Web avec `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="ebeab-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="ebeab-130">L’intergiciel gRPC-Web peut également être configuré de sorte que tous les services prennent en charge gRPC-Web par défaut et non `EnableGrpcWeb` requis.</span><span class="sxs-lookup"><span data-stu-id="ebeab-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="ebeab-131">Spécifiez à `new GrpcWebOptions { DefaultEnabled = true }` quel moment l’intergiciel est ajouté.</span><span class="sxs-lookup"><span data-stu-id="ebeab-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="ebeab-132">Il existe un problème connu qui provoque l’échec de gRPC-Web lorsqu’il est [hébergé par HTTP.sys](xref:fundamentals/servers/httpsys) dans .net Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="ebeab-132">There is a known issue that causes gRPC-Web to fail when [hosted by HTTP.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="ebeab-133">Une solution de contournement pour obtenir gRPC-Web Working sur HTTP.sys est disponible [ici](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="ebeab-133">A workaround to get gRPC-Web working on HTTP.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="ebeab-134">gRPC-Web et CORS</span><span class="sxs-lookup"><span data-stu-id="ebeab-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="ebeab-135">La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="ebeab-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="ebeab-136">Cette restriction s’applique à la création d’appels gRPC-Web avec des applications de navigateur.</span><span class="sxs-lookup"><span data-stu-id="ebeab-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="ebeab-137">Par exemple, une application de navigateur servie par `https://www.contoso.com` est bloquée de l’appel de gRPC-services Web hébergés sur `https://services.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="ebeab-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="ebeab-138">Le partage des ressources Cross-Origin (CORS) peut être utilisé pour assouplir cette restriction.</span><span class="sxs-lookup"><span data-stu-id="ebeab-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="ebeab-139">Pour permettre à une application de navigateur d’effectuer des appels gRPC-Web Cross-Origin, configurez [cors dans ASP.net Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="ebeab-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="ebeab-140">Utilisez la prise en charge de CORS intégrée et exposez des en-têtes spécifiques à gRPC avec <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> .</span><span class="sxs-lookup"><span data-stu-id="ebeab-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="ebeab-141">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="ebeab-141">The preceding code:</span></span>

* <span data-ttu-id="ebeab-142">Appelle `AddCors` pour ajouter des services cors et configure une stratégie cors qui expose des en-têtes spécifiques à gRPC.</span><span class="sxs-lookup"><span data-stu-id="ebeab-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="ebeab-143">Appelle `UseCors` pour ajouter l’intergiciel (middleware) cors après le routage et avant les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="ebeab-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="ebeab-144">Spécifie que la `endpoints.MapGrpcService<GreeterService>()` méthode prend en charge cors avec `RequiresCors` .</span><span class="sxs-lookup"><span data-stu-id="ebeab-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

### <a name="grpc-web-and-streaming"></a><span data-ttu-id="ebeab-145">gRPC-Web et streaming</span><span class="sxs-lookup"><span data-stu-id="ebeab-145">gRPC-Web and streaming</span></span>

<span data-ttu-id="ebeab-146">Le gRPC traditionnel sur HTTP/2 prend en charge la diffusion en continu dans toutes les directions.</span><span class="sxs-lookup"><span data-stu-id="ebeab-146">Traditional gRPC over HTTP/2 supports streaming in all directions.</span></span> <span data-ttu-id="ebeab-147">gRPC-Web offre une prise en charge limitée de la diffusion en continu :</span><span class="sxs-lookup"><span data-stu-id="ebeab-147">gRPC-Web offers limited support for streaming:</span></span>

* <span data-ttu-id="ebeab-148">gRPC-les clients de navigateur Web ne prennent pas en charge l’appel des méthodes de diffusion en continu bidirectionnelle et de streaming client.</span><span class="sxs-lookup"><span data-stu-id="ebeab-148">gRPC-Web browser clients don't support calling client streaming and bidirectional streaming methods.</span></span>
* <span data-ttu-id="ebeab-149">ASP.NET Core Services gRPC hébergés sur Azure App Service et IIS ne prennent pas en charge la diffusion bidirectionnelle.</span><span class="sxs-lookup"><span data-stu-id="ebeab-149">ASP.NET Core gRPC services hosted on Azure App Service and IIS don't support bidirectional streaming.</span></span>

<span data-ttu-id="ebeab-150">Lors de l’utilisation de gRPC-Web, nous vous recommandons uniquement d’utiliser des méthodes unaires et des méthodes de diffusion de serveur.</span><span class="sxs-lookup"><span data-stu-id="ebeab-150">When using gRPC-Web, we only recommend the use of unary methods and server streaming methods.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="ebeab-151">Appeler gRPC-Web à partir du navigateur</span><span class="sxs-lookup"><span data-stu-id="ebeab-151">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="ebeab-152">Les applications de navigateur peuvent utiliser gRPC-Web pour appeler des services gRPC.</span><span class="sxs-lookup"><span data-stu-id="ebeab-152">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="ebeab-153">Il existe certaines exigences et limitations lors de l’appel de services gRPC avec gRPC-Web à partir du navigateur :</span><span class="sxs-lookup"><span data-stu-id="ebeab-153">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="ebeab-154">Le serveur doit avoir été configuré pour prendre en charge gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="ebeab-154">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="ebeab-155">Les appels de diffusion en continu et bidirectionnelle du client ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ebeab-155">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="ebeab-156">La diffusion en continu du serveur est prise en charge.</span><span class="sxs-lookup"><span data-stu-id="ebeab-156">Server streaming is supported.</span></span>
* <span data-ttu-id="ebeab-157">L’appel de services gRPC sur un autre domaine requiert la configuration de [cors](xref:security/cors) sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="ebeab-157">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="ebeab-158">JavaScript gRPC-client Web</span><span class="sxs-lookup"><span data-stu-id="ebeab-158">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="ebeab-159">Il existe un client JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="ebeab-159">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="ebeab-160">Pour obtenir des instructions sur l’utilisation de gRPC-Web à partir de JavaScript, consultez [écrire du code JavaScript client avec gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="ebeab-160">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="ebeab-161">Configurer gRPC-Web avec le client .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="ebeab-161">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="ebeab-162">Le client .NET gRPC peut être configuré pour effectuer des appels gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="ebeab-162">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="ebeab-163">Cela est utile pour les [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) applications qui sont hébergées dans le navigateur et qui ont les mêmes limitations http que le code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ebeab-163">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="ebeab-164">L’appel de gRPC-Web avec un client .NET est [identique à http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="ebeab-164">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="ebeab-165">La seule modification est la manière dont le canal est créé.</span><span class="sxs-lookup"><span data-stu-id="ebeab-165">The only modification is how the channel is created.</span></span>

<span data-ttu-id="ebeab-166">Pour utiliser gRPC-Web :</span><span class="sxs-lookup"><span data-stu-id="ebeab-166">To use gRPC-Web:</span></span>

* <span data-ttu-id="ebeab-167">Ajoutez une référence au package [GRPC .net. client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="ebeab-167">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="ebeab-168">Assurez-vous que la référence au package [GRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) est 2.29.0 ou supérieure.</span><span class="sxs-lookup"><span data-stu-id="ebeab-168">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="ebeab-169">Configurez le canal pour utiliser le `GrpcWebHandler` :</span><span class="sxs-lookup"><span data-stu-id="ebeab-169">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="ebeab-170">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="ebeab-170">The preceding code:</span></span>

* <span data-ttu-id="ebeab-171">Configure un canal pour utiliser gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="ebeab-171">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="ebeab-172">Crée un client et effectue un appel à l’aide du canal.</span><span class="sxs-lookup"><span data-stu-id="ebeab-172">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="ebeab-173">`GrpcWebHandler` possède les options de configuration suivantes :</span><span class="sxs-lookup"><span data-stu-id="ebeab-173">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="ebeab-174">**InnerHandler**: sous-jacent <xref:System.Net.Http.HttpMessageHandler> qui effectue la requête http gRPC, par exemple, `HttpClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="ebeab-174">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="ebeab-175">**GrpcWebMode**: type énumération qui spécifie si la requête http gRPC `Content-Type` est `application/grpc-web` ou `application/grpc-web-text` .</span><span class="sxs-lookup"><span data-stu-id="ebeab-175">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="ebeab-176">`GrpcWebMode.GrpcWeb` configure le contenu à envoyer sans encodage.</span><span class="sxs-lookup"><span data-stu-id="ebeab-176">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="ebeab-177">Valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="ebeab-177">Default value.</span></span>
    * <span data-ttu-id="ebeab-178">`GrpcWebMode.GrpcWebText` configure le contenu pour qu’il soit encodé en base64.</span><span class="sxs-lookup"><span data-stu-id="ebeab-178">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="ebeab-179">Requis pour les appels de diffusion en continu du serveur dans les navigateurs.</span><span class="sxs-lookup"><span data-stu-id="ebeab-179">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="ebeab-180">**HttpVersion**: protocole http `Version` utilisé pour définir [HttpRequestMessage. version](xref:System.Net.Http.HttpRequestMessage.Version) sur la requête http gRPC sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="ebeab-180">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="ebeab-181">gRPC-Web n’a pas besoin d’une version spécifique et ne remplace pas la valeur par défaut, sauf indication contraire.</span><span class="sxs-lookup"><span data-stu-id="ebeab-181">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ebeab-182">Les clients gRPC générés ont des méthodes synchrones et asynchrones pour appeler des méthodes unaires.</span><span class="sxs-lookup"><span data-stu-id="ebeab-182">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="ebeab-183">Par exemple, `SayHello` est Sync et `SayHelloAsync` est Async.</span><span class="sxs-lookup"><span data-stu-id="ebeab-183">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="ebeab-184">L’appel d’une méthode de synchronisation dans une Blazor WebAssembly application entraîne le blocage de l’application.</span><span class="sxs-lookup"><span data-stu-id="ebeab-184">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="ebeab-185">Les méthodes Async doivent toujours être utilisées dans Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="ebeab-185">Async methods must always be used in Blazor WebAssembly.</span></span>

### <a name="use-grpc-client-factory-with-grpc-web"></a><span data-ttu-id="ebeab-186">Utiliser la fabrique de clients gRPC avec gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="ebeab-186">Use gRPC client factory with gRPC-Web</span></span>

<span data-ttu-id="ebeab-187">Un client .NET compatible avec le Web gRPC peut être créé à l’aide de l’intégration de gRPC avec [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="ebeab-187">A gRPC-Web compatible .NET client can be created using gRPC's integration with [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).</span></span>

<span data-ttu-id="ebeab-188">Pour utiliser gRPC-Web avec la fabrique de clients :</span><span class="sxs-lookup"><span data-stu-id="ebeab-188">To use gRPC-Web with client factory:</span></span>

* <span data-ttu-id="ebeab-189">Ajoutez des références de package au fichier projet pour les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="ebeab-189">Add package references to the project file for the following packages:</span></span>
  * [<span data-ttu-id="ebeab-190">GRPC .net. client. Web</span><span class="sxs-lookup"><span data-stu-id="ebeab-190">Grpc.Net.Client.Web</span></span>](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [<span data-ttu-id="ebeab-191">GRPC .net. ClientFactory</span><span class="sxs-lookup"><span data-stu-id="ebeab-191">Grpc.Net.ClientFactory</span></span>](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* <span data-ttu-id="ebeab-192">Inscrire un client gRPC avec l’injection de dépendance (DI) à l’aide de la `AddGrpcClient` méthode d’extension générique.</span><span class="sxs-lookup"><span data-stu-id="ebeab-192">Register a gRPC client with dependency injection (DI) using the generic `AddGrpcClient` extension method.</span></span> <span data-ttu-id="ebeab-193">Dans une Blazor WebAssembly application, les services sont inscrits auprès de di dans `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="ebeab-193">In a Blazor WebAssembly app, services are registered with DI in `Program.cs`.</span></span>
* <span data-ttu-id="ebeab-194">Configurez `GrpcWebHandler` à l’aide de la <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="ebeab-194">Configure `GrpcWebHandler` using the <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> extension method.</span></span>

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

<span data-ttu-id="ebeab-195">Pour plus d’informations, consultez <xref:grpc/clientfactory>.</span><span class="sxs-lookup"><span data-stu-id="ebeab-195">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ebeab-196">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ebeab-196">Additional resources</span></span>

* [<span data-ttu-id="ebeab-197">gRPC pour le projet GitHub des clients Web</span><span class="sxs-lookup"><span data-stu-id="ebeab-197">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>
