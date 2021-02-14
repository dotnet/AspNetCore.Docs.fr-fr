---
title: BlazorPlateformes prises en charge ASP.net Core
author: guardrex
description: En savoir plus sur les plateformes prises en charge pour ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 948c3e3f66da4727731b37491ae5c5470cfb36fe
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280716"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="e5199-103">BlazorPlateformes prises en charge ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="e5199-103">ASP.NET Core Blazor supported platforms</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e5199-104">Blazor WebAssembly et Blazor Server sont pris en charge dans les navigateurs présentés dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="e5199-104">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="e5199-105">Navigateur</span><span class="sxs-lookup"><span data-stu-id="e5199-105">Browser</span></span>                          | <span data-ttu-id="e5199-106">Version</span><span class="sxs-lookup"><span data-stu-id="e5199-106">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="e5199-107">Apple Safari, y compris iOS</span><span class="sxs-lookup"><span data-stu-id="e5199-107">Apple Safari, including iOS</span></span>      | <span data-ttu-id="e5199-108">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-108">Current&dagger;</span></span> |
| <span data-ttu-id="e5199-109">Google Chrome, y compris Android</span><span class="sxs-lookup"><span data-stu-id="e5199-109">Google Chrome, including Android</span></span> | <span data-ttu-id="e5199-110">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-110">Current&dagger;</span></span> |
| <span data-ttu-id="e5199-111">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="e5199-111">Microsoft Edge</span></span>                   | <span data-ttu-id="e5199-112">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-112">Current&dagger;</span></span> |
| <span data-ttu-id="e5199-113">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e5199-113">Mozilla Firefox</span></span>                  | <span data-ttu-id="e5199-114">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-114">Current&dagger;</span></span> |  

<span data-ttu-id="e5199-115">&dagger;*Current* fait référence à la dernière version du navigateur.</span><span class="sxs-lookup"><span data-stu-id="e5199-115">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="e5199-116">Navigateur</span><span class="sxs-lookup"><span data-stu-id="e5199-116">Browser</span></span>                          | <span data-ttu-id="e5199-117">Version</span><span class="sxs-lookup"><span data-stu-id="e5199-117">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="e5199-118">Apple Safari, y compris iOS</span><span class="sxs-lookup"><span data-stu-id="e5199-118">Apple Safari, including iOS</span></span>      | <span data-ttu-id="e5199-119">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-119">Current&dagger;</span></span>       |
| <span data-ttu-id="e5199-120">Google Chrome, y compris Android</span><span class="sxs-lookup"><span data-stu-id="e5199-120">Google Chrome, including Android</span></span> | <span data-ttu-id="e5199-121">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-121">Current&dagger;</span></span>       |
| <span data-ttu-id="e5199-122">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="e5199-122">Microsoft Edge</span></span>                   | <span data-ttu-id="e5199-123">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-123">Current&dagger;</span></span>       |
| <span data-ttu-id="e5199-124">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="e5199-124">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="e5199-125">Non pris en charge&Dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-125">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="e5199-126">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e5199-126">Mozilla Firefox</span></span>                  | <span data-ttu-id="e5199-127">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-127">Current&dagger;</span></span>       |  

<span data-ttu-id="e5199-128">&dagger;*Current* fait référence à la dernière version du navigateur.</span><span class="sxs-lookup"><span data-stu-id="e5199-128">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="e5199-129">&Dagger;Microsoft Internet Explorer ne prend pas en charge [Webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="e5199-129">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="e5199-130">Navigateur</span><span class="sxs-lookup"><span data-stu-id="e5199-130">Browser</span></span>                          | <span data-ttu-id="e5199-131">Version</span><span class="sxs-lookup"><span data-stu-id="e5199-131">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="e5199-132">Apple Safari, y compris iOS</span><span class="sxs-lookup"><span data-stu-id="e5199-132">Apple Safari, including iOS</span></span>      | <span data-ttu-id="e5199-133">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-133">Current&dagger;</span></span> |
| <span data-ttu-id="e5199-134">Google Chrome, y compris Android</span><span class="sxs-lookup"><span data-stu-id="e5199-134">Google Chrome, including Android</span></span> | <span data-ttu-id="e5199-135">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-135">Current&dagger;</span></span> |
| <span data-ttu-id="e5199-136">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="e5199-136">Microsoft Edge</span></span>                   | <span data-ttu-id="e5199-137">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-137">Current&dagger;</span></span> |
| <span data-ttu-id="e5199-138">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="e5199-138">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="e5199-139">11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-139">11&Dagger;</span></span>      |
| <span data-ttu-id="e5199-140">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e5199-140">Mozilla Firefox</span></span>                  | <span data-ttu-id="e5199-141">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5199-141">Current&dagger;</span></span> |

<span data-ttu-id="e5199-142">&dagger;*Current* fait référence à la dernière version du navigateur.</span><span class="sxs-lookup"><span data-stu-id="e5199-142">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="e5199-143">&Dagger;Des polyremplissages supplémentaires sont nécessaires.</span><span class="sxs-lookup"><span data-stu-id="e5199-143">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="e5199-144">Par exemple, les promesses peuvent être ajoutées via un [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span><span class="sxs-lookup"><span data-stu-id="e5199-144">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e5199-145">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e5199-145">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
