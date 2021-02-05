---
title: Héberger et déployer des ASP.NET Core Blazor Server
author: guardrex
description: Découvrez comment héberger et déployer une Blazor Server application à l’aide de ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: a209109210ef5e335734a974ceb0c2af7cb8e1a1
ms.sourcegitcommit: c1839f2992b003c92cd958244a2e0771ae928786
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "94595439"
---
# <a name="host-and-deploy-blazor-server"></a>Héberger et déployer Blazor Server

Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valeurs de configuration de l’hôte

les [ Blazor Server applications](xref:blazor/hosting-models#blazor-server) peuvent accepter des [valeurs de configuration d’hôte génériques](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Déploiement

À l’aide du [ Blazor Server modèle d’hébergement](xref:blazor/hosting-models#blazor-server), Blazor est exécuté sur le serveur à partir d’une application ASP.net core. Les mises à jour de l’interface utilisateur, la gestion des événements et les appels JavaScript sont gérés sur une [SignalR](xref:signalr/introduction) connexion.

Un serveur web capable d’héberger une application ASP.NET Core est nécessaire. Visual Studio comprend le modèle de projet d' **Blazor Server application** ( `blazorserverside` modèle lors de l’utilisation de la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande).

## <a name="scalability"></a>Scalabilité

Planifiez un déploiement pour tirer le meilleur parti de l’infrastructure disponible pour une Blazor Server application. Consultez les ressources suivantes pour résoudre l' Blazor Server évolutivité des applications :

* [Notions de base des Blazor Server applications](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a>Serveur de déploiement

Lorsque vous envisagez l’évolutivité d’un serveur unique (montée en puissance), la mémoire disponible pour une application est probablement la première ressource que l’application épuisera en fonction des demandes des utilisateurs. La mémoire disponible sur le serveur affecte les éléments suivants :

* Nombre de circuits actifs qu’un serveur peut prendre en charge.
* Latence de l’interface utilisateur sur le client.

Pour obtenir des conseils sur la création d’applications serveur sécurisées et évolutives Blazor , consultez <xref:blazor/security/server/threat-mitigation> .

Chaque circuit utilise environ 250 Ko de mémoire pour une application de type *Hello World* minimale. La taille d’un circuit dépend du code de l’application et des exigences de maintenance d’état associées à chaque composant. Nous vous recommandons de mesurer les demandes de ressources pendant le développement de votre application et de votre infrastructure, mais la ligne de base suivante peut être un point de départ pour la planification de votre cible de déploiement : Si vous pensez que votre application prend en charge 5 000 utilisateurs simultanés, envisagez de budgétiser au moins 1,3 Go de mémoire serveur vers l’application (ou ~ 273 Ko par utilisateur)

### <a name="signalr-configuration"></a>SignalR configuré

Blazor Server les applications utilisent ASP.NET Core SignalR pour communiquer avec le navigateur. [ SignalR les conditions d’hébergement et de mise à l’échelle de](xref:signalr/publish-to-azure-web-app) s’appliquent aux Blazor Server applications.

Blazor fonctionne mieux lorsque vous utilisez WebSocket en tant que SignalR transport en raison d’une latence, d’une fiabilité et d’une [sécurité](xref:signalr/security)moindres. L’interrogation longue est utilisée par SignalR lorsque WebSocket n’est pas disponible ou lorsque l’application est configurée explicitement pour utiliser une interrogation longue. Lors du déploiement sur Azure App Service, configurez l’application pour qu’elle utilise WebSockets dans les paramètres Portail Azure pour le service. Pour plus d’informations sur la configuration de l’application pour Azure App Service, consultez les [ SignalR instructions de publication](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-signalr-service"></a>SignalRService Azure

Nous vous recommandons d’utiliser le [ SignalR service Azure](xref:signalr/scale#azure-signalr-service) pour les Blazor Server applications. Le service permet la mise à l’échelle d’une Blazor Server application vers un grand nombre de connexions simultanées SignalR . En outre, la SignalR portée mondiale et les centres de données haute performance du service contribuent de manière significative à réduire la latence en raison de la géographie.

> [!IMPORTANT]
> Lorsque les [WebSockets](https://wikipedia.org/wiki/WebSocket) sont désactivés, Azure App service simule une connexion en temps réel à l’aide d’une longue interrogation http. L’interrogation HTTP longue est sensiblement plus lente que l’exécution avec WebSockets activé, qui n’utilise pas l’interrogation pour simuler une connexion client-serveur.
>
> Nous vous recommandons d’utiliser WebSockets pour les Blazor Server applications déployées sur Azure App service. Par défaut, le [ SignalR service Azure](xref:signalr/scale#azure-signalr-service) utilise WebSocket. Si l’application n’utilise pas le SignalR service Azure, consultez <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> .
>
> Pour plus d’informations, consultez :
>
> * [Qu’est-ce que le SignalR service Azure ?](/azure/azure-signalr/signalr-overview)
> * [Guide des performances pour le SignalR service Azure](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a>Configuration

Pour configurer une application pour le SignalR service Azure, l’application doit prendre en charge les *sessions rémanentes*, où les clients sont [redirigés vers le même serveur lors du prérendu](xref:blazor/hosting-models#connection-to-the-server). L' `ServerStickyMode` option ou la valeur de configuration est définie sur `Required` . En règle générale, une application crée la configuration à l’aide de l' **_une_** des approches suivantes :

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Configuration (utilisez l' **_une_** des approches suivantes) :
  
     * Dans `appsettings.json` :

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * Les paramètres d’application de **configuration** de l’app service  >   dans le portail Azure (**nom**: `Azure__SignalR__StickyServerMode` , **valeur**: `Required` ). Cette approche est adoptée automatiquement pour l’application si vous [approvisionnez le SignalR service Azure](#provision-the-azure-signalr-service).

### <a name="provision-the-azure-signalr-service"></a>Approvisionner le SignalR service Azure

Pour approvisionner le SignalR service Azure pour une application dans Visual Studio :

1. Créez un profil de publication Azure Apps dans Visual Studio pour l' Blazor Server application.
1. Ajoutez la dépendance du **SignalR service Azure** au profil. Si l’abonnement Azure n’a pas d’instance de service Azure préexistante SignalR à attribuer à l’application, sélectionnez **créer une nouvelle instance de SignalR service Azure** pour approvisionner une nouvelle instance de service.
1. Publiez l’application dans Azure.

L’approvisionnement du service Azure SignalR dans Visual Studio active automatiquement [les *sessions rémanentes*](#configuration) et ajoute SignalR la chaîne de connexion à la configuration de l’app service.

#### <a name="iis"></a>IIS

Lorsque vous utilisez IIS, activez :

* [WebSocket sur IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Sessions rémanentes avec application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Créez une définition d’entrée avec les [Annotations Kubernetes suivantes pour les sessions rémanentes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a>Linux avec Nginx

Pour que SignalR WebSocket fonctionne correctement, vérifiez que les `Upgrade` `Connection` en-têtes et du proxy sont définis sur les valeurs suivantes et qu’ils sont `$connection_upgrade` mappés à l’un ou l’autre des éléments suivants :

* Valeur d’en-tête de mise à niveau par défaut.
* `close` Lorsque l’en-tête de mise à niveau est manquant ou vide.

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

Pour plus d’informations, consultez les articles suivants :

* [NGINX en tant que proxy WebSocket](https://www.nginx.com/blog/websocket-nginx/)
* [Proxy WebSocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a>Linux avec Apache

Pour héberger une Blazor application derrière Apache sur Linux, configurez `ProxyPass` pour le trafic HTTP et WebSocket.

Dans l’exemple suivant :

* Le serveur Kestrel est en cours d’exécution sur l’ordinateur hôte.
* L’application écoute le trafic sur le port 5000.

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

Activez les modules suivants :

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

Consultez la console du navigateur pour les erreurs WebSocket. Exemples d’erreurs :

* Firefox ne peut pas établir de connexion au serveur sur ws://the-domain-name.tld/_blazor ?id=XXX.
* Erreur : échec du démarrage du transport’WebSockets' : erreur : une erreur s’est produite avec le transport.
* Erreur : impossible de démarrer le transport’LongPolling' : TypeError : This. transport n’est pas défini
* Erreur : impossible de se connecter au serveur avec l’un des transports disponibles. Échec de WebSocket
* Erreur : impossible d’envoyer des données si la connexion n’est pas dans l’état « connecté ».

Pour plus d’informations, consultez la [documentation Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).

### <a name="measure-network-latency"></a>Mesurer la latence du réseau

L' [interopérabilité js](xref:blazor/call-javascript-from-dotnet) peut être utilisée pour mesurer la latence du réseau, comme le montre l’exemple suivant :

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code {
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            startTime = DateTime.UtcNow;
            var _ = await JS.InvokeAsync<string>("toString");
            latency = DateTime.UtcNow - startTime;
            StateHasChanged();
        }
    }
}
```

Pour une expérience d’interface utilisateur raisonnable, nous recommandons une latence d’interface utilisateur soutenue de 250 ms ou moins.
