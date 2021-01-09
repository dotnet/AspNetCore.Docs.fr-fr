---
title: Prise en charge des WebSockets dans ASP.NET Core
author: rick-anderson
description: Découvrez comment commencer à utiliser les WebSockets dans ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
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
uid: fundamentals/websockets
ms.openlocfilehash: 6edf2017cc889321cfb484e643b75711fd66004d
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058348"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="64b7b-103">Prise en charge des WebSockets dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64b7b-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="64b7b-104">Par [Tom Dykstra](https://github.com/tdykstra) et [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="64b7b-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="64b7b-105">Cet article explique comment commencer avec les WebSockets dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="64b7b-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="64b7b-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) est un protocole qui autorise des canaux de communication persistants bidirectionnels sur les connexions TCP.</span><span class="sxs-lookup"><span data-stu-id="64b7b-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="64b7b-107">Son utilisation profite aux applications qui tirent parti d’une communication rapide et en temps réel, par exemple les applications de conversation, de tableau de bord et de jeu.</span><span class="sxs-lookup"><span data-stu-id="64b7b-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="64b7b-108">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="64b7b-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="64b7b-109">[Comment exécuter](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="64b7b-109">[How to run](#sample-app).</span></span>

## SignalR

<span data-ttu-id="64b7b-110">[ASP.net Core SignalR ](xref:signalr/introduction) est une bibliothèque qui simplifie l’ajout de fonctionnalités Web en temps réel aux applications.</span><span class="sxs-lookup"><span data-stu-id="64b7b-110">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="64b7b-111">Elle utilise des WebSockets dans la mesure du possible.</span><span class="sxs-lookup"><span data-stu-id="64b7b-111">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="64b7b-112">Pour la plupart des applications, nous vous recommandons d’utiliser des SignalR WebSockets bruts.</span><span class="sxs-lookup"><span data-stu-id="64b7b-112">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="64b7b-113">SignalR fournit le secours de transport pour les environnements où WebSockets n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="64b7b-113">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="64b7b-114">Il fournit également un modèle d’application d’appel de procédure distante de base.</span><span class="sxs-lookup"><span data-stu-id="64b7b-114">It also provides a basic remote procedure call app model.</span></span> <span data-ttu-id="64b7b-115">Et dans la plupart des scénarios, ne présente pas de dépassements SignalR significatifs en matière de performances par rapport à l’utilisation de WebSocket RAW.</span><span class="sxs-lookup"><span data-stu-id="64b7b-115">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

<span data-ttu-id="64b7b-116">Pour certaines applications, [gRPC sur .net](xref:grpc/index) fournit une alternative à WebSockets.</span><span class="sxs-lookup"><span data-stu-id="64b7b-116">For some apps, [gRPC on .NET](xref:grpc/index) provides an alternative to WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="64b7b-117">Prérequis</span><span class="sxs-lookup"><span data-stu-id="64b7b-117">Prerequisites</span></span>

* <span data-ttu-id="64b7b-118">Tout système d’exploitation prenant en charge ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="64b7b-118">Any OS that supports ASP.NET Core:</span></span>  
  * <span data-ttu-id="64b7b-119">Windows 7 / Windows Server 2008 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="64b7b-119">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="64b7b-120">Linux</span><span class="sxs-lookup"><span data-stu-id="64b7b-120">Linux</span></span>
  * <span data-ttu-id="64b7b-121">macOS</span><span class="sxs-lookup"><span data-stu-id="64b7b-121">macOS</span></span>  
* <span data-ttu-id="64b7b-122">Si l’application s’exécute sur Windows avec IIS :</span><span class="sxs-lookup"><span data-stu-id="64b7b-122">If the app runs on Windows with IIS:</span></span>
  * <span data-ttu-id="64b7b-123">Windows 8/Windows Server 2012 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="64b7b-123">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="64b7b-124">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="64b7b-124">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="64b7b-125">Les WebSockets doivent être activés.</span><span class="sxs-lookup"><span data-stu-id="64b7b-125">WebSockets must be enabled.</span></span> <span data-ttu-id="64b7b-126">Consultez la section [prise en charge d’IIS/IIS Express](#iisiis-express-support) .</span><span class="sxs-lookup"><span data-stu-id="64b7b-126">See the [IIS/IIS Express support](#iisiis-express-support) section.</span></span>  
* <span data-ttu-id="64b7b-127">Si l’application s’exécute sur [HTTP.sys](xref:fundamentals/servers/httpsys) :</span><span class="sxs-lookup"><span data-stu-id="64b7b-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>
  * <span data-ttu-id="64b7b-128">Windows 8/Windows Server 2012 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="64b7b-128">Windows 8 / Windows Server 2012 or later</span></span>
* <span data-ttu-id="64b7b-129">Pour les navigateurs pris en charge, consultez https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="64b7b-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="configure-the-middleware"></a><span data-ttu-id="64b7b-130">Configurer les middlewares</span><span class="sxs-lookup"><span data-stu-id="64b7b-130">Configure the middleware</span></span>

<span data-ttu-id="64b7b-131">Ajoutez le middleware WebSocket dans la méthode `Configure` de la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="64b7b-131">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="64b7b-132">Les paramètres suivants peuvent être configurés :</span><span class="sxs-lookup"><span data-stu-id="64b7b-132">The following settings can be configured:</span></span>

* <span data-ttu-id="64b7b-133">`KeepAliveInterval` : fréquence d’envoi de frames de « ping » au client pour garantir que les proxys maintiennent la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="64b7b-133">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="64b7b-134">La valeur par défaut est deux minutes.</span><span class="sxs-lookup"><span data-stu-id="64b7b-134">The default is two minutes.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="64b7b-135">Les paramètres suivants peuvent être configurés :</span><span class="sxs-lookup"><span data-stu-id="64b7b-135">The following settings can be configured:</span></span>

* <span data-ttu-id="64b7b-136">`KeepAliveInterval` : fréquence d’envoi de frames de « ping » au client pour garantir que les proxys maintiennent la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="64b7b-136">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="64b7b-137">La valeur par défaut est deux minutes.</span><span class="sxs-lookup"><span data-stu-id="64b7b-137">The default is two minutes.</span></span>
* <span data-ttu-id="64b7b-138">`AllowedOrigins` : liste des valeurs d’en-tête Origin autorisées pour les requêtes WebSocket.</span><span class="sxs-lookup"><span data-stu-id="64b7b-138">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="64b7b-139">Par défaut, toutes les origines sont autorisées.</span><span class="sxs-lookup"><span data-stu-id="64b7b-139">By default, all origins are allowed.</span></span> <span data-ttu-id="64b7b-140">Pour plus d’informations, consultez « Restriction d’origine WebSocket » ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="64b7b-140">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="64b7b-141">Accepter les requêtes WebSocket</span><span class="sxs-lookup"><span data-stu-id="64b7b-141">Accept WebSocket requests</span></span>

<span data-ttu-id="64b7b-142">Un peu plus tard dans le cycle de vie de la requête (plus loin dans la méthode `Configure` ou dans une méthode action, par exemple), vérifiez s’il s’agit d’une requête WebSocket, puis acceptez-la.</span><span class="sxs-lookup"><span data-stu-id="64b7b-142">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="64b7b-143">L’exemple suivant est extrait d’une partie ultérieure de la méthode `Configure` :</span><span class="sxs-lookup"><span data-stu-id="64b7b-143">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="64b7b-144">Une requête WebSocket peut figurer sur toute URL, mais cet exemple de code accepte uniquement les requêtes pour `/ws`.</span><span class="sxs-lookup"><span data-stu-id="64b7b-144">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="64b7b-145">Lorsque vous utilisez un WebSocket, vous **devez** conserver le pipeline de l’intergiciel en cours d’exécution pendant la durée de la connexion.</span><span class="sxs-lookup"><span data-stu-id="64b7b-145">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="64b7b-146">Si vous tentez d’envoyer ou de recevoir un message WebSocket une fois que le pipeline de l’intergiciel se termine, vous pouvez obtenir une exception comme suit :</span><span class="sxs-lookup"><span data-stu-id="64b7b-146">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="64b7b-147">Si vous utilisez un service en arrière-plan pour écrire des données dans un WebSocket, assurez-vous que vous conservez le pipeline de l’intergiciel en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="64b7b-147">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="64b7b-148">Pour ce faire, utilisez un <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="64b7b-148">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="64b7b-149">Passez le `TaskCompletionSource` à l’arrière-plan de votre service et faites appeler <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> lorsque vous avez terminé avec le WebSocket.</span><span class="sxs-lookup"><span data-stu-id="64b7b-149">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="64b7b-150">Utilisez ensuite `await` sur la propriété <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> durant l’envoi de la requête, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="64b7b-150">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

<span data-ttu-id="64b7b-151">L’exception fermée WebSocket peut également se produire lors du retour d’une méthode d’action trop tôt.</span><span class="sxs-lookup"><span data-stu-id="64b7b-151">The WebSocket closed exception can also happen when returning too soon from an action method.</span></span> <span data-ttu-id="64b7b-152">Lors de l’acceptation d’un socket dans une méthode d’action, attendez que le code qui utilise le socket se termine avant de retourner à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="64b7b-152">When accepting a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="64b7b-153">N’utilisez jamais `Task.Wait`, `Task.Result` ou des appels de blocage similaires pour attendre la fin de l’exécution du socket, car cela peut entraîner de graves problèmes de thread.</span><span class="sxs-lookup"><span data-stu-id="64b7b-153">Never use `Task.Wait`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="64b7b-154">Utilisez toujours `await`.</span><span class="sxs-lookup"><span data-stu-id="64b7b-154">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="64b7b-155">Envoyer et recevoir des messages</span><span class="sxs-lookup"><span data-stu-id="64b7b-155">Send and receive messages</span></span>

<span data-ttu-id="64b7b-156">La méthode `AcceptWebSocketAsync` met à niveau la connexion TCP vers une connexion WebSocket et fournit un objet [WebSocket](/dotnet/core/api/system.net.websockets.websocket).</span><span class="sxs-lookup"><span data-stu-id="64b7b-156">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="64b7b-157">Utilisez l’objet `WebSocket` pour envoyer et recevoir des messages.</span><span class="sxs-lookup"><span data-stu-id="64b7b-157">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="64b7b-158">Le code montré précédemment qui accepte la requête WebSocket passe l’objet `WebSocket` à une méthode `Echo`.</span><span class="sxs-lookup"><span data-stu-id="64b7b-158">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="64b7b-159">Le code reçoit un message et renvoie immédiatement le même message.</span><span class="sxs-lookup"><span data-stu-id="64b7b-159">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="64b7b-160">Les messages sont envoyés et reçus dans une boucle jusqu’à ce que le client ferme la connexion :</span><span class="sxs-lookup"><span data-stu-id="64b7b-160">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="64b7b-161">Quand vous acceptez le WebSocket avant de commencer cette boucle, le pipeline de middlewares se termine.</span><span class="sxs-lookup"><span data-stu-id="64b7b-161">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="64b7b-162">À la fermeture du socket, le pipeline se déroule.</span><span class="sxs-lookup"><span data-stu-id="64b7b-162">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="64b7b-163">Autrement dit, la requête cesse d’avancer dans le pipeline quand le WebSocket est accepté.</span><span class="sxs-lookup"><span data-stu-id="64b7b-163">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="64b7b-164">Quand la boucle est terminée et que le socket est fermé, la requête recule dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="64b7b-164">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="64b7b-165">Gérer la déconnexion du client</span><span class="sxs-lookup"><span data-stu-id="64b7b-165">Handle client disconnects</span></span>

<span data-ttu-id="64b7b-166">Le serveur n’est pas automatiquement informé lorsque le client se déconnecte en raison d’une perte de connectivité.</span><span class="sxs-lookup"><span data-stu-id="64b7b-166">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="64b7b-167">Le serveur reçoit un message de déconnexion uniquement si le client l’envoie, ce qui n’est pas possible si la connexion Internet est perdue.</span><span class="sxs-lookup"><span data-stu-id="64b7b-167">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="64b7b-168">Si vous souhaitez prendre des mesures quand cela se produit, définissez un délai d’attente lorsque rien n’est reçu du client dans un certain laps de temps.</span><span class="sxs-lookup"><span data-stu-id="64b7b-168">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="64b7b-169">Si le client n’envoie toujours pas de messages et si vous ne souhaitez pas appliquer le délai d’attente uniquement parce que la connexion devient inactive, demandez au client d’utiliser un minuteur pour envoyer un message ping toutes les X secondes.</span><span class="sxs-lookup"><span data-stu-id="64b7b-169">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="64b7b-170">Sur le serveur, si un message n’arrive pas dans un délai de 2\*X secondes après le précédent, mettez fin à la connexion et signalez que le client s’est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="64b7b-170">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="64b7b-171">Attendez deux fois la durée de l’intervalle de temps attendu pour autoriser les délais réseau qui peuvent retarder le message ping.</span><span class="sxs-lookup"><span data-stu-id="64b7b-171">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="64b7b-172">Restriction d’origine WebSocket</span><span class="sxs-lookup"><span data-stu-id="64b7b-172">WebSocket origin restriction</span></span>

<span data-ttu-id="64b7b-173">Les protections fournies par CORS ne s’appliquent pas aux WebSockets.</span><span class="sxs-lookup"><span data-stu-id="64b7b-173">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="64b7b-174">Les navigateurs **:**</span><span class="sxs-lookup"><span data-stu-id="64b7b-174">Browsers do **not**:</span></span>

* <span data-ttu-id="64b7b-175">n’effectuent pas de requêtes préalables CORS ;</span><span class="sxs-lookup"><span data-stu-id="64b7b-175">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="64b7b-176">respectent les restrictions spécifiées dans les en-têtes `Access-Control` quand ils effectuent des requêtes WebSocket.</span><span class="sxs-lookup"><span data-stu-id="64b7b-176">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="64b7b-177">Toutefois, les navigateurs envoient l’en-tête `Origin` au moment de l’émission des requêtes WebSocket.</span><span class="sxs-lookup"><span data-stu-id="64b7b-177">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="64b7b-178">Les applications doivent être configurées de manière à valider ces en-têtes, le but étant de vérifier que seuls les WebSockets provenant des origines attendues sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="64b7b-178">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="64b7b-179">Si vous hébergez votre serveur sur «https://server.com» et votre client sur «https://client.com», ajoutez «https://client.com» à la liste `AllowedOrigins` pour autoriser les WebSockets.</span><span class="sxs-lookup"><span data-stu-id="64b7b-179">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="64b7b-180">L’en-tête `Origin` est contrôlé par le client et, comme l’en-tête `Referer`, peut être falsifié.</span><span class="sxs-lookup"><span data-stu-id="64b7b-180">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="64b7b-181">**N’utilisez pas** ces en-têtes comme mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="64b7b-181">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="64b7b-182">Prise en charge d’IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="64b7b-182">IIS/IIS Express support</span></span>

<span data-ttu-id="64b7b-183">Windows Server 2012 ou ultérieur et Windows 8 ou ultérieur avec IIS/IIS Express 8 ou ultérieure prennent en charge le protocole WebSocket.</span><span class="sxs-lookup"><span data-stu-id="64b7b-183">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="64b7b-184">WebSockets sont toujours activés lorsque vous utilisez IIS Express.</span><span class="sxs-lookup"><span data-stu-id="64b7b-184">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="64b7b-185">Activation de WebSockets sur IIS</span><span class="sxs-lookup"><span data-stu-id="64b7b-185">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="64b7b-186">Pour activer la prise en charge du protocole WebSocket sur Windows Server 2012 ou ultérieur :</span><span class="sxs-lookup"><span data-stu-id="64b7b-186">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="64b7b-187">Ces étapes ne sont pas nécessaires si vous utilisez IIS Express</span><span class="sxs-lookup"><span data-stu-id="64b7b-187">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="64b7b-188">Utilisez l’Assistant **Ajouter des rôles et des fonctionnalités** par le biais du menu **Gérer** ou du lien dans **Gestionnaire de serveur**.</span><span class="sxs-lookup"><span data-stu-id="64b7b-188">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="64b7b-189">Sélectionnez **Installation basée sur un rôle ou une fonctionnalité**.</span><span class="sxs-lookup"><span data-stu-id="64b7b-189">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="64b7b-190">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="64b7b-190">Select **Next**.</span></span>
1. <span data-ttu-id="64b7b-191">Sélectionnez le serveur approprié (le serveur local est sélectionné par défaut).</span><span class="sxs-lookup"><span data-stu-id="64b7b-191">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="64b7b-192">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="64b7b-192">Select **Next**.</span></span>
1. <span data-ttu-id="64b7b-193">Développez **Serveur web (IIS)** dans l’arborescence **Rôles**, développez **Serveur Web**, puis développez **Développement d’applications**.</span><span class="sxs-lookup"><span data-stu-id="64b7b-193">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="64b7b-194">Sélectionnez **Protocole WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="64b7b-194">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="64b7b-195">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="64b7b-195">Select **Next**.</span></span>
1. <span data-ttu-id="64b7b-196">Si vous n’avez pas besoin d’autres fonctionnalités, sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="64b7b-196">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="64b7b-197">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="64b7b-197">Select **Install**.</span></span>
1. <span data-ttu-id="64b7b-198">Une fois l’installation terminée, sélectionnez **Fermer** pour quitter l’Assistant.</span><span class="sxs-lookup"><span data-stu-id="64b7b-198">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="64b7b-199">Pour activer la prise en charge du protocole WebSocket sur Windows 8 ou ultérieur :</span><span class="sxs-lookup"><span data-stu-id="64b7b-199">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="64b7b-200">Ces étapes ne sont pas nécessaires si vous utilisez IIS Express</span><span class="sxs-lookup"><span data-stu-id="64b7b-200">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="64b7b-201">Accédez à **panneau de configuration**  >  **programmes** programmes  >  **et fonctionnalités**  >  **activer ou désactiver des fonctionnalités Windows** (côté gauche de l’écran).</span><span class="sxs-lookup"><span data-stu-id="64b7b-201">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="64b7b-202">Ouvrez les nœuds suivants : **Internet Information Services**  >  fonctionnalités de développement d’applications **World Wide Web services**  >  .</span><span class="sxs-lookup"><span data-stu-id="64b7b-202">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="64b7b-203">Sélectionnez la fonctionnalité **Protocole WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="64b7b-203">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="64b7b-204">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="64b7b-204">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="64b7b-205">Désactiver WebSocket lors de l’utilisation de socket.io sur Node.js</span><span class="sxs-lookup"><span data-stu-id="64b7b-205">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="64b7b-206">Si vous utilisez la prise en charge de WebSocket dans [Socket.IO](https://socket.io/) sur [Node.js](https://nodejs.org/), désactivez le module WebSocket IIS par défaut à l’aide `webSocket` de l’élément dans *web.config* ou *applicationHost.config*. Si cette étape n’est pas effectuée, le module WebSocket IIS tente de gérer la communication WebSocket plutôt que Node.js et l’application.</span><span class="sxs-lookup"><span data-stu-id="64b7b-206">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="64b7b-207">Exemple d'application</span><span class="sxs-lookup"><span data-stu-id="64b7b-207">Sample app</span></span>

<span data-ttu-id="64b7b-208">[L’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) qui accompagne cet article est une application d’écho.</span><span class="sxs-lookup"><span data-stu-id="64b7b-208">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="64b7b-209">Elle dispose d’une page Web qui établit des connexions WebSocket et le serveur renvoie tous les messages qu’elle reçoit au client.</span><span class="sxs-lookup"><span data-stu-id="64b7b-209">It has a webpage that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="64b7b-210">L’exemple d’application n’est pas configuré pour s’exécuter à partir de Visual Studio avec IIS Express. vous devez donc exécuter l’application dans un interpréteur de commandes avec [`dotnet run`](/dotnet/core/tools/dotnet-run) et naviguer dans un navigateur vers `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="64b7b-210">The sample app isn't configured to run from Visual Studio with IIS Express, so run the app in a command shell with [`dotnet run`](/dotnet/core/tools/dotnet-run) and navigate in a browser to `http://localhost:5000`.</span></span> <span data-ttu-id="64b7b-211">La page Web affiche l’état de la connexion :</span><span class="sxs-lookup"><span data-stu-id="64b7b-211">The webpage shows the connection status:</span></span>

![État initial de la page Web avant la connexion WebSocket](websockets/_static/start.png)

<span data-ttu-id="64b7b-213">Sélectionnez **Connect** (Connexion) pour envoyer une requête WebSocket à l’URL affichée.</span><span class="sxs-lookup"><span data-stu-id="64b7b-213">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="64b7b-214">Entrez un message de test, puis sélectionnez **Send** (Envoyer).</span><span class="sxs-lookup"><span data-stu-id="64b7b-214">Enter a test message and select **Send**.</span></span> <span data-ttu-id="64b7b-215">Quand vous avez terminé, sélectionnez **Close Socket** (Fermer le socket).</span><span class="sxs-lookup"><span data-stu-id="64b7b-215">When done, select **Close Socket**.</span></span> <span data-ttu-id="64b7b-216">La section **Communication Log** (Journal des communications) signale chaque action d’ouverture, d’envoi et de fermeture en temps réel.</span><span class="sxs-lookup"><span data-stu-id="64b7b-216">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![État final de la page Web après l’envoi et la réception des messages de connexion et de test WebSocket](websockets/_static/end.png)
