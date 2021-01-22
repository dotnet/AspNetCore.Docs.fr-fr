---
title: SignalRPlateformes prises en charge ASP.net Core
author: bradygaster
description: En savoir plus sur les plateformes prises en charge pour ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/21/2021
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
uid: signalr/supported-platforms
ms.openlocfilehash: 0a858de44f4a87b182a43a776154b782c7e96288
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689225"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="07346-103">SignalRPlateformes prises en charge ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="07346-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="07346-104">Configuration requise pour le serveur</span><span class="sxs-lookup"><span data-stu-id="07346-104">Server system requirements</span></span>

<span data-ttu-id="07346-105">SignalR pour ASP.NET Core prend en charge toutes les plateformes de serveur prises en charge par ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07346-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="07346-106">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="07346-106">JavaScript client</span></span>

<span data-ttu-id="07346-107">Le [client JavaScript](xref:signalr/javascript-client) s’exécute sur NodeJS 8 et versions ultérieures, ainsi que sur les navigateurs suivants :</span><span class="sxs-lookup"><span data-stu-id="07346-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="07346-108">Browser</span><span class="sxs-lookup"><span data-stu-id="07346-108">Browser</span></span>                          | <span data-ttu-id="07346-109">Version</span><span class="sxs-lookup"><span data-stu-id="07346-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="07346-110">Apple Safari, y compris iOS</span><span class="sxs-lookup"><span data-stu-id="07346-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="07346-111">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="07346-111">Current&dagger;</span></span> |
| <span data-ttu-id="07346-112">Google Chrome, y compris Android</span><span class="sxs-lookup"><span data-stu-id="07346-112">Google Chrome, including Android</span></span> | <span data-ttu-id="07346-113">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="07346-113">Current&dagger;</span></span> |
| <span data-ttu-id="07346-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="07346-114">Microsoft Edge</span></span>                   | <span data-ttu-id="07346-115">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="07346-115">Current&dagger;</span></span> |
| <span data-ttu-id="07346-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="07346-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="07346-117">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="07346-117">Current&dagger;</span></span> |

<span data-ttu-id="07346-118">&dagger;*Current* fait référence à la dernière version du navigateur.</span><span class="sxs-lookup"><span data-stu-id="07346-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

<span data-ttu-id="07346-119">Le client JavaScript ne prend pas en charge Internet Explorer et d’autres navigateurs plus anciens.</span><span class="sxs-lookup"><span data-stu-id="07346-119">The JavaScript client doesn't support Internet Explorer and other older browsers.</span></span> <span data-ttu-id="07346-120">Le client peut avoir un comportement inattendu et des erreurs sur les navigateurs non pris en charge.</span><span class="sxs-lookup"><span data-stu-id="07346-120">The client might have unexpected behavior and errors on unsupported browsers.</span></span>

## <a name="net-client"></a><span data-ttu-id="07346-121">Client .NET</span><span class="sxs-lookup"><span data-stu-id="07346-121">.NET client</span></span>

<span data-ttu-id="07346-122">Le [client .net](xref:signalr/dotnet-client) s’exécute sur n’importe quelle plateforme prise en charge par ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="07346-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="07346-123">Par exemple, [les développeurs Xamarin peuvent SignalR utiliser](https://github.com/aspnet/Announcements/issues/305) pour créer des applications Android à l’aide de Xamarin. Android 8.4.0.1 et versions ultérieures et des applications iOS à l’aide de Xamarin. iOS 11.14.0.4 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="07346-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="07346-124">Si le serveur exécute IIS, le transport WebSockets requiert IIS 8,0 ou une version ultérieure sur Windows Server 2012 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="07346-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="07346-125">D’autres transports sont pris en charge sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="07346-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="07346-126">Client Java</span><span class="sxs-lookup"><span data-stu-id="07346-126">Java client</span></span>

<span data-ttu-id="07346-127">Le [client Java](xref:signalr/java-client) prend en charge Java 8 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="07346-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="07346-128">Clients non pris en charge</span><span class="sxs-lookup"><span data-stu-id="07346-128">Unsupported clients</span></span>

<span data-ttu-id="07346-129">Les clients suivants sont disponibles, mais sont expérimentaux ou non officiels.</span><span class="sxs-lookup"><span data-stu-id="07346-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="07346-130">Ils ne sont actuellement pas pris en charge et peuvent ne jamais être.</span><span class="sxs-lookup"><span data-stu-id="07346-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="07346-131">[Client C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="07346-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="07346-132">[Client SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="07346-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
