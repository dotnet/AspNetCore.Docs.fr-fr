---
title: Utiliser des hubs dans ASP.NET Core SignalR
author: bradygaster
description: Découvrez comment utiliser des hubs dans ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/hubs
ms.openlocfilehash: 4a31c16eb44e2244574d0df49c30e7a44b2bba6e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050938"
---
# <a name="use-hubs-in-no-locsignalr-for-aspnet-core"></a>Utiliser des hubs dans SignalR pour ASP.net Core

Par [Rachel appel](https://twitter.com/rachelappel) et [Kevin Griffin](https://twitter.com/1kevgriff)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-no-locsignalr-hub"></a>Qu’est-ce qu’un SignalR Hub ?

L' SignalR API hubs vous permet d’appeler des méthodes sur des clients connectés à partir du serveur. Dans le code serveur, vous définissez des méthodes qui sont appelées par le client. Dans le code client, vous définissez des méthodes qui sont appelées à partir du serveur. SignalR s’occupe de tout en arrière-plan qui rend possible les communications de client à serveur et de serveur à client en temps réel.

## <a name="configure-no-locsignalr-hubs"></a>Configurer des SignalR hubs

L' SignalR intergiciel (middleware) requiert des services qui sont configurés en appelant `services.AddSignalR` .

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

Lors de SignalR l’ajout de fonctionnalités à une application ASP.net Core, le programme d’installation effectue SignalR le routage en appelant `endpoint.MapHub` dans le `Startup.Configure` rappel de la méthode `app.UseEndpoints` .

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Lors de SignalR l’ajout de fonctionnalités à une application ASP.net Core, le programme d’installation effectue SignalR le routage en appelant `app.UseSignalR` dans la `Startup.Configure` méthode.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>Créer et utiliser des hubs

Créez un concentrateur en déclarant une classe qui hérite de `Hub` et ajoutez des méthodes publiques à celle-ci. Les clients peuvent appeler des méthodes qui sont définies en tant que `public` .

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Vous pouvez spécifier un type de retour et des paramètres, y compris des types complexes et des tableaux, comme vous le feriez dans une méthode C#. SignalR gère la sérialisation et la désérialisation d’objets et de tableaux complexes dans vos paramètres et valeurs de retour.

> [!NOTE]
> Les concentrateurs sont temporaires :
>
> * Ne stockez pas l’État dans une propriété de la classe de concentrateur. Chaque appel de méthode de concentrateur est exécuté sur une nouvelle instance de concentrateur.
> * Utilisez `await` lors de l’appel de méthodes asynchrones qui dépendent du concentrateur qui reste actif. Par exemple, une méthode telle que `Clients.All.SendAsync(...)` peut échouer si elle est appelée sans `await` et que la méthode de concentrateur se termine avant la fin de l’opération `SendAsync` .

## <a name="the-context-object"></a>Objet de contexte

La `Hub` classe a une `Context` propriété qui contient les propriétés suivantes avec des informations sur la connexion :

| Propriété | Description |
| ------ | ----------- |
| `ConnectionId` | Obtient l’ID unique de la connexion, assignée par SignalR . Il existe un ID de connexion pour chaque connexion.|
| `UserIdentifier` | Obtient l' [identificateur](xref:signalr/groups)de l’utilisateur. Par défaut, SignalR utilise `ClaimTypes.NameIdentifier` à partir du `ClaimsPrincipal` associé à la connexion comme identificateur d’utilisateur. |
| `User` | Obtient le `ClaimsPrincipal` associé à l’utilisateur actuel. |
| `Items` | Obtient une collection clé/valeur qui peut être utilisée pour partager des données dans l’étendue de cette connexion. Les données peuvent être stockées dans cette collection et conservées pour la connexion entre différents appels de méthode de concentrateur. |
| `Features` | Obtient la collection de fonctionnalités disponibles sur la connexion. Pour le moment, cette collection n’est pas nécessaire dans la plupart des scénarios. elle n’est donc pas encore documentée en détail. |
| `ConnectionAborted` | Obtient un `CancellationToken` qui notifie lorsque la connexion est abandonnée. |

`Hub.Context` contient également les méthodes suivantes :

| Méthode | Description |
| ------ | ----------- |
| `GetHttpContext` | Retourne le `HttpContext` de la connexion ou `null` si la connexion n’est pas associée à une requête http. Pour les connexions HTTP, vous pouvez utiliser cette méthode pour obtenir des informations telles que les en-têtes HTTP et les chaînes de requête. |
| `Abort` | Abandonne la connexion. |

## <a name="the-clients-object"></a>Objet clients

La `Hub` classe a une `Clients` propriété qui contient les propriétés suivantes pour la communication entre le serveur et le client :

| Propriété | Description |
| ------ | ----------- |
| `All` | Appelle une méthode sur tous les clients connectés |
| `Caller` | Appelle une méthode sur le client qui a appelé la méthode de concentrateur |
| `Others` | Appelle une méthode sur tous les clients connectés, à l’exception du client qui a appelé la méthode. |

`Hub.Clients` contient également les méthodes suivantes :

| Méthode | Description |
| ------ | ----------- |
| `AllExcept` | Appelle une méthode sur tous les clients connectés, à l’exception des connexions spécifiées |
| `Client` | Appelle une méthode sur un client connecté spécifique |
| `Clients` | Appelle une méthode sur des clients connectés spécifiques |
| `Group` | Appelle une méthode sur toutes les connexions dans le groupe spécifié  |
| `GroupExcept` | Appelle une méthode sur toutes les connexions dans le groupe spécifié, à l’exception des connexions spécifiées. |
| `Groups` | Appelle une méthode sur plusieurs groupes de connexions  |
| `OthersInGroup` | Appelle une méthode sur un groupe de connexions, à l’exclusion du client qui a appelé la méthode de concentrateur  |
| `User` | Appelle une méthode sur toutes les connexions associées à un utilisateur spécifique |
| `Users` | Appelle une méthode sur toutes les connexions associées aux utilisateurs spécifiés |

Chaque propriété ou méthode dans les tables précédentes retourne un objet avec une `SendAsync` méthode. La `SendAsync` méthode vous permet de fournir le nom et les paramètres de la méthode cliente à appeler.

## <a name="send-messages-to-clients"></a>Envoyer des messages aux clients

Pour effectuer des appels à des clients spécifiques, utilisez les propriétés de l' `Clients` objet. Dans l’exemple suivant, il existe trois méthodes de concentrateur :

* `SendMessage` envoie un message à tous les clients connectés à l’aide de `Clients.All` .
* `SendMessageToCaller` renvoie un message à l’appelant à l’aide de `Clients.Caller` .
* `SendMessageToGroups` envoie un message à tous les clients du `SignalR Users` groupe.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Hubs fortement typés

L’inconvénient de l’utilisation de `SendAsync` est qu’elle s’appuie sur une chaîne magique pour spécifier la méthode cliente à appeler. Cela laisse le code ouvert aux erreurs d’exécution si le nom de la méthode est mal orthographié ou manquant dans le client.

Une alternative à l’utilisation de `SendAsync` consiste à taper fortement le `Hub` avec <xref:Microsoft.AspNetCore.SignalR.Hub%601> . Dans l’exemple suivant, les `ChatHub` méthodes clientes ont été extraites dans une interface appelée `IChatClient` .

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Cette interface peut être utilisée pour refactoriser l' `ChatHub` exemple précédent.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

L’utilisation `Hub<IChatClient>` de active la vérification au moment de la compilation des méthodes clientes. Cela empêche les problèmes causés par l’utilisation de chaînes magiques, car `Hub<T>` peut uniquement fournir l’accès aux méthodes définies dans l’interface.

L’utilisation d’un fortement typé `Hub<T>` désactive la capacité à utiliser `SendAsync` . Toutes les méthodes définies sur l’interface peuvent toujours être définies comme asynchrones. En fait, chacune de ces méthodes doit retourner un `Task` . Étant donné qu’il s’agit d’une interface, n’utilisez pas le `async` mot clé. Exemple :

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> Le `Async` suffixe n’est pas supprimé du nom de la méthode. À moins que votre méthode cliente ne soit définie avec `.on('MyMethodAsync')` , vous ne devez pas utiliser `MyMethodAsync` comme nom.

## <a name="change-the-name-of-a-hub-method"></a>Modifier le nom d’une méthode de concentrateur

Par défaut, un nom de méthode de concentrateur de serveur est le nom de la méthode .NET. Toutefois, vous pouvez utiliser l’attribut [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) pour modifier cette valeur par défaut et spécifier manuellement un nom pour la méthode. Le client doit utiliser ce nom au lieu du nom de la méthode .NET, lors de l’appel de la méthode.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Gérer les événements pour une connexion

L' SignalR API hubs fournit les `OnConnectedAsync` `OnDisconnectedAsync` méthodes virtuelles et pour gérer et suivre les connexions. Substituez la `OnConnectedAsync` méthode virtuelle pour exécuter des actions lorsqu’un client se connecte au concentrateur, par exemple l’ajouter à un groupe.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Substituez la `OnDisconnectedAsync` méthode virtuelle pour exécuter des actions lorsqu’un client se déconnecte. Si le client se déconnecte intentionnellement (en appelant `connection.stop()` , par exemple), le `exception` paramètre est `null` . Toutefois, si le client est déconnecté en raison d’une erreur (telle qu’une défaillance du réseau), le `exception` paramètre contiendra une exception décrivant l’échec.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a>Gérer les erreurs

Les exceptions levées dans vos méthodes de concentrateur sont envoyées au client qui a appelé la méthode. Sur le client JavaScript, la `invoke` méthode retourne une [promesse JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). Lorsque le client reçoit une erreur avec un gestionnaire attaché à la promesse à l’aide de `catch` , il est appelé et transmis en tant qu' `Error` objet JavaScript.

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Si votre Hub lève une exception, les connexions ne sont pas fermées. Par défaut, SignalR retourne un message d’erreur générique au client. Exemple :

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Les exceptions inattendues contiennent souvent des informations sensibles, telles que le nom d’un serveur de base de données dans une exception déclenchée lorsque la connexion à la base de données échoue. SignalR n’expose pas ces messages d’erreur détaillés par défaut en tant que mesure de sécurité. Pour plus d’informations sur la raison pour laquelle les détails de l’exception sont supprimés, consultez l' [article considérations](xref:signalr/security#exceptions) sur la sécurité.

Si vous avez une condition *exceptionnelle que vous souhaitez* propager au client, vous pouvez utiliser la `HubException` classe. Si vous levez un `HubException` à partir de votre méthode de concentrateur, enverra SignalR **will** l’intégralité du message au client, sans modification.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR envoie uniquement la `Message` propriété de l’exception au client. La trace de la pile et les autres propriétés de l’exception ne sont pas disponibles pour le client.

## <a name="related-resources"></a>Ressources associées

* [Introduction à ASP.NET Core SignalR](xref:signalr/introduction)
* [Client JavaScript](xref:signalr/javascript-client)
* [Publication dans Azure](xref:signalr/publish-to-azure-web-app)
