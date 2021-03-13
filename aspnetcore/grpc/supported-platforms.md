---
title: gRPC sur les plateformes prises en charge par .NET
author: jamesnk
description: En savoir plus sur les plateformes prises en charge pour gRPC sur .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 3/11/2021
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
ms.openlocfilehash: c2bd808d16f11077e39aada829d79e8aedf2755b
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413416"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="9fbe0-103">gRPC sur les plateformes prises en charge par .NET</span><span class="sxs-lookup"><span data-stu-id="9fbe0-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="9fbe0-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="9fbe0-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="9fbe0-105">Cet article décrit la configuration requise et les plateformes prises en charge pour l’utilisation de gRPC avec .NET.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span> <span data-ttu-id="9fbe0-106">Il existe différentes exigences pour les deux charges de travail gRPC majeures :</span><span class="sxs-lookup"><span data-stu-id="9fbe0-106">There are different requirements for the two major gRPC workloads:</span></span>

* [<span data-ttu-id="9fbe0-107">Hébergement des services gRPC dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9fbe0-107">Hosting gRPC services in ASP.NET Core</span></span>](#aspnet-core-grpc-server-requirements)
* [<span data-ttu-id="9fbe0-108">Appel de gRPC à partir d’applications clientes .NET</span><span class="sxs-lookup"><span data-stu-id="9fbe0-108">Calling gRPC from .NET client apps</span></span>](#net-grpc-client-requirements)

## <a name="wire-formats"></a><span data-ttu-id="9fbe0-109">Formats filaires</span><span class="sxs-lookup"><span data-stu-id="9fbe0-109">Wire-formats</span></span>

<span data-ttu-id="9fbe0-110">gRPC tire parti des fonctionnalités avancées disponibles dans HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-110">gRPC takes advantage of advanced features available in HTTP/2.</span></span> <span data-ttu-id="9fbe0-111">HTTP/2 n’est pas pris en charge dans tous les pays, mais un deuxième format filaire à l’aide de HTTP/1.1 est disponible pour gRPC :</span><span class="sxs-lookup"><span data-stu-id="9fbe0-111">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="9fbe0-112">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC sur HTTP/2 indique comment gRPC est généralement utilisé.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-112">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="9fbe0-113">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modifie le protocole gRPC pour qu’il soit compatible avec HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-113">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="9fbe0-114">gRPC-Web peut être utilisé à d’autres endroits.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-114">gRPC-Web can be used in more places.</span></span> <span data-ttu-id="9fbe0-115">gRPC-Web peut être utilisé par les applications de navigateur et les réseaux sans prise en charge complète de HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-115">gRPC-Web can be used by browser apps and in networks without complete support for HTTP/2.</span></span> <span data-ttu-id="9fbe0-116">Deux fonctionnalités gRPC avancées ne sont plus prises en charge : la diffusion en continu du client et la diffusion bidirectionnelle.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-116">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="9fbe0-117">gRPC sur .NET prend en charge les formats câblés.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-117">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="9fbe0-118">`application/grpc` est utilisé par défaut.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-118">`application/grpc` is used by default.</span></span> <span data-ttu-id="9fbe0-119">gRPC-Web doit être configuré sur le client et sur le serveur pour les appels gRPC-Web réussis.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-119">gRPC-Web must be configured on the client and the server for successful gRPC-Web calls.</span></span> <span data-ttu-id="9fbe0-120">Pour plus d’informations sur la configuration de gRPC-Web, consultez <xref:grpc/browser> .</span><span class="sxs-lookup"><span data-stu-id="9fbe0-120">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="aspnet-core-grpc-server-requirements"></a><span data-ttu-id="9fbe0-121">ASP.NET Core configuration requise du serveur gRPC</span><span class="sxs-lookup"><span data-stu-id="9fbe0-121">ASP.NET Core gRPC server requirements</span></span>

<span data-ttu-id="9fbe0-122">L’hébergement des services gRPC avec ASP.NET Core nécessite .NET Core 3. x ou une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-122">Hosting gRPC services with ASP.NET Core requires .NET Core 3.x or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="9fbe0-123">.NET 5 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="9fbe0-123">.NET 5 or later</span></span>
> * <span data-ttu-id="9fbe0-124">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="9fbe0-124">.NET Core 3</span></span>

<span data-ttu-id="9fbe0-125">ASP.NET Core Services gRPC peuvent être hébergés sur tous les systèmes d’exploitation pris en charge par .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-125">ASP.NET Core gRPC services can be hosted on all operating system that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="9fbe0-126">Windows</span><span class="sxs-lookup"><span data-stu-id="9fbe0-126">Windows</span></span>
> * <span data-ttu-id="9fbe0-127">Linux</span><span class="sxs-lookup"><span data-stu-id="9fbe0-127">Linux</span></span>
> * <span data-ttu-id="9fbe0-128">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="9fbe0-128">macOS&dagger;</span></span>

<span data-ttu-id="9fbe0-129">&dagger;[MacOS ne prend pas en charge l’hébergement d’applications ASP.net core avec HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="9fbe0-129">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="supported-aspnet-core-servers"></a><span data-ttu-id="9fbe0-130">Serveurs de ASP.NET Core pris en charge</span><span class="sxs-lookup"><span data-stu-id="9fbe0-130">Supported ASP.NET Core servers</span></span>

<span data-ttu-id="9fbe0-131">Tous les serveurs ASP.NET Core intégrés sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-131">All built-in ASP.NET Core servers are supported.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="9fbe0-132">Kestrel</span><span class="sxs-lookup"><span data-stu-id="9fbe0-132">Kestrel</span></span>
> * <span data-ttu-id="9fbe0-133">TestServer</span><span class="sxs-lookup"><span data-stu-id="9fbe0-133">TestServer</span></span>
> * <span data-ttu-id="9fbe0-134">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="9fbe0-134">IIS&dagger;</span></span>
> * <span data-ttu-id="9fbe0-135">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="9fbe0-135">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="9fbe0-136">&dagger;IIS requiert .NET 5 et Windows 10 Build 20241 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-136">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="9fbe0-137">&Dagger;HTTP.sys nécessite .NET 5 et Windows 10 Build 19529 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-137">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="9fbe0-138">Pour plus d’informations sur la configuration des serveurs ASP.NET Core pour exécuter gRPC, consultez <xref:grpc/aspnetcore#server-options> .</span><span class="sxs-lookup"><span data-stu-id="9fbe0-138">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

### <a name="azure-services"></a><span data-ttu-id="9fbe0-139">Services Azure</span><span class="sxs-lookup"><span data-stu-id="9fbe0-139">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="9fbe0-140">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="9fbe0-140">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="9fbe0-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="9fbe0-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="9fbe0-142">&dagger;Azure App Service ne prend pas en charge l’hébergement de gRPC sur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-142">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="9fbe0-143">gRPC-Web est une alternative compatible.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-143">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="9fbe0-144">Le travail est en cours pour améliorer la prise en charge de gRPC avec HTTP/2 dans Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-144">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="9fbe0-145">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/9020).</span><span class="sxs-lookup"><span data-stu-id="9fbe0-145">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="net-grpc-client-requirements"></a><span data-ttu-id="9fbe0-146">Configuration requise du client .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="9fbe0-146">.NET gRPC client requirements</span></span>

<span data-ttu-id="9fbe0-147">Le package [GRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client/) prend en charge les appels GRPC sur http/2 sur .net Core 3 et .net 5 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-147">The [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/) package supports gRPC calls over HTTP/2 on .NET Core 3 and .NET 5 or later.</span></span>

<span data-ttu-id="9fbe0-148">Une prise en charge limitée est disponible pour gRPC sur HTTP/2 sur .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-148">Limited support is available for gRPC over HTTP/2 on .NET Framework.</span></span> <span data-ttu-id="9fbe0-149">D’autres versions de .NET telles que UWP, Xamarin et Unity n’ont pas la prise en charge HTTP/2 requise, et doivent utiliser gRPC-Web à la place.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-149">Other .NET versions such as UWP, Xamarin and Unity don't have required HTTP/2 support, and must use gRPC-Web instead.</span></span>

<span data-ttu-id="9fbe0-150">Le tableau suivant répertorie les implémentations .NET et leur prise en charge du client gRPC :</span><span class="sxs-lookup"><span data-stu-id="9fbe0-150">The following table lists .NET implementations and their gRPC client support:</span></span>

| <span data-ttu-id="9fbe0-151">Implémentation de .NET</span><span class="sxs-lookup"><span data-stu-id="9fbe0-151">.NET implementation</span></span>                          | <span data-ttu-id="9fbe0-152">gRPC sur HTTP/2</span><span class="sxs-lookup"><span data-stu-id="9fbe0-152">gRPC over HTTP/2</span></span>   | <span data-ttu-id="9fbe0-153">gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="9fbe0-153">gRPC-Web</span></span>   |
|----------------------------------------------|--------------------|------------|
| <span data-ttu-id="9fbe0-154">.NET 5 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="9fbe0-154">.NET 5 or later</span></span>                              | <span data-ttu-id="9fbe0-155">✔️</span><span class="sxs-lookup"><span data-stu-id="9fbe0-155">✔️</span></span>                | <span data-ttu-id="9fbe0-156">✔️</span><span class="sxs-lookup"><span data-stu-id="9fbe0-156">✔️</span></span>         |
| <span data-ttu-id="9fbe0-157">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="9fbe0-157">.NET Core 3</span></span>                                  | <span data-ttu-id="9fbe0-158">✔️</span><span class="sxs-lookup"><span data-stu-id="9fbe0-158">✔️</span></span>                | <span data-ttu-id="9fbe0-159">✔️</span><span class="sxs-lookup"><span data-stu-id="9fbe0-159">✔️</span></span>         |
| <span data-ttu-id="9fbe0-160">.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="9fbe0-160">.NET Core 2.1</span></span>                                | ❌                | <span data-ttu-id="9fbe0-161">✔️</span><span class="sxs-lookup"><span data-stu-id="9fbe0-161">✔️</span></span>         |
| <span data-ttu-id="9fbe0-162">.NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="9fbe0-162">.NET Framework 4.6.1</span></span>                         | ⚠️&dagger;        | <span data-ttu-id="9fbe0-163">✔️</span><span class="sxs-lookup"><span data-stu-id="9fbe0-163">✔️</span></span>         |
| Blazor WebAssembly                           | ❌                | <span data-ttu-id="9fbe0-164">✔️</span><span class="sxs-lookup"><span data-stu-id="9fbe0-164">✔️</span></span>         |
| <span data-ttu-id="9fbe0-165">Mono 5.4</span><span class="sxs-lookup"><span data-stu-id="9fbe0-165">Mono 5.4</span></span>                                     | ❌                | <span data-ttu-id="9fbe0-166">✔️</span><span class="sxs-lookup"><span data-stu-id="9fbe0-166">✔️</span></span>         |
| <span data-ttu-id="9fbe0-167">Xamarin.iOS 10.14</span><span class="sxs-lookup"><span data-stu-id="9fbe0-167">Xamarin.iOS 10.14</span></span>                            | ❌                | <span data-ttu-id="9fbe0-168">✔️</span><span class="sxs-lookup"><span data-stu-id="9fbe0-168">✔️</span></span>         |
| <span data-ttu-id="9fbe0-169">Xamarin.Android 8.0</span><span class="sxs-lookup"><span data-stu-id="9fbe0-169">Xamarin.Android 8.0</span></span>                          | ❌                | <span data-ttu-id="9fbe0-170">✔️</span><span class="sxs-lookup"><span data-stu-id="9fbe0-170">✔️</span></span>         |
| <span data-ttu-id="9fbe0-171">Plateforme Windows universelle 10.0.16299</span><span class="sxs-lookup"><span data-stu-id="9fbe0-171">Universal Windows Platform 10.0.16299</span></span>        | ❌                | <span data-ttu-id="9fbe0-172">✔️</span><span class="sxs-lookup"><span data-stu-id="9fbe0-172">✔️</span></span>         |
| <span data-ttu-id="9fbe0-173">Unity 2018.1</span><span class="sxs-lookup"><span data-stu-id="9fbe0-173">Unity 2018.1</span></span>                                 | ❌                | <span data-ttu-id="9fbe0-174">✔️</span><span class="sxs-lookup"><span data-stu-id="9fbe0-174">✔️</span></span>         |

<span data-ttu-id="9fbe0-175">&dagger;.NET Framework nécessite <xref:System.Net.Http.WinHttpHandler> d’être configuré et Windows 10 Build 19622 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-175">&dagger;.NET Framework requires <xref:System.Net.Http.WinHttpHandler> to be configured and Windows 10 Build 19622 or later.</span></span>

<span data-ttu-id="9fbe0-176">L’utilisation `Grpc.Net.Client` de sur .NET Framework ou avec gRPC-Web requiert une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-176">Using `Grpc.Net.Client` on .NET Framework or with gRPC-Web requires additional configuration.</span></span> <span data-ttu-id="9fbe0-177">Pour plus d’informations, consultez <xref:grpc/netstandard>.</span><span class="sxs-lookup"><span data-stu-id="9fbe0-177">For more information, see <xref:grpc/netstandard>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9fbe0-178">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9fbe0-178">Additional resources</span></span>

* <xref:grpc/netstandard>
* [<span data-ttu-id="9fbe0-179">gRPC C# Core-bibliothèque</span><span class="sxs-lookup"><span data-stu-id="9fbe0-179">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
