---
title: ASP.NET Core SignalR de l’hébergement et de la mise à l’échelle de production
author: bradygaster
description: Découvrez comment éviter les problèmes de performances et de mise à l’échelle dans les applications qui utilisent ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: e70f3143159a1817e326a95b30e7369a5c9ab025
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564011"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>SignalRHébergement et mise à l’échelle ASP.net Core

Par [Andrew Stanton-infirmière](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)et [Tom Dykstra](https://github.com/tdykstra)

Cet article explique les considérations relatives à l’hébergement et à la mise à l’échelle pour les applications à fort trafic qui utilisent ASP.NET Core SignalR .

## <a name="sticky-sessions"></a>Sessions rémanentes

SignalR requiert que toutes les requêtes HTTP pour une connexion spécifique soient gérées par le même processus serveur. Lorsque SignalR s’exécute sur une batterie de serveurs (plusieurs serveurs), vous devez utiliser des « sessions rémanentes ». Les « sessions rémanentes » sont également appelées « affinité de session » par certains équilibrages de charge. Azure App Service utilise [application Request Routing](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (arr) pour acheminer les demandes. L’activation du paramètre « affinité ARR » dans votre Azure App Service permet d’activer les « sessions rémanentes ». Les sessions rémanentes ne sont pas nécessaires dans les cas suivants :

1. En cas d’hébergement sur un seul serveur, dans un seul processus.
1. Lors de l’utilisation du SignalR service Azure.
1. Lorsque tous les clients sont configurés pour utiliser **uniquement** les WebSockets, **et** que le [paramètre SkipNegotiation](xref:signalr/configuration#configure-additional-options) est activé dans la configuration du client.

Dans toutes les autres circonstances (y compris lorsque le fond de panier ReDim est utilisé), l’environnement du serveur doit être configuré pour les sessions rémanentes.

Pour obtenir des conseils sur la configuration de Azure App Service pour SignalR , consultez <xref:signalr/publish-to-azure-web-app> .

## <a name="tcp-connection-resources"></a>Ressources de connexion TCP

Le nombre de connexions TCP simultanées qu’un serveur Web peut prendre en charge est limité. Les clients HTTP standard utilisent des connexions *éphémères* . Ces connexions peuvent être fermées lorsque le client devient inactif et rouvert ultérieurement. En revanche, une SignalR connexion est *persistante*. SignalR les connexions restent ouvertes même lorsque le client devient inactif. Dans une application à trafic élevé qui dessert de nombreux clients, ces connexions persistantes peuvent entraîner l’atteinte du nombre maximal de connexions des serveurs.

Les connexions persistantes consomment également de la mémoire supplémentaire pour effectuer le suivi de chaque connexion.

L’utilisation intensive des ressources liées à la connexion par SignalR peut affecter les autres applications Web qui sont hébergées sur le même serveur. Lorsque SignalR ouvre et contient les dernières connexions TCP disponibles, les autres applications Web sur le même serveur n’ont pas non plus de connexions disponibles.

Si un serveur est à court de connexions, vous verrez des erreurs de socket aléatoires et des erreurs de réinitialisation de la connexion. Par exemple :

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Pour empêcher SignalR l’utilisation des ressources de provoquer des erreurs dans d’autres applications Web, exécutez SignalR sur des serveurs différents de ceux des autres applications Web.

Pour empêcher l' SignalR utilisation des ressources de provoquer des erreurs dans une SignalR application, augmentez la taille des instances pour limiter le nombre de connexions qu’un serveur doit gérer.

## <a name="scale-out"></a>Scale-out

Une application qui utilise SignalR doit effectuer le suivi de toutes ses connexions, ce qui crée des problèmes pour une batterie de serveurs. Ajoutez un serveur et il obtient les nouvelles connexions que les autres serveurs ne connaissent pas. Par exemple, SignalR sur chaque serveur du diagramme suivant n’a pas connaissance des connexions sur les autres serveurs. Lorsque SignalR sur l’un des serveurs souhaite envoyer un message à tous les clients, le message est envoyé uniquement aux clients connectés à ce serveur.

![Mise à l’échelle ::: No-Loc (Signalr) ::: sans fond de panier](scale/_static/scale-no-backplane.png)

Les options permettant de résoudre ce problème sont [le SignalR service Azure](#azure-signalr-service) et le [fond de panier ReDim](#redis-backplane).

## <a name="azure-signalr-service"></a>SignalRService Azure

Le SignalR service Azure est un proxy plutôt qu’un fond de panier. Chaque fois qu’un client initie une connexion au serveur, le client est redirigé pour se connecter au service. Ce processus est illustré dans le diagramme suivant :

![Établissement d’une connexion à Azure ::: No-Loc (Signalr) ::: service](scale/_static/azure-signalr-service-one-connection.png)

Le résultat est que le service gère toutes les connexions clientes, alors que chaque serveur n’a besoin que d’un petit nombre constant de connexions au service, comme indiqué dans le diagramme suivant :

![Clients connectés au service, serveurs connectés au service](scale/_static/azure-signalr-service-multiple-connections.png)

Cette approche de la montée en puissance parallèle présente plusieurs avantages par rapport au fond de panier ReDim :

* Les sessions rémanentes, également appelées « [affinité du client](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)», ne sont pas nécessaires, car les clients sont immédiatement redirigés vers le SignalR service Azure lorsqu’ils se connectent.
* Une SignalR application peut évoluer en fonction du nombre de messages envoyés, tandis que le service Azure est mis à l’échelle SignalR pour gérer un nombre quelconque de connexions. Par exemple, il peut y avoir des milliers de clients, mais si seuls quelques messages par seconde sont envoyés, l' SignalR application n’a pas besoin d’effectuer une montée en charge sur plusieurs serveurs uniquement pour gérer les connexions elles-mêmes.
* Une SignalR application n’utilise pas beaucoup plus de ressources de connexion qu’une application Web sans SignalR .

Pour ces raisons, nous vous recommandons d’utiliser le SignalR service Azure pour toutes les SignalR applications ASP.net Core hébergées sur Azure, y compris les app service, les machines virtuelles et les conteneurs.

Pour plus d’informations, consultez la [ SignalR documentation du service Azure](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Fond de panier Redis

[Redims](https://redis.io/) est un magasin de valeurs de clé en mémoire qui prend en charge un système de messagerie avec un modèle de publication/abonnement. Le SignalR fond de panier ReDim utilise la fonctionnalité Pub/Sub pour transférer des messages vers d’autres serveurs. Lorsqu’un client établit une connexion, les informations de connexion sont transmises au fond de panier. Lorsqu’un serveur souhaite envoyer un message à tous les clients, il envoie au fond de panier. Le fond de panier connaît tous les clients connectés et les serveurs sur lesquels ils se trouvent. Elle envoie le message à tous les clients par le biais de leurs serveurs respectifs. Ce processus est illustré dans le diagramme suivant :

![Le fond de panier ReDim, message envoyé d’un serveur à tous les clients](scale/_static/redis-backplane.png)

Le fond de panier ReDim est l’approche recommandée pour la montée en puissance parallèle pour les applications hébergées dans votre propre infrastructure. En cas de latence de connexion importante entre votre centre de données et un centre de données Azure, le SignalR service Azure peut ne pas être une option pratique pour les applications locales avec des exigences de faible latence ou de débit élevé.

Les SignalR avantages du service Azure mentionnés précédemment sont les inconvénients du fond de panier ReDim :

* Des sessions rémanentes, également appelées « [affinité du client](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)», sont nécessaires, sauf lorsque les **deux** conditions suivantes sont réunies :
  * Tous les clients sont configurés pour utiliser **uniquement** les WebSockets.
  * Le [paramètre SkipNegotiation](xref:signalr/configuration#configure-additional-options) est activé dans la configuration du client. 
   Une fois qu’une connexion est établie sur un serveur, la connexion doit rester sur ce serveur.
* Une SignalR application doit être montée en charge en fonction du nombre de clients, même si peu de messages sont envoyés.
* Une SignalR application utilise beaucoup plus de ressources de connexion qu’une application Web sans SignalR .

## <a name="iis-limitations-on-windows-client-os"></a>Limitations IIS sur le système d’exploitation client Windows

Windows 10 et Windows 8. x sont des systèmes d’exploitation clients. IIS sur les systèmes d’exploitation clients est limité à 10 connexions simultanées. SignalRles connexions de sont :

* Transitoire et fréquemment rétabli.
* **N’est pas** supprimé immédiatement lorsqu’il n’est plus utilisé.

Les conditions précédentes permettent d’atteindre la limite de 10 connexions sur un système d’exploitation client. Lorsqu’un système d’exploitation client est utilisé pour le développement, nous vous recommandons les opérations suivantes :

* Évitez les services Internet.
* Utilisez Kestrel ou IIS Express comme cibles de déploiement.

## <a name="linux-with-nginx"></a>Linux avec Nginx

L’exemple suivant contient les paramètres requis minimum pour activer WebSockets, ServerSentEvents et LongPolling pour SignalR :

```nginx
http {
  map $http_connection $connection_upgrade {
    "~*Upgrade" $http_connection;
    default keep-alive;
}

  server {
    listen 80;
    server_name example.com *.example.com;

    # Configure the SignalR Endpoint
    location /hubroute {
      # App server url
      proxy_pass http://localhost:5000;

      # Configuration for WebSockets
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_cache off;

      # Configuration for ServerSentEvents
      proxy_buffering off;

      # Configuration for LongPolling or if your KeepAliveInterval is longer than 60 seconds
      proxy_read_timeout 100s;

      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
```

Lorsque plusieurs serveurs principaux sont utilisés, des sessions rémanentes doivent être ajoutées pour empêcher les SignalR connexions de changer de serveur lors de la connexion. Il existe plusieurs façons d’ajouter des sessions rémanentes dans nginx. Deux approches sont présentées ci-dessous en fonction de ce que vous avez disponible.

Les éléments suivants sont ajoutés en plus de la configuration précédente. Dans les exemples suivants, `backend` est le nom du groupe de serveurs.

Avec [Nginx Open source](https://nginx.org/en/), utilisez `ip_hash` pour acheminer les connexions à un serveur en fonction de l’adresse IP du client :

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    ip_hash;
  }
}
```

Avec [Nginx plus](https://www.nginx.com/products/nginx), utilisez `sticky` pour ajouter un cookie à des demandes et épingler les demandes de l’utilisateur à un serveur :

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    sticky cookie srv_id expires=max domain=.example.com path=/ httponly;
  }
}
```

Enfin, remplacez `proxy_pass http://localhost:5000` la `server` section par `proxy_pass http://backend` .

Pour plus d’informations sur les WebSockets sur nginx, consultez [Nginx en tant que proxy WebSocket](https://www.nginx.com/blog/websocket-nginx).

Pour plus d’informations sur l’équilibrage de charge et les sessions rémanentes, consultez [équilibrage de charge Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).

Pour plus d’informations sur les ASP.NET Core avec nginx, consultez l’article suivant :
* <xref:host-and-deploy/linux-nginx>

## <a name="third-party-signalr-backplane-providers"></a>SignalRFournisseurs de fond de panier tiers

* [NCache](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* [Orléans](https://github.com/OrleansContrib/SignalR.Orleans)
* [Rebus](https://github.com/rebus-org/Rebus.SignalR)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [SignalRDocumentation du service Azure](/azure/azure-signalr/signalr-overview)
* [Configurer un backplane ReDim](xref:signalr/redis-backplane)
