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
# <a name="websockets-support-in-aspnet-core"></a>Prise en charge des WebSockets dans ASP.NET Core

Par [Tom Dykstra](https://github.com/tdykstra) et [Andrew Stanton-Nurse](https://github.com/anurse)

Cet article explique comment commencer avec les WebSockets dans ASP.NET Core. [WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) est un protocole qui autorise des canaux de communication persistants bidirectionnels sur les connexions TCP. Son utilisation profite aux applications qui tirent parti d’une communication rapide et en temps réel, par exemple les applications de conversation, de tableau de bord et de jeu.

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)). [Comment exécuter](#sample-app).

## SignalR

[ASP.net Core SignalR ](xref:signalr/introduction) est une bibliothèque qui simplifie l’ajout de fonctionnalités Web en temps réel aux applications. Elle utilise des WebSockets dans la mesure du possible.

Pour la plupart des applications, nous vous recommandons d’utiliser des SignalR WebSockets bruts. SignalR fournit le secours de transport pour les environnements où WebSockets n’est pas disponible. Il fournit également un modèle d’application d’appel de procédure distante de base. Et dans la plupart des scénarios, ne présente pas de dépassements SignalR significatifs en matière de performances par rapport à l’utilisation de WebSocket RAW.

Pour certaines applications, [gRPC sur .net](xref:grpc/index) fournit une alternative à WebSockets.

## <a name="prerequisites"></a>Prérequis

* Tout système d’exploitation prenant en charge ASP.NET Core :  
  * Windows 7 / Windows Server 2008 ou ultérieur
  * Linux
  * macOS  
* Si l’application s’exécute sur Windows avec IIS :
  * Windows 8/Windows Server 2012 ou ultérieur
  * IIS 8/IIS 8 Express
  * Les WebSockets doivent être activés. Consultez la section [prise en charge d’IIS/IIS Express](#iisiis-express-support) .  
* Si l’application s’exécute sur [HTTP.sys](xref:fundamentals/servers/httpsys) :
  * Windows 8/Windows Server 2012 ou ultérieur
* Pour les navigateurs pris en charge, consultez https://caniuse.com/#feat=websockets.

## <a name="configure-the-middleware"></a>Configurer les middlewares

Ajoutez le middleware WebSocket dans la méthode `Configure` de la classe `Startup` :

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

Les paramètres suivants peuvent être configurés :

* `KeepAliveInterval` : fréquence d’envoi de frames de « ping » au client pour garantir que les proxys maintiennent la connexion ouverte. La valeur par défaut est deux minutes.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Les paramètres suivants peuvent être configurés :

* `KeepAliveInterval` : fréquence d’envoi de frames de « ping » au client pour garantir que les proxys maintiennent la connexion ouverte. La valeur par défaut est deux minutes.
* `AllowedOrigins` : liste des valeurs d’en-tête Origin autorisées pour les requêtes WebSocket. Par défaut, toutes les origines sont autorisées. Pour plus d’informations, consultez « Restriction d’origine WebSocket » ci-dessous.

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a>Accepter les requêtes WebSocket

Un peu plus tard dans le cycle de vie de la requête (plus loin dans la méthode `Configure` ou dans une méthode action, par exemple), vérifiez s’il s’agit d’une requête WebSocket, puis acceptez-la.

L’exemple suivant est extrait d’une partie ultérieure de la méthode `Configure` :

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

Une requête WebSocket peut figurer sur toute URL, mais cet exemple de code accepte uniquement les requêtes pour `/ws`.

Lorsque vous utilisez un WebSocket, vous **devez** conserver le pipeline de l’intergiciel en cours d’exécution pendant la durée de la connexion. Si vous tentez d’envoyer ou de recevoir un message WebSocket une fois que le pipeline de l’intergiciel se termine, vous pouvez obtenir une exception comme suit :

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

Si vous utilisez un service en arrière-plan pour écrire des données dans un WebSocket, assurez-vous que vous conservez le pipeline de l’intergiciel en cours d’exécution. Pour ce faire, utilisez un <xref:System.Threading.Tasks.TaskCompletionSource%601>. Passez le `TaskCompletionSource` à l’arrière-plan de votre service et faites appeler <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> lorsque vous avez terminé avec le WebSocket. Utilisez ensuite `await` sur la propriété <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> durant l’envoi de la requête, comme indiqué dans l’exemple suivant :

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

L’exception fermée WebSocket peut également se produire lors du retour d’une méthode d’action trop tôt. Lors de l’acceptation d’un socket dans une méthode d’action, attendez que le code qui utilise le socket se termine avant de retourner à partir de la méthode d’action.

N’utilisez jamais `Task.Wait`, `Task.Result` ou des appels de blocage similaires pour attendre la fin de l’exécution du socket, car cela peut entraîner de graves problèmes de thread. Utilisez toujours `await`.

## <a name="send-and-receive-messages"></a>Envoyer et recevoir des messages

La méthode `AcceptWebSocketAsync` met à niveau la connexion TCP vers une connexion WebSocket et fournit un objet [WebSocket](/dotnet/core/api/system.net.websockets.websocket). Utilisez l’objet `WebSocket` pour envoyer et recevoir des messages.

Le code montré précédemment qui accepte la requête WebSocket passe l’objet `WebSocket` à une méthode `Echo`. Le code reçoit un message et renvoie immédiatement le même message. Les messages sont envoyés et reçus dans une boucle jusqu’à ce que le client ferme la connexion :

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

Quand vous acceptez le WebSocket avant de commencer cette boucle, le pipeline de middlewares se termine. À la fermeture du socket, le pipeline se déroule. Autrement dit, la requête cesse d’avancer dans le pipeline quand le WebSocket est accepté. Quand la boucle est terminée et que le socket est fermé, la requête recule dans le pipeline.

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a>Gérer la déconnexion du client

Le serveur n’est pas automatiquement informé lorsque le client se déconnecte en raison d’une perte de connectivité. Le serveur reçoit un message de déconnexion uniquement si le client l’envoie, ce qui n’est pas possible si la connexion Internet est perdue. Si vous souhaitez prendre des mesures quand cela se produit, définissez un délai d’attente lorsque rien n’est reçu du client dans un certain laps de temps.

Si le client n’envoie toujours pas de messages et si vous ne souhaitez pas appliquer le délai d’attente uniquement parce que la connexion devient inactive, demandez au client d’utiliser un minuteur pour envoyer un message ping toutes les X secondes. Sur le serveur, si un message n’arrive pas dans un délai de 2\*X secondes après le précédent, mettez fin à la connexion et signalez que le client s’est déconnecté. Attendez deux fois la durée de l’intervalle de temps attendu pour autoriser les délais réseau qui peuvent retarder le message ping.

## <a name="websocket-origin-restriction"></a>Restriction d’origine WebSocket

Les protections fournies par CORS ne s’appliquent pas aux WebSockets. Les navigateurs **:**

* n’effectuent pas de requêtes préalables CORS ;
* respectent les restrictions spécifiées dans les en-têtes `Access-Control` quand ils effectuent des requêtes WebSocket.

Toutefois, les navigateurs envoient l’en-tête `Origin` au moment de l’émission des requêtes WebSocket. Les applications doivent être configurées de manière à valider ces en-têtes, le but étant de vérifier que seuls les WebSockets provenant des origines attendues sont autorisés.

Si vous hébergez votre serveur sur «https://server.com» et votre client sur «https://client.com», ajoutez «https://client.com» à la liste `AllowedOrigins` pour autoriser les WebSockets.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> L’en-tête `Origin` est contrôlé par le client et, comme l’en-tête `Referer`, peut être falsifié. **N’utilisez pas** ces en-têtes comme mécanisme d’authentification.

::: moniker-end

## <a name="iisiis-express-support"></a>Prise en charge d’IIS/IIS Express

Windows Server 2012 ou ultérieur et Windows 8 ou ultérieur avec IIS/IIS Express 8 ou ultérieure prennent en charge le protocole WebSocket.

> [!NOTE]
> WebSockets sont toujours activés lorsque vous utilisez IIS Express.

### <a name="enabling-websockets-on-iis"></a>Activation de WebSockets sur IIS

Pour activer la prise en charge du protocole WebSocket sur Windows Server 2012 ou ultérieur :

> [!NOTE]
> Ces étapes ne sont pas nécessaires si vous utilisez IIS Express

1. Utilisez l’Assistant **Ajouter des rôles et des fonctionnalités** par le biais du menu **Gérer** ou du lien dans **Gestionnaire de serveur**.
1. Sélectionnez **Installation basée sur un rôle ou une fonctionnalité**. Sélectionnez **Suivant**.
1. Sélectionnez le serveur approprié (le serveur local est sélectionné par défaut). Sélectionnez **Suivant**.
1. Développez **Serveur web (IIS)** dans l’arborescence **Rôles**, développez **Serveur Web**, puis développez **Développement d’applications**.
1. Sélectionnez **Protocole WebSocket**. Sélectionnez **Suivant**.
1. Si vous n’avez pas besoin d’autres fonctionnalités, sélectionnez **Suivant**.
1. Sélectionnez **Installer**.
1. Une fois l’installation terminée, sélectionnez **Fermer** pour quitter l’Assistant.

Pour activer la prise en charge du protocole WebSocket sur Windows 8 ou ultérieur :

> [!NOTE]
> Ces étapes ne sont pas nécessaires si vous utilisez IIS Express

1. Accédez à **panneau de configuration**  >  **programmes** programmes  >  **et fonctionnalités**  >  **activer ou désactiver des fonctionnalités Windows** (côté gauche de l’écran).
1. Ouvrez les nœuds suivants : **Internet Information Services**  >  fonctionnalités de développement d’applications **World Wide Web services**  >  .
1. Sélectionnez la fonctionnalité **Protocole WebSocket**. Sélectionnez **OK**.

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>Désactiver WebSocket lors de l’utilisation de socket.io sur Node.js

Si vous utilisez la prise en charge de WebSocket dans [Socket.IO](https://socket.io/) sur [Node.js](https://nodejs.org/), désactivez le module WebSocket IIS par défaut à l’aide `webSocket` de l’élément dans *web.config* ou *applicationHost.config*. Si cette étape n’est pas effectuée, le module WebSocket IIS tente de gérer la communication WebSocket plutôt que Node.js et l’application.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a>Exemple d'application

[L’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) qui accompagne cet article est une application d’écho. Elle dispose d’une page Web qui établit des connexions WebSocket et le serveur renvoie tous les messages qu’elle reçoit au client. L’exemple d’application n’est pas configuré pour s’exécuter à partir de Visual Studio avec IIS Express. vous devez donc exécuter l’application dans un interpréteur de commandes avec [`dotnet run`](/dotnet/core/tools/dotnet-run) et naviguer dans un navigateur vers `http://localhost:5000` . La page Web affiche l’état de la connexion :

![État initial de la page Web avant la connexion WebSocket](websockets/_static/start.png)

Sélectionnez **Connect** (Connexion) pour envoyer une requête WebSocket à l’URL affichée. Entrez un message de test, puis sélectionnez **Send** (Envoyer). Quand vous avez terminé, sélectionnez **Close Socket** (Fermer le socket). La section **Communication Log** (Journal des communications) signale chaque action d’ouverture, d’envoi et de fermeture en temps réel.

![État final de la page Web après l’envoi et la réception des messages de connexion et de test WebSocket](websockets/_static/end.png)
