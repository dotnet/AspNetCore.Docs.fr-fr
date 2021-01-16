---
title: Demander le drainage avec ASP.NET Core serveur Web Kestrel
author: rick-anderson
description: En savoir plus sur la demande de drainage avec Kestrel, le serveur Web multiplateforme pour ASP.NET Core.
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253949"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="38020-103">Demander le drainage avec ASP.NET Core serveur Web Kestrel</span><span class="sxs-lookup"><span data-stu-id="38020-103">Request draining with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="38020-104">L’ouverture de connexions HTTP prend beaucoup de temps.</span><span class="sxs-lookup"><span data-stu-id="38020-104">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="38020-105">Pour le protocole HTTPs, il s’agit également d’une utilisation intensive des ressources.</span><span class="sxs-lookup"><span data-stu-id="38020-105">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="38020-106">Par conséquent, Kestrel tente de réutiliser les connexions par protocole HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="38020-106">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="38020-107">Un corps de demande doit être entièrement consommé pour permettre la réutilisation de la connexion.</span><span class="sxs-lookup"><span data-stu-id="38020-107">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="38020-108">L’application ne consomme pas toujours le corps de la requête, par exemple les requêtes HTTP Après, où le serveur retourne une réponse de redirection ou 404.</span><span class="sxs-lookup"><span data-stu-id="38020-108">The app doesn't always consume the request body, such as HTTP POST requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="38020-109">Dans le cas de redirection HTTP, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="38020-109">In the HTTP POST redirect case:</span></span>

* <span data-ttu-id="38020-110">Le client a peut-être déjà envoyé une partie des données de publication.</span><span class="sxs-lookup"><span data-stu-id="38020-110">The client may already have sent part of the POST data.</span></span>
* <span data-ttu-id="38020-111">Le serveur écrit la réponse 301.</span><span class="sxs-lookup"><span data-stu-id="38020-111">The server writes the 301 response.</span></span>
* <span data-ttu-id="38020-112">La connexion ne peut pas être utilisée pour une nouvelle demande tant que les données de publication du corps de la requête précédente n’ont pas été entièrement lues.</span><span class="sxs-lookup"><span data-stu-id="38020-112">The connection can't be used for a new request until the POST data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="38020-113">Kestrel tente de vider le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="38020-113">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="38020-114">Le drainage du corps de la demande signifie lire et ignorer les données sans les traiter.</span><span class="sxs-lookup"><span data-stu-id="38020-114">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="38020-115">Le processus de drainage fait un compromis entre l’autorisation de réutilisation de la connexion et le temps nécessaire pour décharger les données restantes :</span><span class="sxs-lookup"><span data-stu-id="38020-115">The draining process makes a tradeoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="38020-116">La vidange a un délai d’expiration de cinq secondes, ce qui n’est pas configurable.</span><span class="sxs-lookup"><span data-stu-id="38020-116">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="38020-117">Si toutes les données spécifiées par l' `Content-Length` `Transfer-Encoding` en-tête ou n’ont pas été lues avant le délai d’attente, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="38020-117">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="38020-118">Il peut arriver que vous souhaitiez mettre fin à la demande immédiatement, avant ou après l’écriture de la réponse.</span><span class="sxs-lookup"><span data-stu-id="38020-118">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="38020-119">Par exemple, les clients peuvent avoir des limites de données restrictives.</span><span class="sxs-lookup"><span data-stu-id="38020-119">For example, clients may have restrictive data caps.</span></span> <span data-ttu-id="38020-120">La limitation des données chargées peut être une priorité.</span><span class="sxs-lookup"><span data-stu-id="38020-120">Limiting uploaded data might be a priority.</span></span> <span data-ttu-id="38020-121">Dans ce cas, pour mettre fin à une demande, appelez [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) à partir d’un contrôleur, d’une Razor page ou d’un intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="38020-121">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="38020-122">Il existe des inconvénients à l’appel de `Abort` :</span><span class="sxs-lookup"><span data-stu-id="38020-122">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="38020-123">La création de nouvelles connexions peut être lente et coûteuse.</span><span class="sxs-lookup"><span data-stu-id="38020-123">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="38020-124">Il n’y a aucune garantie que le client a lu la réponse avant la fermeture de la connexion.</span><span class="sxs-lookup"><span data-stu-id="38020-124">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="38020-125">L’appel `Abort` de doit être rare et réservé aux cas d’erreur graves, et non aux erreurs courantes.</span><span class="sxs-lookup"><span data-stu-id="38020-125">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="38020-126">Appelez uniquement `Abort` lorsqu’un problème spécifique doit être résolu.</span><span class="sxs-lookup"><span data-stu-id="38020-126">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="38020-127">Par exemple, appelez `Abort` si des clients malveillants essaient de poster des données ou lorsqu’il y a un bogue dans le code client qui provoque des demandes volumineuses ou multiples.</span><span class="sxs-lookup"><span data-stu-id="38020-127">For example, call `Abort` if malicious clients are trying to POST data or when there's a bug in client code that causes large or several requests.</span></span>
  * <span data-ttu-id="38020-128">N’appelez pas `Abort` pour les situations d’erreur courantes, telles que HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="38020-128">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="38020-129">L’appel de [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) avant d’appeler `Abort` garantit que le serveur a terminé l’écriture de la réponse.</span><span class="sxs-lookup"><span data-stu-id="38020-129">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="38020-130">Toutefois, le comportement du client n’est pas prévisible et il est possible qu’il ne lise pas la réponse avant l’abandon de la connexion.</span><span class="sxs-lookup"><span data-stu-id="38020-130">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="38020-131">Ce processus est différent pour HTTP/2, car le protocole prend en charge l’abandon des flux de demande individuels sans fermer la connexion.</span><span class="sxs-lookup"><span data-stu-id="38020-131">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="38020-132">Le délai d’attente de drainage de cinq secondes ne s’applique pas.</span><span class="sxs-lookup"><span data-stu-id="38020-132">The five-second drain timeout doesn't apply.</span></span> <span data-ttu-id="38020-133">S’il existe des données de corps de demande non lues après avoir complété une réponse, le serveur envoie une trame HTTP/2 RST.</span><span class="sxs-lookup"><span data-stu-id="38020-133">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="38020-134">Les trames de données de corps de requête supplémentaires sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="38020-134">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="38020-135">Si possible, il est préférable pour les clients d’utiliser l’en-tête de demande [expect : 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) et d’attendre que le serveur réponde avant de commencer à envoyer le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="38020-135">If possible, it's better for clients to use the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="38020-136">Cela donne au client la possibilité d’examiner la réponse et de l’abandonner avant d’envoyer des données inutiles.</span><span class="sxs-lookup"><span data-stu-id="38020-136">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>
