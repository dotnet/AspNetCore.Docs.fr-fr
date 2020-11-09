---
title: 'ASP.NET Core SignalR des hôtes dans les services d’arrière-plan'
author: bradygaster
description: 'Découvrez comment envoyer des messages à SignalR des clients à partir de classes BackgroundService .net core.'
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/background-services
ms.openlocfilehash: 810eff7ccb08ecc22ea255bf0a9fe3d22637179f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060103"
---
# <a name="host-aspnet-core-no-locsignalr-in-background-services"></a><span data-ttu-id="be745-103">ASP.NET Core SignalR des hôtes dans les services d’arrière-plan</span><span class="sxs-lookup"><span data-stu-id="be745-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="be745-104">Par [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="be745-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="be745-105">Cet article fournit des conseils pour :</span><span class="sxs-lookup"><span data-stu-id="be745-105">This article provides guidance for:</span></span>

* <span data-ttu-id="be745-106">Hébergement de SignalR hubs à l’aide d’un processus de travail en arrière-plan hébergé avec ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="be745-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="be745-107">Envoi de messages à des clients connectés à partir d’un [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).net core.</span><span class="sxs-lookup"><span data-stu-id="be745-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="be745-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="be745-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="be745-109">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="be745-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end

## <a name="enable-no-locsignalr-in-startup"></a><span data-ttu-id="be745-110">Activer SignalR au démarrage</span><span class="sxs-lookup"><span data-stu-id="be745-110">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="be745-111">L’hébergement de ASP.NET Core SignalR hubs dans le contexte d’un processus de travail en arrière-plan est identique à l’hébergement d’un concentrateur dans une application web ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="be745-111">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="be745-112">Dans la `Startup.ConfigureServices` méthode, l’appel de `services.AddSignalR` ajoute les services requis à la couche d’injection de dépendances ASP.net Core (di) pour prendre en charge SignalR .</span><span class="sxs-lookup"><span data-stu-id="be745-112">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="be745-113">Dans `Startup.Configure` , la `MapHub` méthode est appelée dans le `UseEndpoints` rappel pour connecter les points de terminaison de concentrateur dans le pipeline de requête ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="be745-113">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="be745-114">L’hébergement de ASP.NET Core SignalR hubs dans le contexte d’un processus de travail en arrière-plan est identique à l’hébergement d’un concentrateur dans une application web ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="be745-114">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="be745-115">Dans la `Startup.ConfigureServices` méthode, l’appel de `services.AddSignalR` ajoute les services requis à la couche d’injection de dépendances ASP.net Core (di) pour prendre en charge SignalR .</span><span class="sxs-lookup"><span data-stu-id="be745-115">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="be745-116">Dans `Startup.Configure` , la `UseSignalR` méthode est appelée pour connecter le ou les points de terminaison de concentrateur dans le pipeline de requête ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="be745-116">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="be745-117">Dans l’exemple précédent, la `ClockHub` classe implémente la `Hub<T>` classe pour créer un concentrateur fortement typé.</span><span class="sxs-lookup"><span data-stu-id="be745-117">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="be745-118">`ClockHub`A été configuré dans la `Startup` classe pour répondre aux demandes au niveau du point de terminaison `/hubs/clock` .</span><span class="sxs-lookup"><span data-stu-id="be745-118">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="be745-119">Pour plus d’informations sur les hubs fortement typés, consultez [utiliser des hubs dans SignalR pour ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="be745-119">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="be745-120">Cette fonctionnalité n’est pas limitée à la classe de [concentrateur \<T> ](xref:Microsoft.AspNetCore.SignalR.Hub`1) .</span><span class="sxs-lookup"><span data-stu-id="be745-120">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="be745-121">Toutes les classes qui héritent de [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), telles que [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), fonctionnent.</span><span class="sxs-lookup"><span data-stu-id="be745-121">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), works.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

<span data-ttu-id="be745-122">L’interface utilisée par le fortement typé `ClockHub` est l' `IClock` interface.</span><span class="sxs-lookup"><span data-stu-id="be745-122">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-no-locsignalr-hub-from-a-background-service"></a><span data-ttu-id="be745-123">Appeler un SignalR Hub à partir d’un service d’arrière-plan</span><span class="sxs-lookup"><span data-stu-id="be745-123">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="be745-124">Au démarrage, la `Worker` classe, a `BackgroundService` est activée à l’aide de `AddHostedService` .</span><span class="sxs-lookup"><span data-stu-id="be745-124">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="be745-125">Étant donné que SignalR est également activé pendant la `Startup` phase, dans lequel chaque concentrateur est attaché à un point de terminaison individuel dans le pipeline de requête HTTP de ASP.net Core, chaque concentrateur est représenté par un `IHubContext<T>` sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="be745-125">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="be745-126">À l’aide des fonctionnalités DI de ASP.NET Core, les autres classes instanciées par la couche d’hébergement, comme les `BackgroundService` classes, les classes de contrôleur MVC ou les Razor modèles de page, peuvent recevoir des références aux hubs côté serveur en acceptant des instances de lors de la `IHubContext<ClockHub, IClock>` construction.</span><span class="sxs-lookup"><span data-stu-id="be745-126">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

<span data-ttu-id="be745-127">Étant donné que la `ExecuteAsync` méthode est appelée de manière itérative dans le service d’arrière-plan, la date et l’heure actuelles du serveur sont envoyées aux clients connectés à l’aide de `ClockHub` .</span><span class="sxs-lookup"><span data-stu-id="be745-127">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-no-locsignalr-events-with-background-services"></a><span data-ttu-id="be745-128">Réagir aux SignalR événements avec les services d’arrière-plan</span><span class="sxs-lookup"><span data-stu-id="be745-128">React to SignalR events with background services</span></span>

<span data-ttu-id="be745-129">À l’instar d’une application à page unique utilisant le client JavaScript pour SignalR ou une application de bureau .net, vous pouvez utiliser le à l’aide du <xref:signalr/dotnet-client> , une `BackgroundService` `IHostedService` implémentation ou peut également être utilisée pour se connecter aux SignalR hubs et répondre aux événements.</span><span class="sxs-lookup"><span data-stu-id="be745-129">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="be745-130">La `ClockHubClient` classe implémente à la fois l' `IClock` interface et l' `IHostedService` interface.</span><span class="sxs-lookup"><span data-stu-id="be745-130">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="be745-131">De cette façon, il peut être activé lors `Startup` de l’exécution en continu et répondre aux événements de concentrateur à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="be745-131">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="be745-132">Pendant l’initialisation, `ClockHubClient` crée une instance de `HubConnection` et active la `IClock.ShowTime` méthode en tant que gestionnaire de l’événement du concentrateur `ShowTime` .</span><span class="sxs-lookup"><span data-stu-id="be745-132">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="be745-133">Dans l' `IHostedService.StartAsync` implémentation, le `HubConnection` est démarré de manière asynchrone.</span><span class="sxs-lookup"><span data-stu-id="be745-133">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="be745-134">Pendant la `IHostedService.StopAsync` méthode, le `HubConnection` est supprimé de manière asynchrone.</span><span class="sxs-lookup"><span data-stu-id="be745-134">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="be745-135">Dans l' `IHostedService.StartAsync` implémentation, le `HubConnection` est démarré de manière asynchrone.</span><span class="sxs-lookup"><span data-stu-id="be745-135">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="be745-136">Pendant la `IHostedService.StopAsync` méthode, le `HubConnection` est supprimé de manière asynchrone.</span><span class="sxs-lookup"><span data-stu-id="be745-136">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="be745-137">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="be745-137">Additional resources</span></span>

* [<span data-ttu-id="be745-138">Prise en main</span><span class="sxs-lookup"><span data-stu-id="be745-138">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="be745-139">Hubs</span><span class="sxs-lookup"><span data-stu-id="be745-139">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="be745-140">Publication dans Azure</span><span class="sxs-lookup"><span data-stu-id="be745-140">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="be745-141">Hubs fortement typés</span><span class="sxs-lookup"><span data-stu-id="be745-141">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
