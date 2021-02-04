---
title: ASP.NET Core SignalR des hôtes dans les services d’arrière-plan
author: bradygaster
description: Découvrez comment envoyer des messages à SignalR des clients à partir de classes BackgroundService .net core.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/background-services
ms.openlocfilehash: 4b877c64a881fec15ac9e9bd74ffdde0b5fa60f9
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530175"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>ASP.NET Core SignalR des hôtes dans les services d’arrière-plan

Par [Brady Gaster](https://twitter.com/bradygaster)

Cet article fournit des conseils pour :

* Hébergement de SignalR hubs à l’aide d’un processus de travail en arrière-plan hébergé avec ASP.net core.
* Envoi de messages à des clients connectés à partir d’un [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).net core.

::: moniker range=">= aspnetcore-3.0"

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)

::: moniker-end

## <a name="enable-signalr-in-startup"></a>Activer SignalR au démarrage

::: moniker range=">= aspnetcore-3.0"

L’hébergement de ASP.NET Core SignalR hubs dans le contexte d’un processus de travail en arrière-plan est identique à l’hébergement d’un concentrateur dans une application web ASP.net core. Dans la `Startup.ConfigureServices` méthode, l’appel de `services.AddSignalR` ajoute les services requis à la couche d’injection de dépendances ASP.net Core (di) pour prendre en charge SignalR . Dans `Startup.Configure` , la `MapHub` méthode est appelée dans le `UseEndpoints` rappel pour connecter les points de terminaison de concentrateur dans le pipeline de requête ASP.net core.

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

L’hébergement de ASP.NET Core SignalR hubs dans le contexte d’un processus de travail en arrière-plan est identique à l’hébergement d’un concentrateur dans une application web ASP.net core. Dans la `Startup.ConfigureServices` méthode, l’appel de `services.AddSignalR` ajoute les services requis à la couche d’injection de dépendances ASP.net Core (di) pour prendre en charge SignalR . Dans `Startup.Configure` , la `UseSignalR` méthode est appelée pour connecter le ou les points de terminaison de concentrateur dans le pipeline de requête ASP.net core.

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

Dans l’exemple précédent, la `ClockHub` classe implémente la `Hub<T>` classe pour créer un concentrateur fortement typé. `ClockHub`A été configuré dans la `Startup` classe pour répondre aux demandes au niveau du point de terminaison `/hubs/clock` .

Pour plus d’informations sur les hubs fortement typés, consultez [utiliser des hubs dans SignalR pour ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Cette fonctionnalité n’est pas limitée à la classe de [concentrateur \<T> ](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Toutes les classes qui héritent de [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), telles que [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), fonctionnent.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

L’interface utilisée par le fortement typé `ClockHub` est l' `IClock` interface.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a>Appeler un SignalR Hub à partir d’un service d’arrière-plan

Au démarrage, la `Worker` classe, a `BackgroundService` est activée à l’aide de `AddHostedService` .

```csharp
services.AddHostedService<Worker>();
```

Étant donné que SignalR est également activé pendant la `Startup` phase, dans lequel chaque concentrateur est attaché à un point de terminaison individuel dans le pipeline de requête HTTP de ASP.net Core, chaque concentrateur est représenté par un `IHubContext<T>` sur le serveur. À l’aide des fonctionnalités DI de ASP.NET Core, les autres classes instanciées par la couche d’hébergement, comme les `BackgroundService` classes, les classes de contrôleur MVC ou les Razor modèles de page, peuvent recevoir des références aux hubs côté serveur en acceptant des instances de lors de la `IHubContext<ClockHub, IClock>` construction.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

Étant donné que la `ExecuteAsync` méthode est appelée de manière itérative dans le service d’arrière-plan, la date et l’heure actuelles du serveur sont envoyées aux clients connectés à l’aide de `ClockHub` .

## <a name="react-to-signalr-events-with-background-services"></a>Réagir aux SignalR événements avec les services d’arrière-plan

Comme une application à page unique utilisant le client JavaScript pour SignalR , ou une application de bureau .net utilisant le <xref:signalr/dotnet-client> , une `BackgroundService` `IHostedService` implémentation ou peut également être utilisée pour se connecter aux SignalR hubs et répondre aux événements.

La `ClockHubClient` classe implémente à la fois l' `IClock` interface et l' `IHostedService` interface. De cette façon, il peut être activé lors `Startup` de l’exécution en continu et répondre aux événements de concentrateur à partir du serveur.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Pendant l’initialisation, `ClockHubClient` crée une instance de `HubConnection` et active la `IClock.ShowTime` méthode en tant que gestionnaire de l’événement du concentrateur `ShowTime` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

Dans l' `IHostedService.StartAsync` implémentation, le `HubConnection` est démarré de manière asynchrone.

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Pendant la `IHostedService.StopAsync` méthode, le `HubConnection` est supprimé de manière asynchrone.

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

Dans l' `IHostedService.StartAsync` implémentation, le `HubConnection` est démarré de manière asynchrone.

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Pendant la `IHostedService.StopAsync` méthode, le `HubConnection` est supprimé de manière asynchrone.

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* [Prise en main](xref:tutorials/signalr)
* [Concentr](xref:signalr/hubs)
* [Publication dans Azure](xref:signalr/publish-to-azure-web-app)
* [Hubs fortement typés](xref:signalr/hubs#strongly-typed-hubs)
