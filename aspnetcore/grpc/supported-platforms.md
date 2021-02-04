---
title: gRPC sur les plateformes prises en charge par .NET
author: jamesnk
description: En savoir plus sur les plateformes prises en charge pour gRPC sur .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530162"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="35a5b-103">gRPC sur les plateformes prises en charge par .NET</span><span class="sxs-lookup"><span data-stu-id="35a5b-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="35a5b-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="35a5b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="35a5b-105">Cet article décrit la configuration requise et les plateformes prises en charge pour l’utilisation de gRPC avec .NET.</span><span class="sxs-lookup"><span data-stu-id="35a5b-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="35a5b-106">gRPC tire parti des fonctionnalités avancées disponibles dans HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="35a5b-106">gRPC takes advantage of advanced features available in  HTTP/2.</span></span> <span data-ttu-id="35a5b-107">HTTP/2 n’est pas pris en charge dans tous les pays, mais un deuxième format filaire à l’aide de HTTP/1.1 est disponible pour gRPC :</span><span class="sxs-lookup"><span data-stu-id="35a5b-107">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="35a5b-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC sur HTTP/2 indique comment gRPC est généralement utilisé.</span><span class="sxs-lookup"><span data-stu-id="35a5b-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="35a5b-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modifie le protocole gRPC pour qu’il soit compatible avec HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="35a5b-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="35a5b-110">gRPC-Web peut être utilisé à d’autres endroits, notamment s’il peut être appelé par des applications de navigateur.</span><span class="sxs-lookup"><span data-stu-id="35a5b-110">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="35a5b-111">Deux fonctionnalités gRPC avancées ne sont plus prises en charge : la diffusion en continu du client et la diffusion bidirectionnelle.</span><span class="sxs-lookup"><span data-stu-id="35a5b-111">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="35a5b-112">gRPC sur .NET prend en charge les formats câblés.</span><span class="sxs-lookup"><span data-stu-id="35a5b-112">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="35a5b-113">gRPC sur HTTP/2 est utilisé par défaut.</span><span class="sxs-lookup"><span data-stu-id="35a5b-113">gRPC over HTTP/2 is used by default.</span></span> <span data-ttu-id="35a5b-114">Pour plus d’informations sur la configuration de gRPC-Web, consultez <xref:grpc/browser> .</span><span class="sxs-lookup"><span data-stu-id="35a5b-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="35a5b-115">Exigences relatives aux appareils</span><span class="sxs-lookup"><span data-stu-id="35a5b-115">Device requirements</span></span>

<span data-ttu-id="35a5b-116">gRPC sur .NET prend en charge tous les appareils que .NET Core prend en charge.</span><span class="sxs-lookup"><span data-stu-id="35a5b-116">gRPC on .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="35a5b-117">Windows</span><span class="sxs-lookup"><span data-stu-id="35a5b-117">Windows</span></span>
> * <span data-ttu-id="35a5b-118">Linux</span><span class="sxs-lookup"><span data-stu-id="35a5b-118">Linux</span></span>
> * <span data-ttu-id="35a5b-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="35a5b-119">macOS&dagger;</span></span>
> * <span data-ttu-id="35a5b-120">Navigateurs&Dagger;</span><span class="sxs-lookup"><span data-stu-id="35a5b-120">Browsers&Dagger;</span></span>

<span data-ttu-id="35a5b-121">&dagger;[MacOS ne prend pas en charge l’hébergement d’applications ASP.net core avec HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="35a5b-121">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span> <span data-ttu-id="35a5b-122">les clients gRPC sur macOS peuvent appeler des services distants qui utilisent le protocole HTTPs.</span><span class="sxs-lookup"><span data-stu-id="35a5b-122">gRPC clients on macOS can call remote services that use HTTPS.</span></span>

<span data-ttu-id="35a5b-123">&Dagger;Blazor WebAssembly les applications peuvent appeler gRPC services avec gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="35a5b-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="35a5b-124">Configuration requise pour ASP.NET Core Server</span><span class="sxs-lookup"><span data-stu-id="35a5b-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="35a5b-125">les services gRPC peuvent être hébergés sur tous les serveurs ASP.NET Core intégrés.</span><span class="sxs-lookup"><span data-stu-id="35a5b-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="35a5b-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="35a5b-126">Kestrel</span></span>
> * <span data-ttu-id="35a5b-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="35a5b-127">TestServer</span></span>
> * <span data-ttu-id="35a5b-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="35a5b-128">IIS&dagger;</span></span>
> * <span data-ttu-id="35a5b-129">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="35a5b-129">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="35a5b-130">&dagger;IIS requiert .NET 5 et Windows 10 Build 20241 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="35a5b-130">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="35a5b-131">&Dagger;HTTP.sys nécessite .NET 5 et Windows 10 Build 19529 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="35a5b-131">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="35a5b-132">Pour plus d’informations sur la configuration des serveurs ASP.NET Core pour exécuter gRPC, consultez <xref:grpc/aspnetcore#server-options> .</span><span class="sxs-lookup"><span data-stu-id="35a5b-132">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="35a5b-133">Configuration requise pour la version .NET</span><span class="sxs-lookup"><span data-stu-id="35a5b-133">.NET version requirements</span></span>

<span data-ttu-id="35a5b-134">gRPC sur .NET prend en charge .NET Core 3 et .NET 5 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="35a5b-134">gRPC on .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="35a5b-135">.NET 5 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="35a5b-135">.NET 5 or later</span></span>
> * <span data-ttu-id="35a5b-136">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="35a5b-136">.NET Core 3</span></span>

<span data-ttu-id="35a5b-137">gRPC sur .NET ne prend pas en charge l’exécution sur .NET Framework et Xamarin.</span><span class="sxs-lookup"><span data-stu-id="35a5b-137">gRPC on .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="35a5b-138">[GRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/) est une bibliothèque tierce qui prend en charge .NET Framework et Xamarin.</span><span class="sxs-lookup"><span data-stu-id="35a5b-138">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="35a5b-139">gRPC C-Core n’est pas pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="35a5b-139">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="35a5b-140">Services Azure</span><span class="sxs-lookup"><span data-stu-id="35a5b-140">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="35a5b-141">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="35a5b-141">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="35a5b-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="35a5b-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="35a5b-143">&dagger;Azure App Service ne prend pas en charge l’hébergement de gRPC sur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="35a5b-143">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="35a5b-144">gRPC-Web est une alternative compatible.</span><span class="sxs-lookup"><span data-stu-id="35a5b-144">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="35a5b-145">Le travail est en cours pour améliorer la prise en charge de gRPC avec HTTP/2 dans Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="35a5b-145">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="35a5b-146">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/9020).</span><span class="sxs-lookup"><span data-stu-id="35a5b-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="35a5b-147">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="35a5b-147">Additional resources</span></span>

* [<span data-ttu-id="35a5b-148">gRPC C# Core-bibliothèque</span><span class="sxs-lookup"><span data-stu-id="35a5b-148">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
