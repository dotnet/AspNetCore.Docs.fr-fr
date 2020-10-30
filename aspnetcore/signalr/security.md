---
title: Considérations relatives à la sécurité dans ASP.NET Core SignalR
author: bradygaster
description: Découvrez comment utiliser l’authentification et l’autorisation dans ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
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
uid: signalr/security
ms.openlocfilehash: 5ecbf07b1527e9c68443870f7fce77adc29a5416
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050834"
---
# <a name="security-considerations-in-aspnet-core-no-locsignalr"></a>Considérations relatives à la sécurité dans ASP.NET Core SignalR

Par [Andrew Stanton-infirmière](https://twitter.com/anurse)

Cet article fournit des informations sur la sécurisation de SignalR .

## <a name="cross-origin-resource-sharing"></a>Partage de ressources cross-origin

Le [partage des ressources Cross-Origin (cors)](https://www.w3.org/TR/cors/) peut être utilisé pour autoriser les connexions Cross-Origin SignalR dans le navigateur. Si le code JavaScript est hébergé sur un domaine différent de l' SignalR application, l' [intergiciel (middleware) cors](xref:security/cors) doit être activé pour permettre au JavaScript de se connecter à l' SignalR application. Autorisez les requêtes Cross-Origin uniquement à partir des domaines que vous approuvez ou contrôlez. Exemple :

* Votre site est hébergé sur `http://www.example.com`
* Votre SignalR application est hébergée sur `http://signalr.example.com`

CORS doit être configuré dans l' SignalR application afin d’autoriser uniquement l’origine `www.example.com` .

Pour plus d’informations sur la configuration de CORS, consultez [activer les demandes Cross-Origin (cors)](xref:security/cors). SignalR**requiert** les stratégies cors suivantes :

* Autorisez les origines attendues spécifiques. L’autorisation d’une origine est possible, mais elle n’est **pas** sécurisée ou recommandée.
* Les méthodes HTTP `GET` et `POST` doivent être autorisées.
* Les informations d’identification doivent être autorisées pour que les cookie sessions rémanentes basées sur fonctionnent correctement. Ils doivent être activés même lorsque l’authentification n’est pas utilisée.

::: moniker range=">= aspnetcore-5.0"

Toutefois, dans 5,0, nous avons fourni une option dans le client de machine à écrire pour ne pas utiliser les informations d’identification.
L’option permettant de ne pas utiliser les informations d’identification ne doit être utilisée que si vous connaissez 100% que les informations d’identification telles que ne Cookie sont pas nécessaires dans votre application ( cookie s sont utilisées par Azure App service lors de l’utilisation de plusieurs serveurs pour les sessions rémanentes).

::: moniker-end

Par exemple, la stratégie CORS suivante permet SignalR à un client de navigateur hébergé sur `https://example.com` d’accéder à l' SignalR application hébergée sur `https://signalr.example.com` :

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a>Restriction d’origine WebSocket

::: moniker range=">= aspnetcore-2.2"

Les protections fournies par CORS ne s’appliquent pas aux WebSockets. Pour une restriction d’origine sur les WebSockets, consultez [restriction d’origine des WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Les protections fournies par CORS ne s’appliquent pas aux WebSockets. Les navigateurs **:**

* n’effectuent pas de requêtes préalables CORS ;
* respectent les restrictions spécifiées dans les en-têtes `Access-Control` quand ils effectuent des requêtes WebSocket.

Toutefois, les navigateurs envoient l’en-tête `Origin` au moment de l’émission des requêtes WebSocket. Les applications doivent être configurées de manière à valider ces en-têtes, le but étant de vérifier que seuls les WebSockets provenant des origines attendues sont autorisés.

Dans ASP.NET Core 2,1 et versions ultérieures, la validation d’en-tête peut être obtenue à l’aide d’un intergiciel (middleware) personnalisé placé **précédemment `UseSignalR` et de l’intergiciel (middleware) d’authentification** dans `Configure` :

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> L’en-tête `Origin` est contrôlé par le client et, comme l’en-tête `Referer`, peut être falsifié. Ces en-têtes ne doivent **pas** être utilisés en tant que mécanisme d’authentification.

::: moniker-end

## <a name="connectionid"></a>ConnectionId

L’exposition `ConnectionId` peut entraîner un emprunt d’identité malveillant si la SignalR version du serveur ou du client est ASP.net Core 2,2 ou une version antérieure. Si la SignalR version du serveur et du client est ASP.NET Core 3,0 ou version ultérieure, le au `ConnectionToken` lieu de `ConnectionId` doit être gardé secret. Le `ConnectionToken` n’est volontairement pas exposé dans une API.  Il peut être difficile de s’assurer que SignalR les anciens clients ne se connectent pas au serveur. par conséquent, même si la version de votre SignalR serveur est ASP.net Core 3,0 ou version ultérieure, le `ConnectionId` ne doit pas être exposé.

## <a name="access-token-logging"></a>Journalisation des jetons d’accès

Lors de l’utilisation d’événements WebSocket ou Server-Sent, le client navigateur envoie le jeton d’accès dans la chaîne de requête. La réception du jeton d’accès via la chaîne de requête est généralement sécurisée à l’aide de l' `Authorization` en-tête standard. Utilisez toujours le protocole HTTPs pour garantir une connexion sécurisée de bout en bout entre le client et le serveur. De nombreux serveurs Web consignent l’URL pour chaque demande, y compris la chaîne de requête. La journalisation des URL peut enregistrer le jeton d’accès. ASP.NET Core enregistre l’URL pour chaque demande par défaut, qui inclut la chaîne de requête. Exemple :

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/chathub?access_token=1234
```

Si vous avez des doutes quant à la journalisation de ces données avec les journaux de votre serveur, vous pouvez désactiver cette journalisation entièrement en configurant l' `Microsoft.AspNetCore.Hosting` enregistreur d’événements au `Warning` niveau ou au-dessus (ces messages sont écrits au `Info` niveau). Pour plus d’informations, consultez [filtrage des journaux](xref:fundamentals/logging/index#log-filtering) pour plus d’informations. Si vous souhaitez toujours enregistrer certaines informations de demande, vous pouvez [écrire un intergiciel (middleware](xref:fundamentals/middleware/write) ) pour enregistrer les données dont vous avez besoin et filtrer la valeur de la `access_token` chaîne de requête (le cas échéant).

## <a name="exceptions"></a>Exceptions

Les messages d’exception sont généralement considérés comme des données sensibles qui ne doivent pas être révélées à un client. Par défaut, SignalR n’envoie pas les détails d’une exception levée par une méthode de concentrateur au client. Au lieu de cela, le client reçoit un message générique indiquant qu’une erreur s’est produite. La remise de message d’exception au client peut être remplacée (par exemple, dans le développement ou le test) par [EnableDetailedErrors](xref:signalr/configuration#configure-server-options). Les messages d’exception ne doivent pas être exposés au client dans les applications de production.

## <a name="buffer-management"></a>Gestion des tampons

SignalR utilise des tampons par connexion pour gérer les messages entrants et sortants. Par défaut, SignalR limite ces mémoires tampons à 32 Ko. Le plus grand message qu’un client ou un serveur peut envoyer est de 32 Ko. La mémoire maximale consommée par une connexion pour les messages est de 32 Ko. Si vos messages sont toujours inférieurs à 32 Ko, vous pouvez réduire la limite, qui :

* Empêche un client d’envoyer un message plus volumineux.
* Le serveur n’a jamais besoin d’allouer de grandes mémoires tampons pour accepter des messages.

Si vos messages sont supérieurs à 32 Ko, vous pouvez augmenter la limite. L’amélioration de cette limite signifie :

* Le client peut forcer le serveur à allouer de grandes mémoires tampons de mémoire.
* L’allocation de serveurs de mémoires tampons de grande taille peut réduire le nombre de connexions simultanées.

Il existe des limites pour les messages entrants et sortants, qui peuvent être configurés sur l’objet [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) configuré dans `MapHub` :

* `ApplicationMaxBufferSize` représente le nombre maximal d’octets du client que le serveur met en mémoire tampon. Si le client tente d’envoyer un message d’une taille supérieure à cette limite, la connexion peut être fermée.
* `TransportMaxBufferSize` représente le nombre maximal d’octets que le serveur peut envoyer. Si le serveur tente d’envoyer un message (y compris les valeurs de retour des méthodes de concentrateur) supérieures à cette limite, une exception est levée.

La définition de la limite à `0` désactive la limite. La suppression de la limite permet à un client d’envoyer un message de toute taille. Les clients malveillants qui envoient des messages volumineux peuvent entraîner l’allocation de mémoire supplémentaire. L’utilisation excessive de la mémoire peut réduire de manière significative le nombre de connexions simultanées.
