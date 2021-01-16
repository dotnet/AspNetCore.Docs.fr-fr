---
title: Utiliser HTTP/2 avec le serveur Web ASP.NET Core Kestrel
author: rick-anderson
description: En savoir plus sur l’utilisation de HTTP/2 avec Kestrel, le serveur Web multiplateforme pour ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253955"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="0119e-103">Utiliser HTTP/2 avec le serveur Web ASP.NET Core Kestrel</span><span class="sxs-lookup"><span data-stu-id="0119e-103">Use HTTP/2 with the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="0119e-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) est disponible pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="0119e-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="0119e-105">Système d’exploitation&dagger;</span><span class="sxs-lookup"><span data-stu-id="0119e-105">Operating system&dagger;</span></span>
  * <span data-ttu-id="0119e-106">Windows Server 2016/Windows 10 ou version ultérieure&Dagger;</span><span class="sxs-lookup"><span data-stu-id="0119e-106">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="0119e-107">Linux avec OpenSSL 1.0.2 ou version ultérieure (par exemple, Ubuntu 16.04 ou version ultérieure)</span><span class="sxs-lookup"><span data-stu-id="0119e-107">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="0119e-108">Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="0119e-108">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="0119e-109">Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="0119e-109">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="0119e-110">TLS 1.2 ou connexion ultérieure</span><span class="sxs-lookup"><span data-stu-id="0119e-110">TLS 1.2 or later connection</span></span>

<span data-ttu-id="0119e-111">&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="0119e-111">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="0119e-112">&Dagger;Kestrel propose une prise en charge limitée de HTTP/2 sous Windows Server 2012 R2 et Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="0119e-112">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="0119e-113">La prise en charge est limitée car la liste des suites de chiffrement TLS prises en charge sur ces systèmes d’exploitation est limitée.</span><span class="sxs-lookup"><span data-stu-id="0119e-113">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="0119e-114">Un certificat généré à l’aide d’Elliptic Curve Digital Signature algorithme (ECDSA) peut être requis pour sécuriser les connexions TLS.</span><span class="sxs-lookup"><span data-stu-id="0119e-114">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="0119e-115">Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) retourne `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="0119e-115">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) reports `HTTP/2`.</span></span>

<span data-ttu-id="0119e-116">À compter de .NET Core 3,0, HTTP/2 est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="0119e-116">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="0119e-117">Pour plus d’informations sur la configuration, consultez les sections [limites KESTREL http/2](xref:fundamentals/servers/kestrel/options#http2-limits) et [ListenOptions. Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="0119e-117">For more information on configuration, see the [Kestrel HTTP/2 limits](xref:fundamentals/servers/kestrel/options#http2-limits) and [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) sections.</span></span>

## <a name="advanced-http2-features"></a><span data-ttu-id="0119e-118">Fonctionnalités HTTP/2 avancées</span><span class="sxs-lookup"><span data-stu-id="0119e-118">Advanced HTTP/2 features</span></span>

<span data-ttu-id="0119e-119">Des fonctionnalités HTTP/2 supplémentaires dans Kestrel prennent en charge gRPC, notamment la prise en charge des codes de fin de réponse et l’envoi d’images de réinitialisation.</span><span class="sxs-lookup"><span data-stu-id="0119e-119">Additional HTTP/2 features in Kestrel support gRPC, including support for response trailers and sending reset frames.</span></span>

### <a name="trailers"></a><span data-ttu-id="0119e-120">Bandes-annonce</span><span class="sxs-lookup"><span data-stu-id="0119e-120">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="0119e-121">Réinitialiser</span><span class="sxs-lookup"><span data-stu-id="0119e-121">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
