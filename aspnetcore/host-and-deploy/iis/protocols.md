---
title: Utiliser ASP.NET Core avec HTTP/2 sur IIS
author: rick-anderson
description: Découvrez comment utiliser les fonctionnalités HTTP/2 avec IIS.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057412"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a><span data-ttu-id="50002-103">Utiliser ASP.NET Core avec HTTP/2 sur IIS</span><span class="sxs-lookup"><span data-stu-id="50002-103">Use ASP.NET Core with HTTP/2 on IIS</span></span>

<span data-ttu-id="50002-104">Par [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="50002-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="50002-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) est pris en charge avec ASP.NET Core dans les scénarios de déploiement IIS suivants :</span><span class="sxs-lookup"><span data-stu-id="50002-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="50002-106">Windows Server 2016 ou version ultérieure/Windows 10 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="50002-106">Windows Server 2016 or later / Windows 10 or later</span></span>
* <span data-ttu-id="50002-107">IIS 10 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="50002-107">IIS 10 or later</span></span>
* <span data-ttu-id="50002-108">TLS 1.2 ou connexion ultérieure</span><span class="sxs-lookup"><span data-stu-id="50002-108">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="50002-109">Lors [de l’hébergement out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): les connexions au serveur Edge public utilisent http/2, mais la connexion du proxy inverse au [serveur Kestrel](xref:fundamentals/servers/kestrel) utilise http/1.1.</span><span class="sxs-lookup"><span data-stu-id="50002-109">When [hosting out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>

<span data-ttu-id="50002-110">Pour un déploiement in-process lors de l’établissement d’une connexion HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) signale `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="50002-110">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="50002-111">Pour un déploiement hors processus lors de l’établissement d’une connexion HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) signale `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="50002-111">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="50002-112">Pour plus d’informations sur les modèles d’hébergement in-process et out-of-process, consultez <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="50002-112">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="50002-113">HTTP/2 est activé par défaut pour les connexions HTTPs/TLS.</span><span class="sxs-lookup"><span data-stu-id="50002-113">HTTP/2 is enabled by default for HTTPS/TLS connections.</span></span> <span data-ttu-id="50002-114">Les connexions reviennent à HTTP/1.1 si une connexion HTTP/2 n’est pas établie.</span><span class="sxs-lookup"><span data-stu-id="50002-114">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="50002-115">Pour plus d’informations sur la configuration de HTTP/2 avec les déploiements IIS, consultez [HTTP/2 sur IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="50002-115">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="50002-116">Fonctionnalités HTTP/2 avancées pour la prise en charge de gRPC</span><span class="sxs-lookup"><span data-stu-id="50002-116">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="50002-117">Des fonctionnalités HTTP/2 supplémentaires dans IIS prennent en charge gRPC, y compris la prise en charge des codes de fin de réponse et l’envoi d’images de réinitialisation</span><span class="sxs-lookup"><span data-stu-id="50002-117">Additional HTTP/2 features in IIS support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="50002-118">Configuration requise pour exécuter gRPC sur IIS :</span><span class="sxs-lookup"><span data-stu-id="50002-118">Requirements to run gRPC on IIS:</span></span>

* <span data-ttu-id="50002-119">Hébergement in-process.</span><span class="sxs-lookup"><span data-stu-id="50002-119">In-process hosting.</span></span>
* <span data-ttu-id="50002-120">Windows 10, version 20300,1000 ou ultérieure du système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="50002-120">Windows 10, OS Build 20300.1000 or later.</span></span> <span data-ttu-id="50002-121">Peut nécessiter l’utilisation de builds Windows Insider.</span><span class="sxs-lookup"><span data-stu-id="50002-121">May require use of Windows Insider Builds.</span></span>
* <span data-ttu-id="50002-122">TLS 1.2 ou connexion ultérieure</span><span class="sxs-lookup"><span data-stu-id="50002-122">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="50002-123">Bandes-annonce</span><span class="sxs-lookup"><span data-stu-id="50002-123">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="50002-124">Réinitialiser</span><span class="sxs-lookup"><span data-stu-id="50002-124">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
