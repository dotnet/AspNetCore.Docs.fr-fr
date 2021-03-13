---
title: Utiliser le client gRPC avec .NET Standard 2,0
author: jamesnk
description: Découvrez comment utiliser le client .NET gRPC dans les applications et les bibliothèques qui prennent en charge .NET Standard 2,0.
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
uid: grpc/netstandard
ms.openlocfilehash: a6b066979dcdcdf648b8b0326bef47fe0e466266
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103422500"
---
# <a name="use-grpc-client-with-net-standard-20"></a><span data-ttu-id="dfacd-103">Utiliser le client gRPC avec .NET Standard 2,0</span><span class="sxs-lookup"><span data-stu-id="dfacd-103">Use gRPC client with .NET Standard 2.0</span></span>

<span data-ttu-id="dfacd-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="dfacd-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="dfacd-105">Cet article explique comment utiliser le client .NET gRPC avec des implémentations .NET qui prennent en charge [.NET Standard 2,0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="dfacd-105">This article discusses how to use the .NET gRPC client with .NET implementations that support [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span>

## <a name="net-implementations"></a><span data-ttu-id="dfacd-106">Implémentations de .NET</span><span class="sxs-lookup"><span data-stu-id="dfacd-106">.NET implementations</span></span>

<span data-ttu-id="dfacd-107">Les implémentations .NET suivantes (ou ultérieures) prennent en charge [GRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client/) , mais ne prennent pas en charge le protocole http/2 :</span><span class="sxs-lookup"><span data-stu-id="dfacd-107">The following .NET implementations (or later) support [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/) but don't have full support for HTTP/2:</span></span>

* <span data-ttu-id="dfacd-108">.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="dfacd-108">.NET Core 2.1</span></span>
* <span data-ttu-id="dfacd-109">.NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="dfacd-109">.NET Framework 4.6.1</span></span>
* <span data-ttu-id="dfacd-110">Mono 5.4</span><span class="sxs-lookup"><span data-stu-id="dfacd-110">Mono 5.4</span></span>
* <span data-ttu-id="dfacd-111">Xamarin.iOS 10.14</span><span class="sxs-lookup"><span data-stu-id="dfacd-111">Xamarin.iOS 10.14</span></span>
* <span data-ttu-id="dfacd-112">Xamarin.Android 8.0</span><span class="sxs-lookup"><span data-stu-id="dfacd-112">Xamarin.Android 8.0</span></span>
* <span data-ttu-id="dfacd-113">Plateforme Windows universelle 10.0.16299</span><span class="sxs-lookup"><span data-stu-id="dfacd-113">Universal Windows Platform 10.0.16299</span></span>
* <span data-ttu-id="dfacd-114">Unity 2018.1</span><span class="sxs-lookup"><span data-stu-id="dfacd-114">Unity 2018.1</span></span>

<span data-ttu-id="dfacd-115">Le client .NET gRPC peut appeler des services à partir de ces implémentations .NET avec une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="dfacd-115">The .NET gRPC client can call services from these .NET implementations with some additional configuration.</span></span>

## <a name="httphandler-configuration"></a><span data-ttu-id="dfacd-116">Configuration de HttpHandler</span><span class="sxs-lookup"><span data-stu-id="dfacd-116">HttpHandler configuration</span></span>

<span data-ttu-id="dfacd-117">Un fournisseur HTTP doit être configuré à l’aide de `GrpcChannelOptions.HttpHandler` .</span><span class="sxs-lookup"><span data-stu-id="dfacd-117">An HTTP provider must be configured using `GrpcChannelOptions.HttpHandler`.</span></span> <span data-ttu-id="dfacd-118">Si un gestionnaire n’est pas configuré, une erreur est générée :</span><span class="sxs-lookup"><span data-stu-id="dfacd-118">If a handler isn't configured, an error is thrown:</span></span>

> <span data-ttu-id="dfacd-119">`System.PlatformNotSupportedException`: gRPC requiert une configuration supplémentaire pour effectuer correctement les appels RPC sur les implémentations .NET qui n’ont pas de prise en charge de gRPC sur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="dfacd-119">`System.PlatformNotSupportedException`: gRPC requires extra configuration to successfully make RPC calls on .NET implementations that don't have support for gRPC over HTTP/2.</span></span> <span data-ttu-id="dfacd-120">Un fournisseur HTTP doit être spécifié à l’aide de `GrpcChannelOptions.HttpHandler` .</span><span class="sxs-lookup"><span data-stu-id="dfacd-120">An HTTP provider must be specified using `GrpcChannelOptions.HttpHandler`.</span></span> <span data-ttu-id="dfacd-121">Le fournisseur HTTP configuré doit prendre en charge HTTP/2 ou être configuré pour utiliser gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="dfacd-121">The configured HTTP provider must either support HTTP/2 or be configured to use gRPC-Web.</span></span>

<span data-ttu-id="dfacd-122">Les implémentations .NET qui ne prennent pas en charge HTTP/2, telles que UWP, Xamarin et Unity, peuvent utiliser gRPC-Web comme alternative.</span><span class="sxs-lookup"><span data-stu-id="dfacd-122">.NET implementations that don't support HTTP/2, such as UWP, Xamarin, and Unity, can use gRPC-Web as an alternative.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new GrpcWebHandler(new HttpClientHandler())
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

<span data-ttu-id="dfacd-123">Pour plus d’informations, consultez [configurer gRPC-Web avec le client .net gRPC](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client).</span><span class="sxs-lookup"><span data-stu-id="dfacd-123">For more information, see [Configure gRPC-Web with the .NET gRPC client](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client).</span></span>

## <a name="net-framework"></a><span data-ttu-id="dfacd-124">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="dfacd-124">.NET Framework</span></span>

<span data-ttu-id="dfacd-125">.NET Framework a une prise en charge limitée de gRPC sur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="dfacd-125">.NET Framework has limited support for gRPC over HTTP/2.</span></span> <span data-ttu-id="dfacd-126">Pour activer gRPC sur HTTP/2 sur .NET Framework, configurez le canal à utiliser <xref:System.Net.Http.WinHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="dfacd-126">To enable gRPC over HTTP/2 on .NET Framework, configure the channel to use <xref:System.Net.Http.WinHttpHandler>.</span></span>

<span data-ttu-id="dfacd-127">Exigences et restrictions relatives à l’utilisation de `WinHttpHandler` :</span><span class="sxs-lookup"><span data-stu-id="dfacd-127">Requirements and restrictions to using `WinHttpHandler`:</span></span>

* <span data-ttu-id="dfacd-128">Windows 10 Build 19622 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="dfacd-128">Windows 10 Build 19622 or later.</span></span>
* <span data-ttu-id="dfacd-129">Référence au package NuGet [System .net. http. WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) .</span><span class="sxs-lookup"><span data-stu-id="dfacd-129">A reference to the [System.Net.Http.WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) NuGet package.</span></span>
* <span data-ttu-id="dfacd-130">Seuls les appels unaires et gRPC Server streaming sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="dfacd-130">Only unary and server streaming gRPC calls are supported.</span></span>
* <span data-ttu-id="dfacd-131">Seuls les appels gRPC sur TLS sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="dfacd-131">Only gRPC calls over TLS are supported.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new WinHttpHandler()
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

> [!NOTE]
> <span data-ttu-id="dfacd-132">La prise en charge de .NET Framework est en phase préliminaire et requiert l’utilisation d’un logiciel en version préliminaire.</span><span class="sxs-lookup"><span data-stu-id="dfacd-132">.NET Framework support is in its early stages and requires using pre-release software.</span></span>
> * <span data-ttu-id="dfacd-133">Windows 10 Build 19622 ou version ultérieure est disponible en tant que Build [Windows Insiders](https://insider.windows.com/) .</span><span class="sxs-lookup"><span data-stu-id="dfacd-133">Windows 10 Build 19622 or later is available as a [Windows Insiders](https://insider.windows.com/) build.</span></span>
> * <span data-ttu-id="dfacd-134">La version requise de `System.Net.Http.WinHttpHandler` n’est pas disponible actuellement sur NuGet.org. La dernière version préliminaire est disponible sur [ce flux NuGet](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json) doit être utilisé.</span><span class="sxs-lookup"><span data-stu-id="dfacd-134">The required version of `System.Net.Http.WinHttpHandler` is not currently available on NuGet.org. The latest pre-release version is available on [this NuGet feed](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json) should be used.</span></span>

## <a name="grpc-c-core-library"></a><span data-ttu-id="dfacd-135">gRPC C# Core-bibliothèque</span><span class="sxs-lookup"><span data-stu-id="dfacd-135">gRPC C# core-library</span></span>

<span data-ttu-id="dfacd-136">Une autre option pour .NET Framework et Xamarin consiste à utiliser [GRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/) pour effectuer des appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="dfacd-136">An alternative option for .NET Framework and Xamarin is to use [gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) to make gRPC calls.</span></span> <span data-ttu-id="dfacd-137">Il s’agit d’une bibliothèque tierce qui prend en charge l’exécution d’appels gRPC sur HTTP/2 sur .NET Framework et Xamarin.</span><span class="sxs-lookup"><span data-stu-id="dfacd-137">It's a third party library that supports making gRPC calls over HTTP/2 on .NET Framework and Xamarin.</span></span> <span data-ttu-id="dfacd-138">gRPC C-Core n’est pas pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="dfacd-138">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dfacd-139">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="dfacd-139">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/browser>
* [<span data-ttu-id="dfacd-140">gRPC C# Core-bibliothèque</span><span class="sxs-lookup"><span data-stu-id="dfacd-140">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
