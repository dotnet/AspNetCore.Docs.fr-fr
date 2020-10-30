---
title: SignalRRésolution des problèmes de connexion ASP.net Core
author: bradygaster
description: SignalRRésolution des problèmes de connexion ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059609"
---
# <a name="troubleshoot-connection-errors"></a>Résoudre les erreurs de connexion

Cette section fournit de l’aide sur les erreurs qui peuvent se produire lors de la tentative d’établissement d’une connexion à un SignalR hub ASP.net core.

### <a name="response-code-404"></a>Code de réponse 404

Lors de l’utilisation de WebSockets et `skipNegotiation = true`
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* Lorsque vous utilisez plusieurs serveurs sans sessions rémanentes, la connexion peut démarrer sur un serveur, puis basculer vers un autre serveur. L’autre serveur n’est pas informé de la connexion précédente.
* Vérifiez que le client se connecte au point de terminaison correct. Par exemple, le serveur est hébergé sur `http://127.0.0.1:5000/hub/myHub` et le client tente de se connecter à `http://127.0.0.1:5000/myHub` .
* Si la connexion utilise l’ID et met trop de temps à envoyer une requête au serveur après Negotiate, le serveur :

  * Supprime l’ID.
  * Retourne un 404.

### <a name="response-code-400-or-503"></a>Code de réponse 400 ou 503

Pour l’erreur suivante :

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

Cette erreur est généralement causée par un client qui utilise uniquement le transport WebSocket, mais le protocole WebSocket n’est pas activé sur le serveur.

### <a name="response-code-307"></a>Code de réponse 307

Lors de l’utilisation de WebSockets et `skipNegotiation = true`
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

Cette erreur peut également se produire pendant la demande de négociation.

Cause courante :
* L’application est configurée pour appliquer le protocole HTTPs en appelant `UseHttpsRedirection` `Startup` , ou applique le protocole HTTPS via une règle de réécriture d’URL.

Solution possible :
* Remplacez l’URL côté client « http » par « https ». `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a>Code de réponse 405

Code d’état HTTP 405-méthode non autorisée

* [Cors](xref:signalr/security#cross-origin-resource-sharing) n’est pas activé pour l’application

### <a name="response-code-0"></a>Code de réponse 0

Code d’État http 0-généralement un problème [cors](xref:signalr/security#cross-origin-resource-sharing) , aucun code d’État n’est fourni

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* Ajouter les origines attendues à `.WithOrigins(...)`

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* Ajoutez `.AllowCredentials()` à votre stratégie cors. Impossible `.AllowAnyOrigin()` d’utiliser ou `.WithOrigins("*")` avec cette option

### <a name="response-code-413"></a>Code de réponse 413

Code d’État http 413-charge utile trop grande

Cela est souvent dû à un jeton d’accès qui est supérieur à 4 Ko.

* Si vous utilisez le SignalR service Azure, réduisez la taille des jetons en personnalisant les revendications envoyées par le biais du service avec :
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a>Échecs réseau temporaires

Les défaillances réseau temporaires peuvent fermer la SignalR connexion. Le serveur peut interpréter la connexion fermée comme une déconnexion normale du client. Pour obtenir plus d’informations sur la raison pour laquelle un client déconnecté dans ces cas [rassemble des journaux à partir du client et du serveur](xref:signalr/diagnostics).