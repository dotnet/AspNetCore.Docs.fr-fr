---
title: Services gRPC avec ASP.NET Core
author: juntaoluo
description: Découvrez les concepts de base liés à l’écriture de services gRPC avec ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: 1a5510364ee46165e275d07073ab087d79d65313
ms.sourcegitcommit: 50d3e939a90c5480df480f651dda032901468dd5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819040"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="e697b-103">Services gRPC avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e697b-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="e697b-104">Ce document montre comment prendre en main les services gRPC à l’aide de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e697b-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="e697b-105">Prérequis</span><span class="sxs-lookup"><span data-stu-id="e697b-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e697b-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e697b-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e697b-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e697b-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e697b-108">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="e697b-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="e697b-109">Bien démarrer avec le service gRPC dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e697b-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="e697b-110">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e697b-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e697b-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e697b-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e697b-112">Pour obtenir des instructions détaillées sur la création d’un projet gRPC, consultez [prise en main des services gRPC](xref:tutorials/grpc/grpc-start) .</span><span class="sxs-lookup"><span data-stu-id="e697b-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e697b-113">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="e697b-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e697b-114">Exécutez `dotnet new grpc -o GrpcGreeter` à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="e697b-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="e697b-115">Ajouter des services gRPC à une application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e697b-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="e697b-116">gRPC requiert le package [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="e697b-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="e697b-117">Configurer gRPC</span><span class="sxs-lookup"><span data-stu-id="e697b-117">Configure gRPC</span></span>

<span data-ttu-id="e697b-118">Dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="e697b-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="e697b-119">gRPC est activé avec la `AddGrpc` méthode.</span><span class="sxs-lookup"><span data-stu-id="e697b-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="e697b-120">Chaque service gRPC est ajouté au pipeline de routage via la `MapGrpcService` méthode.</span><span class="sxs-lookup"><span data-stu-id="e697b-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e697b-121">ASP.NET Core middleware et les fonctionnalités partagent le pipeline de routage. par conséquent, une application peut être configurée pour servir des gestionnaires de demandes supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="e697b-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="e697b-122">Les gestionnaires de demandes supplémentaires, tels que les contrôleurs MVC, fonctionnent en parallèle avec les services gRPC configurés.</span><span class="sxs-lookup"><span data-stu-id="e697b-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="server-options"></a><span data-ttu-id="e697b-123">Options de serveur</span><span class="sxs-lookup"><span data-stu-id="e697b-123">Server options</span></span>

<span data-ttu-id="e697b-124">les services gRPC peuvent être hébergés par tous les serveurs ASP.NET Core intégrés.</span><span class="sxs-lookup"><span data-stu-id="e697b-124">gRPC services can be hosted by all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="e697b-125">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e697b-125">Kestrel</span></span>
> * <span data-ttu-id="e697b-126">TestServer</span><span class="sxs-lookup"><span data-stu-id="e697b-126">TestServer</span></span>
> * <span data-ttu-id="e697b-127">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="e697b-127">IIS&dagger;</span></span>
> * <span data-ttu-id="e697b-128">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="e697b-128">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="e697b-129">&dagger;IIS requiert .NET 5 et Windows 10 Build 20241 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="e697b-129">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="e697b-130">&Dagger;HTTP.sys nécessite .NET 5 et Windows 10 Build 19529 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="e697b-130">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="e697b-131">Pour plus d’informations sur le choix du serveur approprié pour une application ASP.NET Core, consultez <xref:fundamentals/servers/index> .</span><span class="sxs-lookup"><span data-stu-id="e697b-131">For more information about choosing the right server for an ASP.NET Core app, see <xref:fundamentals/servers/index>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="e697b-132">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e697b-132">Kestrel</span></span>

<span data-ttu-id="e697b-133">[Kestrel](xref:fundamentals/servers/kestrel) est un serveur Web multiplateforme pour ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="e697b-133">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="e697b-134">Kestrel fournit les meilleures performances et l’utilisation de la mémoire, mais il ne dispose pas de certaines des fonctionnalités avancées de HTTP.sys telles que le partage de ports.</span><span class="sxs-lookup"><span data-stu-id="e697b-134">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="e697b-135">Points de terminaison Kestrel gRPC :</span><span class="sxs-lookup"><span data-stu-id="e697b-135">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="e697b-136">Nécessite HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e697b-136">Require HTTP/2.</span></span>
* <span data-ttu-id="e697b-137">Doit être sécurisé avec [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="e697b-137">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="e697b-138">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e697b-138">HTTP/2</span></span>

<span data-ttu-id="e697b-139">gRPC requiert HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e697b-139">gRPC requires HTTP/2.</span></span> <span data-ttu-id="e697b-140">gRPC pour ASP.NET Core valide [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) est `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="e697b-140">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="e697b-141">Kestrel [prend en charge http/2](xref:fundamentals/servers/kestrel/http2) sur la plupart des systèmes d’exploitation modernes.</span><span class="sxs-lookup"><span data-stu-id="e697b-141">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="e697b-142">Les points de terminaison Kestrel sont configurés pour prendre en charge les connexions HTTP/1.1 et HTTP/2 par défaut.</span><span class="sxs-lookup"><span data-stu-id="e697b-142">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="e697b-143">TLS</span><span class="sxs-lookup"><span data-stu-id="e697b-143">TLS</span></span>

<span data-ttu-id="e697b-144">Les points de terminaison Kestrel utilisés pour gRPC doivent être sécurisés avec TLS.</span><span class="sxs-lookup"><span data-stu-id="e697b-144">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="e697b-145">En cours de développement, un point de terminaison sécurisé avec TLS est automatiquement créé `https://localhost:5001` lorsque le certificat de développement ASP.net Core est présent.</span><span class="sxs-lookup"><span data-stu-id="e697b-145">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="e697b-146">Aucune configuration n'est requise.</span><span class="sxs-lookup"><span data-stu-id="e697b-146">No configuration is required.</span></span> <span data-ttu-id="e697b-147">Un `https` préfixe vérifie que le point de terminaison Kestrel utilise TLS.</span><span class="sxs-lookup"><span data-stu-id="e697b-147">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="e697b-148">En production, TLS doit être configuré de manière explicite.</span><span class="sxs-lookup"><span data-stu-id="e697b-148">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="e697b-149">Dans l' *appsettings.json* exemple suivant, un point de terminaison http/2 sécurisé avec TLS est fourni :</span><span class="sxs-lookup"><span data-stu-id="e697b-149">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="e697b-150">Vous pouvez également configurer les points de terminaison Kestrel dans *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e697b-150">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="e697b-151">Négociation de protocole</span><span class="sxs-lookup"><span data-stu-id="e697b-151">Protocol negotiation</span></span>

<span data-ttu-id="e697b-152">TLS est utilisé pour davantage que la sécurisation de la communication.</span><span class="sxs-lookup"><span data-stu-id="e697b-152">TLS is used for more than securing communication.</span></span> <span data-ttu-id="e697b-153">La négociation de [négociation de protocole de couche d’application (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) TLS est utilisée pour négocier le protocole de connexion entre le client et le serveur lorsqu’un point de terminaison prend en charge plusieurs protocoles.</span><span class="sxs-lookup"><span data-stu-id="e697b-153">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="e697b-154">Cette négociation détermine si la connexion utilise HTTP/1.1 ou HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e697b-154">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="e697b-155">Si un point de terminaison HTTP/2 est configuré sans TLS, le [ListenOptions. Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) du point de terminaison doit avoir la valeur `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="e697b-155">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="e697b-156">Un point de terminaison avec plusieurs protocoles (par exemple, `HttpProtocols.Http1AndHttp2` ) ne peut pas être utilisé sans TLS, car il n’y a aucune négociation.</span><span class="sxs-lookup"><span data-stu-id="e697b-156">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="e697b-157">Toutes les connexions au point de terminaison non sécurisé par défaut pour les appels HTTP/1.1 et gRPC échouent.</span><span class="sxs-lookup"><span data-stu-id="e697b-157">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="e697b-158">Pour plus d’informations sur l’activation de HTTP/2 et de TLS avec Kestrel, consultez [configuration du point de terminaison Kestrel](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="e697b-158">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="e697b-159">MacOS ne prend pas en charge ASP.NET Core gRPC avec TLS.</span><span class="sxs-lookup"><span data-stu-id="e697b-159">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="e697b-160">Une configuration supplémentaire est nécessaire pour exécuter correctement les services gRPC sur MacOS.</span><span class="sxs-lookup"><span data-stu-id="e697b-160">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="e697b-161">Pour plus d’informations, consultez [Impossible de démarrer l’application ASP.NET Core gRPC sur MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="e697b-161">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="iis"></a><span data-ttu-id="e697b-162">IIS</span><span class="sxs-lookup"><span data-stu-id="e697b-162">IIS</span></span>

<span data-ttu-id="e697b-163">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) est un serveur Web flexible, sécurisé et facile à gérer pour héberger des applications Web, y compris des ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="e697b-163">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="e697b-164">.NET 5 et Windows 10 Build 20241 ou version ultérieure sont requis pour héberger les services gRPC avec IIS.</span><span class="sxs-lookup"><span data-stu-id="e697b-164">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with IIS.</span></span>

<span data-ttu-id="e697b-165">IIS doit être configuré pour utiliser TLS et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e697b-165">IIS must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="e697b-166">Pour plus d’informations, consultez <xref:host-and-deploy/iis/protocols>.</span><span class="sxs-lookup"><span data-stu-id="e697b-166">For more information, see <xref:host-and-deploy/iis/protocols>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="e697b-167">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e697b-167">HTTP.sys</span></span>

<span data-ttu-id="e697b-168">[HTTP.sys](xref:fundamentals/servers/httpsys) est un serveur web pour ASP.NET Core qui s’exécute uniquement sous Windows.</span><span class="sxs-lookup"><span data-stu-id="e697b-168">[HTTP.sys](xref:fundamentals/servers/httpsys) is a web server for ASP.NET Core that only runs on Windows.</span></span> <span data-ttu-id="e697b-169">.NET 5 et Windows 10 Build 19529 ou version ultérieure sont requis pour héberger les services gRPC avec HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="e697b-169">.NET 5 and Windows 10 Build 19529 or later are required to host gRPC services with HTTP.sys.</span></span>

<span data-ttu-id="e697b-170">Les HTTP.sys doivent être configurés pour utiliser TLS et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e697b-170">HTTP.sys must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="e697b-171">Pour plus d’informations, consultez  [HTTP.sys la prise en charge http/2 du serveur Web](xref:fundamentals/servers/httpsys#http2-support).</span><span class="sxs-lookup"><span data-stu-id="e697b-171">For more information, see  [HTTP.sys web server HTTP/2 support](xref:fundamentals/servers/httpsys#http2-support).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="e697b-172">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e697b-172">Kestrel</span></span>

<span data-ttu-id="e697b-173">[Kestrel](xref:fundamentals/servers/kestrel) est un serveur Web multiplateforme pour ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="e697b-173">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="e697b-174">Kestrel fournit les meilleures performances et l’utilisation de la mémoire, mais il ne dispose pas de certaines des fonctionnalités avancées de HTTP.sys telles que le partage de ports.</span><span class="sxs-lookup"><span data-stu-id="e697b-174">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="e697b-175">Points de terminaison Kestrel gRPC :</span><span class="sxs-lookup"><span data-stu-id="e697b-175">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="e697b-176">Nécessite HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e697b-176">Require HTTP/2.</span></span>
* <span data-ttu-id="e697b-177">Doit être sécurisé avec [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="e697b-177">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="e697b-178">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e697b-178">HTTP/2</span></span>

<span data-ttu-id="e697b-179">gRPC requiert HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e697b-179">gRPC requires HTTP/2.</span></span> <span data-ttu-id="e697b-180">gRPC pour ASP.NET Core valide [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) est `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="e697b-180">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="e697b-181">Kestrel [prend en charge http/2](xref:fundamentals/servers/kestrel#http2-support) sur la plupart des systèmes d’exploitation modernes.</span><span class="sxs-lookup"><span data-stu-id="e697b-181">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="e697b-182">Les points de terminaison Kestrel sont configurés pour prendre en charge les connexions HTTP/1.1 et HTTP/2 par défaut.</span><span class="sxs-lookup"><span data-stu-id="e697b-182">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="e697b-183">TLS</span><span class="sxs-lookup"><span data-stu-id="e697b-183">TLS</span></span>

<span data-ttu-id="e697b-184">Les points de terminaison Kestrel utilisés pour gRPC doivent être sécurisés avec TLS.</span><span class="sxs-lookup"><span data-stu-id="e697b-184">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="e697b-185">En cours de développement, un point de terminaison sécurisé avec TLS est automatiquement créé `https://localhost:5001` lorsque le certificat de développement ASP.net Core est présent.</span><span class="sxs-lookup"><span data-stu-id="e697b-185">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="e697b-186">Aucune configuration n'est requise.</span><span class="sxs-lookup"><span data-stu-id="e697b-186">No configuration is required.</span></span> <span data-ttu-id="e697b-187">Un `https` préfixe vérifie que le point de terminaison Kestrel utilise TLS.</span><span class="sxs-lookup"><span data-stu-id="e697b-187">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="e697b-188">En production, TLS doit être configuré de manière explicite.</span><span class="sxs-lookup"><span data-stu-id="e697b-188">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="e697b-189">Dans l' *appsettings.json* exemple suivant, un point de terminaison http/2 sécurisé avec TLS est fourni :</span><span class="sxs-lookup"><span data-stu-id="e697b-189">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="e697b-190">Vous pouvez également configurer les points de terminaison Kestrel dans *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e697b-190">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="e697b-191">Négociation de protocole</span><span class="sxs-lookup"><span data-stu-id="e697b-191">Protocol negotiation</span></span>

<span data-ttu-id="e697b-192">TLS est utilisé pour davantage que la sécurisation de la communication.</span><span class="sxs-lookup"><span data-stu-id="e697b-192">TLS is used for more than securing communication.</span></span> <span data-ttu-id="e697b-193">La négociation de [négociation de protocole de couche d’application (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) TLS est utilisée pour négocier le protocole de connexion entre le client et le serveur lorsqu’un point de terminaison prend en charge plusieurs protocoles.</span><span class="sxs-lookup"><span data-stu-id="e697b-193">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="e697b-194">Cette négociation détermine si la connexion utilise HTTP/1.1 ou HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e697b-194">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="e697b-195">Si un point de terminaison HTTP/2 est configuré sans TLS, le [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) du point de terminaison doit avoir la valeur `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="e697b-195">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="e697b-196">Un point de terminaison avec plusieurs protocoles (par exemple, `HttpProtocols.Http1AndHttp2` ) ne peut pas être utilisé sans TLS, car il n’y a aucune négociation.</span><span class="sxs-lookup"><span data-stu-id="e697b-196">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="e697b-197">Toutes les connexions au point de terminaison non sécurisé par défaut pour les appels HTTP/1.1 et gRPC échouent.</span><span class="sxs-lookup"><span data-stu-id="e697b-197">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="e697b-198">Pour plus d’informations sur l’activation de HTTP/2 et de TLS avec Kestrel, consultez [configuration du point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="e697b-198">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="e697b-199">MacOS ne prend pas en charge ASP.NET Core gRPC avec TLS.</span><span class="sxs-lookup"><span data-stu-id="e697b-199">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="e697b-200">Une configuration supplémentaire est nécessaire pour exécuter correctement les services gRPC sur MacOS.</span><span class="sxs-lookup"><span data-stu-id="e697b-200">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="e697b-201">Pour plus d’informations, consultez [Impossible de démarrer l’application ASP.NET Core gRPC sur MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="e697b-201">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="e697b-202">Intégration avec les API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e697b-202">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="e697b-203">les services gRPC ont un accès complet aux fonctionnalités de ASP.NET Core telles que l' [injection de dépendances](xref:fundamentals/dependency-injection) (di) et la [journalisation](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="e697b-203">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e697b-204">Par exemple, l’implémentation de service peut résoudre un service d’enregistreur d’événements à partir du conteneur DI via le constructeur :</span><span class="sxs-lookup"><span data-stu-id="e697b-204">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="e697b-205">Par défaut, l’implémentation du service gRPC peut résoudre d’autres services DI avec n’importe quelle durée de vie (Singleton, étendue ou transitoire).</span><span class="sxs-lookup"><span data-stu-id="e697b-205">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="e697b-206">Résoudre HttpContext dans les méthodes gRPC</span><span class="sxs-lookup"><span data-stu-id="e697b-206">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="e697b-207">L’API gRPC permet d’accéder à certaines données de message HTTP/2, telles que la méthode, l’hôte, l’en-tête et les codes de fin.</span><span class="sxs-lookup"><span data-stu-id="e697b-207">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="e697b-208">L’accès s’effectue par le biais de l' `ServerCallContext` argument passé à chaque méthode gRPC :</span><span class="sxs-lookup"><span data-stu-id="e697b-208">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="e697b-209">`ServerCallContext` ne fournit pas un accès complet à `HttpContext` dans toutes les api ASP.net.</span><span class="sxs-lookup"><span data-stu-id="e697b-209">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="e697b-210">La `GetHttpContext` méthode d’extension fournit un accès complet au `HttpContext` représentant le message http/2 sous-jacent dans les API ASP.net :</span><span class="sxs-lookup"><span data-stu-id="e697b-210">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="e697b-211">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e697b-211">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
