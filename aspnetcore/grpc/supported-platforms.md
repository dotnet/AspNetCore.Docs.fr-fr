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
ms.openlocfilehash: 88d371f460839261b618a32564a723c257b0b119
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217490"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="b7c05-103">gRPC sur les plateformes prises en charge par .NET</span><span class="sxs-lookup"><span data-stu-id="b7c05-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="b7c05-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="b7c05-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="b7c05-105">Cet article décrit la configuration requise et les plateformes prises en charge pour l’utilisation de gRPC avec .NET.</span><span class="sxs-lookup"><span data-stu-id="b7c05-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="b7c05-106">gRPC tire parti des fonctionnalités avancées disponibles dans HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7c05-106">gRPC takes advantage of advanced features available in  HTTP/2.</span></span> <span data-ttu-id="b7c05-107">HTTP/2 n’est pas pris en charge dans tous les pays, mais un deuxième format filaire à l’aide de HTTP/1.1 est disponible pour gRPC :</span><span class="sxs-lookup"><span data-stu-id="b7c05-107">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="b7c05-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC sur HTTP/2 indique comment gRPC est généralement utilisé.</span><span class="sxs-lookup"><span data-stu-id="b7c05-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="b7c05-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modifie le protocole gRPC pour qu’il soit compatible avec HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="b7c05-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="b7c05-110">gRPC-Web peut être utilisé à d’autres endroits, notamment s’il peut être appelé par des applications de navigateur.</span><span class="sxs-lookup"><span data-stu-id="b7c05-110">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="b7c05-111">Deux fonctionnalités gRPC avancées ne sont plus prises en charge : la diffusion en continu du client et la diffusion bidirectionnelle.</span><span class="sxs-lookup"><span data-stu-id="b7c05-111">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="b7c05-112">gRPC sur .NET prend en charge les formats câblés.</span><span class="sxs-lookup"><span data-stu-id="b7c05-112">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="b7c05-113">gRPC sur HTTP/2 est utilisé par défaut.</span><span class="sxs-lookup"><span data-stu-id="b7c05-113">gRPC over HTTP/2 is used by default.</span></span> <span data-ttu-id="b7c05-114">Pour plus d’informations sur la configuration de gRPC-Web, consultez <xref:grpc/browser> .</span><span class="sxs-lookup"><span data-stu-id="b7c05-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="b7c05-115">Exigences relatives aux appareils</span><span class="sxs-lookup"><span data-stu-id="b7c05-115">Device requirements</span></span>

<span data-ttu-id="b7c05-116">gRPC sur .NET prend en charge tous les appareils que .NET Core prend en charge.</span><span class="sxs-lookup"><span data-stu-id="b7c05-116">gRPC on .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="b7c05-117">Windows</span><span class="sxs-lookup"><span data-stu-id="b7c05-117">Windows</span></span>
> * <span data-ttu-id="b7c05-118">Linux</span><span class="sxs-lookup"><span data-stu-id="b7c05-118">Linux</span></span>
> * <span data-ttu-id="b7c05-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="b7c05-119">macOS&dagger;</span></span>
> * <span data-ttu-id="b7c05-120">Navigateurs&Dagger;</span><span class="sxs-lookup"><span data-stu-id="b7c05-120">Browsers&Dagger;</span></span>

<span data-ttu-id="b7c05-121">&dagger;[MacOS ne prend pas en charge l’hébergement d’applications ASP.net core avec HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="b7c05-121">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span> <span data-ttu-id="b7c05-122">les clients gRPC sur macOS peuvent appeler des services distants qui utilisent le protocole HTTPs.</span><span class="sxs-lookup"><span data-stu-id="b7c05-122">gRPC clients on macOS can call remote services that use HTTPS.</span></span>

<span data-ttu-id="b7c05-123">&Dagger;Blazor WebAssembly les applications peuvent appeler gRPC services avec gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="b7c05-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="b7c05-124">Configuration requise pour ASP.NET Core Server</span><span class="sxs-lookup"><span data-stu-id="b7c05-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="b7c05-125">les services gRPC peuvent être hébergés sur tous les serveurs ASP.NET Core intégrés.</span><span class="sxs-lookup"><span data-stu-id="b7c05-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="b7c05-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="b7c05-126">Kestrel</span></span>
> * <span data-ttu-id="b7c05-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="b7c05-127">TestServer</span></span>
> * <span data-ttu-id="b7c05-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="b7c05-128">IIS&dagger;</span></span>
> * <span data-ttu-id="b7c05-129">HTTP.sys&dagger;</span><span class="sxs-lookup"><span data-stu-id="b7c05-129">HTTP.sys&dagger;</span></span>

<span data-ttu-id="b7c05-130">&dagger;IIS et HTTP.sys requièrent .NET 5 et Windows 10 Build 20241 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="b7c05-130">&dagger;IIS and HTTP.sys require .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="b7c05-131">Pour plus d’informations, consultez <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="b7c05-131">For more information, see <xref:grpc/aspnetcore>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="b7c05-132">Configuration requise pour la version .NET</span><span class="sxs-lookup"><span data-stu-id="b7c05-132">.NET version requirements</span></span>

<span data-ttu-id="b7c05-133">gRPC sur .NET prend en charge .NET Core 3 et .NET 5 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="b7c05-133">gRPC on .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="b7c05-134">.NET 5 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="b7c05-134">.NET 5 or later</span></span>
> * <span data-ttu-id="b7c05-135">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="b7c05-135">.NET Core 3</span></span>

<span data-ttu-id="b7c05-136">gRPC sur .NET ne prend pas en charge l’exécution sur .NET Framework et Xamarin.</span><span class="sxs-lookup"><span data-stu-id="b7c05-136">gRPC on .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="b7c05-137">[GRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/) est une bibliothèque tierce qui prend en charge .NET Framework et Xamarin.</span><span class="sxs-lookup"><span data-stu-id="b7c05-137">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="b7c05-138">gRPC C-Core n’est pas pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="b7c05-138">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="b7c05-139">Services Azure</span><span class="sxs-lookup"><span data-stu-id="b7c05-139">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="b7c05-140">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="b7c05-140">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="b7c05-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="b7c05-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="b7c05-142">&dagger;Azure App Service ne prend pas en charge l’hébergement de gRPC sur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7c05-142">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="b7c05-143">gRPC-Web est une alternative compatible.</span><span class="sxs-lookup"><span data-stu-id="b7c05-143">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="b7c05-144">Le travail est en cours pour améliorer la prise en charge de gRPC avec HTTP/2 dans Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b7c05-144">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="b7c05-145">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/9020).</span><span class="sxs-lookup"><span data-stu-id="b7c05-145">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7c05-146">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b7c05-146">Additional resources</span></span>

* [<span data-ttu-id="b7c05-147">gRPC C# Core-bibliothèque</span><span class="sxs-lookup"><span data-stu-id="b7c05-147">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
