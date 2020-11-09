---
title: SignalRRésolution des problèmes de connexion ASP.net Core
author: bradygaster
description: SignalRRésolution des problèmes de connexion ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059609"
---
# <a name="troubleshoot-connection-errors"></a><span data-ttu-id="ce012-103">Résoudre les erreurs de connexion</span><span class="sxs-lookup"><span data-stu-id="ce012-103">Troubleshoot connection errors</span></span>

<span data-ttu-id="ce012-104">Cette section fournit de l’aide sur les erreurs qui peuvent se produire lors de la tentative d’établissement d’une connexion à un SignalR hub ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="ce012-104">This section provides help with errors that can occur when trying to establish a connection to a ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="ce012-105">Code de réponse 404</span><span class="sxs-lookup"><span data-stu-id="ce012-105">Response code 404</span></span>

<span data-ttu-id="ce012-106">Lors de l’utilisation de WebSockets et `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="ce012-106">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* <span data-ttu-id="ce012-107">Lorsque vous utilisez plusieurs serveurs sans sessions rémanentes, la connexion peut démarrer sur un serveur, puis basculer vers un autre serveur.</span><span class="sxs-lookup"><span data-stu-id="ce012-107">When using multiple servers without sticky sessions, the connection can start on one server and then switch to another server.</span></span> <span data-ttu-id="ce012-108">L’autre serveur n’est pas informé de la connexion précédente.</span><span class="sxs-lookup"><span data-stu-id="ce012-108">The other server is not aware of the previous connection.</span></span>
* <span data-ttu-id="ce012-109">Vérifiez que le client se connecte au point de terminaison correct.</span><span class="sxs-lookup"><span data-stu-id="ce012-109">Verify the client is connecting to the correct endpoint.</span></span> <span data-ttu-id="ce012-110">Par exemple, le serveur est hébergé sur `http://127.0.0.1:5000/hub/myHub` et le client tente de se connecter à `http://127.0.0.1:5000/myHub` .</span><span class="sxs-lookup"><span data-stu-id="ce012-110">For example, the server is hosted at `http://127.0.0.1:5000/hub/myHub` and client is trying to connect to `http://127.0.0.1:5000/myHub`.</span></span>
* <span data-ttu-id="ce012-111">Si la connexion utilise l’ID et met trop de temps à envoyer une requête au serveur après Negotiate, le serveur :</span><span class="sxs-lookup"><span data-stu-id="ce012-111">If the connection uses the ID and takes too long to send a request to the server after the negotiate, the server:</span></span>

  * <span data-ttu-id="ce012-112">Supprime l’ID.</span><span class="sxs-lookup"><span data-stu-id="ce012-112">Deletes the ID.</span></span>
  * <span data-ttu-id="ce012-113">Retourne un 404.</span><span class="sxs-lookup"><span data-stu-id="ce012-113">Returns a 404.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="ce012-114">Code de réponse 400 ou 503</span><span class="sxs-lookup"><span data-stu-id="ce012-114">Response code 400 or 503</span></span>

<span data-ttu-id="ce012-115">Pour l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="ce012-115">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="ce012-116">Cette erreur est généralement causée par un client qui utilise uniquement le transport WebSocket, mais le protocole WebSocket n’est pas activé sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="ce012-116">This error is usually caused by a client using only the WebSockets transport but the WebSockets protocol is not enabled on the server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="ce012-117">Code de réponse 307</span><span class="sxs-lookup"><span data-stu-id="ce012-117">Response code 307</span></span>

<span data-ttu-id="ce012-118">Lors de l’utilisation de WebSockets et `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="ce012-118">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="ce012-119">Cette erreur peut également se produire pendant la demande de négociation.</span><span class="sxs-lookup"><span data-stu-id="ce012-119">This error can also happen during the negotiate request.</span></span>

<span data-ttu-id="ce012-120">Cause courante :</span><span class="sxs-lookup"><span data-stu-id="ce012-120">Common cause:</span></span>
* <span data-ttu-id="ce012-121">L’application est configurée pour appliquer le protocole HTTPs en appelant `UseHttpsRedirection` `Startup` , ou applique le protocole HTTPS via une règle de réécriture d’URL.</span><span class="sxs-lookup"><span data-stu-id="ce012-121">App is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="ce012-122">Solution possible :</span><span class="sxs-lookup"><span data-stu-id="ce012-122">Possible solution:</span></span>
* <span data-ttu-id="ce012-123">Remplacez l’URL côté client « http » par « https ».</span><span class="sxs-lookup"><span data-stu-id="ce012-123">Change the URL on the client side from "http" to "https".</span></span> `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a><span data-ttu-id="ce012-124">Code de réponse 405</span><span class="sxs-lookup"><span data-stu-id="ce012-124">Response code 405</span></span>

<span data-ttu-id="ce012-125">Code d’état HTTP 405-méthode non autorisée</span><span class="sxs-lookup"><span data-stu-id="ce012-125">Http status code 405 - Method Not Allowed</span></span>

* <span data-ttu-id="ce012-126">[Cors](xref:signalr/security#cross-origin-resource-sharing) n’est pas activé pour l’application</span><span class="sxs-lookup"><span data-stu-id="ce012-126">The app doesn't have [CORS](xref:signalr/security#cross-origin-resource-sharing) enabled</span></span>

### <a name="response-code-0"></a><span data-ttu-id="ce012-127">Code de réponse 0</span><span class="sxs-lookup"><span data-stu-id="ce012-127">Response code 0</span></span>

<span data-ttu-id="ce012-128">Code d’État http 0-généralement un problème [cors](xref:signalr/security#cross-origin-resource-sharing) , aucun code d’État n’est fourni</span><span class="sxs-lookup"><span data-stu-id="ce012-128">Http status code 0 - Usually a [CORS](xref:signalr/security#cross-origin-resource-sharing) issue, no status code is given</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* <span data-ttu-id="ce012-129">Ajouter les origines attendues à `.WithOrigins(...)`</span><span class="sxs-lookup"><span data-stu-id="ce012-129">Add the expected origins to `.WithOrigins(...)`</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* <span data-ttu-id="ce012-130">Ajoutez `.AllowCredentials()` à votre stratégie cors.</span><span class="sxs-lookup"><span data-stu-id="ce012-130">Add `.AllowCredentials()` to your CORS policy.</span></span> <span data-ttu-id="ce012-131">Impossible `.AllowAnyOrigin()` d’utiliser ou `.WithOrigins("*")` avec cette option</span><span class="sxs-lookup"><span data-stu-id="ce012-131">Cannot use `.AllowAnyOrigin()` or `.WithOrigins("*")` with this option</span></span>

### <a name="response-code-413"></a><span data-ttu-id="ce012-132">Code de réponse 413</span><span class="sxs-lookup"><span data-stu-id="ce012-132">Response code 413</span></span>

<span data-ttu-id="ce012-133">Code d’État http 413-charge utile trop grande</span><span class="sxs-lookup"><span data-stu-id="ce012-133">Http status code 413 - Payload Too Large</span></span>

<span data-ttu-id="ce012-134">Cela est souvent dû à un jeton d’accès qui est supérieur à 4 Ko.</span><span class="sxs-lookup"><span data-stu-id="ce012-134">This is often caused by having an access token that is over 4k.</span></span>

* <span data-ttu-id="ce012-135">Si vous utilisez le SignalR service Azure, réduisez la taille des jetons en personnalisant les revendications envoyées par le biais du service avec :</span><span class="sxs-lookup"><span data-stu-id="ce012-135">If using the Azure SignalR Service, reduce the token size by customizing the claims being sent through the Service with:</span></span>
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a><span data-ttu-id="ce012-136">Échecs réseau temporaires</span><span class="sxs-lookup"><span data-stu-id="ce012-136">Transient network failures</span></span>

<span data-ttu-id="ce012-137">Les défaillances réseau temporaires peuvent fermer la SignalR connexion.</span><span class="sxs-lookup"><span data-stu-id="ce012-137">Transient network failures may close the SignalR connection.</span></span> <span data-ttu-id="ce012-138">Le serveur peut interpréter la connexion fermée comme une déconnexion normale du client.</span><span class="sxs-lookup"><span data-stu-id="ce012-138">The server may interpret the closed connection as a graceful client disconnect.</span></span> <span data-ttu-id="ce012-139">Pour obtenir plus d’informations sur la raison pour laquelle un client déconnecté dans ces cas [rassemble des journaux à partir du client et du serveur](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="ce012-139">To get more info on why a client disconnected in those cases [gather logs from the client and server](xref:signalr/diagnostics).</span></span>