---
title: Configuration de ASP.NET Core SignalR
author: bradygaster
description: Découvrez comment configurer des SignalR applications ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
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
uid: signalr/configuration
ms.openlocfilehash: 8851246dbaa076af1fdbc4e5e4f1ada0e4e3988a
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326592"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="0cf9c-103">Configuration de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="0cf9c-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0cf9c-104">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="0cf9c-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0cf9c-105">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0cf9c-106">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0cf9c-107">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="0cf9c-108">`AddJsonProtocol`peut être ajouté après [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0cf9c-109">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0cf9c-110">La propriété [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur cet objet est un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objet qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0cf9c-111">Pour plus d’informations, consultez la [System.Text.Jsdans la documentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="0cf9c-112">Par exemple, pour configurer le sérialiseur de manière à ce qu’il ne change pas la casse des noms de propriétés, au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="0cf9c-113">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0cf9c-114">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="0cf9c-115">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="0cf9c-116">Basculer vers Newtonsoft.Jssur</span><span class="sxs-lookup"><span data-stu-id="0cf9c-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="0cf9c-117">Si vous avez besoin de fonctionnalités de `Newtonsoft.Json` qui ne sont pas prises en charge dans `System.Text.Json` , consultez [basculer vers Newtonsoft.Jssur](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0cf9c-118">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="0cf9c-118">MessagePack serialization options</span></span>

<span data-ttu-id="0cf9c-119">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0cf9c-120">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0cf9c-121">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0cf9c-122">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="0cf9c-122">Configure server options</span></span>

<span data-ttu-id="0cf9c-123">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0cf9c-124">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-124">Option</span></span> | <span data-ttu-id="0cf9c-125">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-125">Default Value</span></span> | <span data-ttu-id="0cf9c-126">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="0cf9c-127">30 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-127">30 seconds</span></span> | <span data-ttu-id="0cf9c-128">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="0cf9c-129">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="0cf9c-130">La valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="0cf9c-131">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-131">15 seconds</span></span> | <span data-ttu-id="0cf9c-132">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0cf9c-133">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-134">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0cf9c-135">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-135">15 seconds</span></span> | <span data-ttu-id="0cf9c-136">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0cf9c-137">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0cf9c-138">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0cf9c-139">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="0cf9c-139">All installed protocols</span></span> | <span data-ttu-id="0cf9c-140">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-140">Protocols supported by this hub.</span></span> <span data-ttu-id="0cf9c-141">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0cf9c-142">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0cf9c-143">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="0cf9c-144">Nombre maximal d’éléments pouvant être mis en mémoire tampon pour les flux de téléchargement du client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="0cf9c-145">Si cette limite est atteinte, le traitement des appels est bloqué jusqu’à ce que le serveur traite les éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="0cf9c-146">32 Ko</span><span class="sxs-lookup"><span data-stu-id="0cf9c-146">32 KB</span></span> | <span data-ttu-id="0cf9c-147">Taille maximale d’un seul message de concentrateur entrant.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="0cf9c-148">1</span><span class="sxs-lookup"><span data-stu-id="0cf9c-148">1</span></span> | <span data-ttu-id="0cf9c-149">Nombre maximal de méthodes de concentrateur que chaque client peut appeler en parallèle avant d’être mis en file d’attente.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="0cf9c-150">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="0cf9c-151">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0cf9c-152">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="0cf9c-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0cf9c-153">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0cf9c-154">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="0cf9c-155">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0cf9c-156">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-156">Option</span></span> | <span data-ttu-id="0cf9c-157">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-157">Default Value</span></span> | <span data-ttu-id="0cf9c-158">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0cf9c-159">32 Ko</span><span class="sxs-lookup"><span data-stu-id="0cf9c-159">32 KB</span></span> | <span data-ttu-id="0cf9c-160">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon avant d’appliquer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="0cf9c-161">L’augmentation de cette valeur permet au serveur de recevoir plus rapidement des messages plus volumineux sans appliquer la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0cf9c-162">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0cf9c-163">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0cf9c-164">32 Ko</span><span class="sxs-lookup"><span data-stu-id="0cf9c-164">32 KB</span></span> | <span data-ttu-id="0cf9c-165">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon avant d’observer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="0cf9c-166">L’augmentation de cette valeur permet au serveur de mettre plus rapidement en mémoire tampon des messages plus volumineux sans attendre la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0cf9c-167">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-167">All Transports are enabled.</span></span> | <span data-ttu-id="0cf9c-168">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0cf9c-169">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-169">See below.</span></span> | <span data-ttu-id="0cf9c-170">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0cf9c-171">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-171">See below.</span></span> | <span data-ttu-id="0cf9c-172">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="0cf9c-173">0</span><span class="sxs-lookup"><span data-stu-id="0cf9c-173">0</span></span> | <span data-ttu-id="0cf9c-174">Spécifiez la version minimale du protocole Negotiate.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="0cf9c-175">Cela permet de limiter les clients aux versions plus récentes.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="0cf9c-176">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0cf9c-177">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-177">Option</span></span> | <span data-ttu-id="0cf9c-178">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-178">Default Value</span></span> | <span data-ttu-id="0cf9c-179">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0cf9c-180">90 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-180">90 seconds</span></span> | <span data-ttu-id="0cf9c-181">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0cf9c-182">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0cf9c-183">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0cf9c-184">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-184">Option</span></span> | <span data-ttu-id="0cf9c-185">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-185">Default Value</span></span> | <span data-ttu-id="0cf9c-186">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0cf9c-187">5 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-187">5 seconds</span></span> | <span data-ttu-id="0cf9c-188">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0cf9c-189">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0cf9c-190">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0cf9c-191">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="0cf9c-191">Configure client options</span></span>

<span data-ttu-id="0cf9c-192">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0cf9c-193">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0cf9c-194">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="0cf9c-194">Configure logging</span></span>

<span data-ttu-id="0cf9c-195">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0cf9c-196">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0cf9c-197">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0cf9c-198">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0cf9c-199">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0cf9c-200">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0cf9c-201">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0cf9c-202">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0cf9c-203">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0cf9c-204">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="0cf9c-205">Au lieu d’une `LogLevel` valeur, vous pouvez également fournir une `string` valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="0cf9c-206">Cela est utile lors SignalR de la configuration de la journalisation dans les environnements où vous n’avez pas accès aux `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="0cf9c-207">Le tableau suivant répertorie les niveaux de journalisation disponibles.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-207">The following table lists the available log levels.</span></span> <span data-ttu-id="0cf9c-208">La valeur que vous fournissez pour `configureLogging` définir le niveau de journalisation **minimale** qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="0cf9c-209">Les messages enregistrés à ce niveau, **ou les niveaux indiqués après celui-ci dans la table**, sont enregistrés.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="0cf9c-210">String</span><span class="sxs-lookup"><span data-stu-id="0cf9c-210">String</span></span>                      | <span data-ttu-id="0cf9c-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="0cf9c-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="0cf9c-212">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="0cf9c-213">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="0cf9c-214">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0cf9c-215">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0cf9c-216">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0cf9c-217">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0cf9c-218">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0cf9c-219">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0cf9c-220">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0cf9c-221">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="0cf9c-221">Configure allowed transports</span></span>

<span data-ttu-id="0cf9c-222">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0cf9c-223">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0cf9c-224">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-224">All transports are enabled by default.</span></span>

<span data-ttu-id="0cf9c-225">Par exemple, pour désactiver le transport des événements Server-Sent, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0cf9c-226">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="0cf9c-227">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="0cf9c-228">Dans le client Java, le transport est sélectionné à l’aide de la `withTransport` méthode sur le `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="0cf9c-229">Le client Java utilise le transport WebSocket par défaut.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0cf9c-230">Le SignalR client Java ne prend pas encore en charge le secours pour le transport.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0cf9c-231">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="0cf9c-231">Configure bearer authentication</span></span>

<span data-ttu-id="0cf9c-232">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0cf9c-233">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0cf9c-234">Dans le client JavaScript, le jeton d’accès est utilisé en tant que jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (plus précisément, dans Server-Sent les événements et les requêtes WebSocket).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0cf9c-235">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0cf9c-236">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0cf9c-237">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0cf9c-238">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0cf9c-239">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0cf9c-240">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0cf9c-241">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="0cf9c-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0cf9c-242">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0cf9c-243">.NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0cf9c-244">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-244">Option</span></span> | <span data-ttu-id="0cf9c-245">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-245">Default value</span></span> | <span data-ttu-id="0cf9c-246">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0cf9c-247">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-248">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-248">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-249">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0cf9c-250">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-251">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0cf9c-252">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-252">15 seconds</span></span> | <span data-ttu-id="0cf9c-253">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="0cf9c-254">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0cf9c-255">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-256">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0cf9c-257">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-257">15 seconds</span></span> | <span data-ttu-id="0cf9c-258">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0cf9c-259">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0cf9c-260">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="0cf9c-261">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0cf9c-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0cf9c-263">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-263">Option</span></span> | <span data-ttu-id="0cf9c-264">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-264">Default value</span></span> | <span data-ttu-id="0cf9c-265">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0cf9c-266">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-267">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-267">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-268">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0cf9c-269">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-270">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="0cf9c-271">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-272">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0cf9c-273">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0cf9c-274">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0cf9c-275">Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-275">Java</span></span>](#tab/java)

| <span data-ttu-id="0cf9c-276">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-276">Option</span></span> | <span data-ttu-id="0cf9c-277">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-277">Default value</span></span> | <span data-ttu-id="0cf9c-278">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0cf9c-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0cf9c-280">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-281">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-281">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-282">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0cf9c-283">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-284">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0cf9c-285">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-285">15 seconds</span></span> | <span data-ttu-id="0cf9c-286">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="0cf9c-287">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0cf9c-288">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-289">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="0cf9c-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="0cf9c-291">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-292">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0cf9c-293">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0cf9c-294">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0cf9c-295">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0cf9c-295">Configure additional options</span></span>

<span data-ttu-id="0cf9c-296">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0cf9c-297">.NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0cf9c-298">Option .NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-298">.NET Option</span></span> |  <span data-ttu-id="0cf9c-299">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-299">Default value</span></span> | <span data-ttu-id="0cf9c-300">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0cf9c-301">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0cf9c-302">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-303">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-304">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0cf9c-305">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-305">Empty</span></span> | <span data-ttu-id="0cf9c-306">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0cf9c-307">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-307">Empty</span></span> | <span data-ttu-id="0cf9c-308">Collection de HTTP cookie s à envoyer avec chaque requête http.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0cf9c-309">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-309">Empty</span></span> | <span data-ttu-id="0cf9c-310">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0cf9c-311">5 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-311">5 seconds</span></span> | <span data-ttu-id="0cf9c-312">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-312">WebSockets only.</span></span> <span data-ttu-id="0cf9c-313">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0cf9c-314">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0cf9c-315">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-315">Empty</span></span> | <span data-ttu-id="0cf9c-316">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0cf9c-317">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0cf9c-318">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="0cf9c-319">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0cf9c-320">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0cf9c-321">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que Cookie s et en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="0cf9c-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0cf9c-322">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0cf9c-323">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0cf9c-324">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0cf9c-325">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0cf9c-326">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0cf9c-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0cf9c-328">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-328">JavaScript Option</span></span> | <span data-ttu-id="0cf9c-329">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-329">Default Value</span></span> | <span data-ttu-id="0cf9c-330">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0cf9c-331">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="0cf9c-332"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valeur spécifiant le transport à utiliser pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="0cf9c-333">Dictionnaire d’en-têtes envoyés avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="0cf9c-334">L’envoi d’en-têtes dans le navigateur ne fonctionne pas pour les WebSockets ou le <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> flux.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="0cf9c-335">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0cf9c-336">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-337">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-337">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-338">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-338">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="0cf9c-339">Spécifie si les informations d’identification seront envoyées avec la demande CORS.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="0cf9c-340">Azure App Service utilise cookie les s pour les sessions rémanentes et a besoin que cette option soit activée pour fonctionner correctement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-340">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="0cf9c-341">Pour plus d’informations sur CORS avec SignalR , consultez <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-341">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0cf9c-342">Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-342">Java</span></span>](#tab/java)

| <span data-ttu-id="0cf9c-343">Option Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-343">Java Option</span></span> | <span data-ttu-id="0cf9c-344">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-344">Default Value</span></span> | <span data-ttu-id="0cf9c-345">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0cf9c-346">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0cf9c-347">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-348">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-348">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-349">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-349">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0cf9c-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0cf9c-351">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-351">Empty</span></span> | <span data-ttu-id="0cf9c-352">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0cf9c-353">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0cf9c-354">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="0cf9c-355">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0cf9c-356">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0cf9c-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0cf9c-357">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="0cf9c-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0cf9c-358">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-358">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0cf9c-359">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0cf9c-360">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="0cf9c-361">`AddJsonProtocol`peut être ajouté après [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-361">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0cf9c-362">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0cf9c-363">La propriété [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur cet objet est un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objet qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0cf9c-364">Pour plus d’informations, consultez la [System.Text.Jsdans la documentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="0cf9c-365">Par exemple, pour configurer le sérialiseur de manière à ce qu’il ne change pas la casse des noms de propriétés, au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="0cf9c-366">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0cf9c-367">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="0cf9c-368">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="0cf9c-369">Basculer vers Newtonsoft.Jssur</span><span class="sxs-lookup"><span data-stu-id="0cf9c-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="0cf9c-370">Si vous avez besoin de fonctionnalités de `Newtonsoft.Json` qui ne sont pas prises en charge dans `System.Text.Json` , consultez [basculer vers Newtonsoft.Jssur](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0cf9c-371">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="0cf9c-371">MessagePack serialization options</span></span>

<span data-ttu-id="0cf9c-372">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0cf9c-373">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-373">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0cf9c-374">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0cf9c-375">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="0cf9c-375">Configure server options</span></span>

<span data-ttu-id="0cf9c-376">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-376">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0cf9c-377">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-377">Option</span></span> | <span data-ttu-id="0cf9c-378">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-378">Default Value</span></span> | <span data-ttu-id="0cf9c-379">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="0cf9c-380">30 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-380">30 seconds</span></span> | <span data-ttu-id="0cf9c-381">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="0cf9c-382">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="0cf9c-383">La valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="0cf9c-384">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-384">15 seconds</span></span> | <span data-ttu-id="0cf9c-385">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0cf9c-386">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-387">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-387">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0cf9c-388">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-388">15 seconds</span></span> | <span data-ttu-id="0cf9c-389">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0cf9c-390">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0cf9c-391">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0cf9c-392">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="0cf9c-392">All installed protocols</span></span> | <span data-ttu-id="0cf9c-393">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-393">Protocols supported by this hub.</span></span> <span data-ttu-id="0cf9c-394">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0cf9c-395">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0cf9c-396">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="0cf9c-397">Nombre maximal d’éléments pouvant être mis en mémoire tampon pour les flux de téléchargement du client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="0cf9c-398">Si cette limite est atteinte, le traitement des appels est bloqué jusqu’à ce que le serveur traite les éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="0cf9c-399">32 Ko</span><span class="sxs-lookup"><span data-stu-id="0cf9c-399">32 KB</span></span> | <span data-ttu-id="0cf9c-400">Taille maximale d’un seul message de concentrateur entrant.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="0cf9c-401">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-401">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="0cf9c-402">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-402">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0cf9c-403">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="0cf9c-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0cf9c-404">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0cf9c-405">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="0cf9c-406">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-406">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0cf9c-407">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-407">Option</span></span> | <span data-ttu-id="0cf9c-408">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-408">Default Value</span></span> | <span data-ttu-id="0cf9c-409">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0cf9c-410">32 Ko</span><span class="sxs-lookup"><span data-stu-id="0cf9c-410">32 KB</span></span> | <span data-ttu-id="0cf9c-411">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon avant d’appliquer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="0cf9c-412">L’augmentation de cette valeur permet au serveur de recevoir plus rapidement des messages plus volumineux sans appliquer la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0cf9c-413">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0cf9c-414">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0cf9c-415">32 Ko</span><span class="sxs-lookup"><span data-stu-id="0cf9c-415">32 KB</span></span> | <span data-ttu-id="0cf9c-416">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon avant d’observer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="0cf9c-417">L’augmentation de cette valeur permet au serveur de mettre plus rapidement en mémoire tampon des messages plus volumineux sans attendre la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0cf9c-418">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-418">All Transports are enabled.</span></span> | <span data-ttu-id="0cf9c-419">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0cf9c-420">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-420">See below.</span></span> | <span data-ttu-id="0cf9c-421">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0cf9c-422">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-422">See below.</span></span> | <span data-ttu-id="0cf9c-423">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="0cf9c-424">0</span><span class="sxs-lookup"><span data-stu-id="0cf9c-424">0</span></span> | <span data-ttu-id="0cf9c-425">Spécifiez la version minimale du protocole Negotiate.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="0cf9c-426">Cela permet de limiter les clients aux versions plus récentes.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="0cf9c-427">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0cf9c-428">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-428">Option</span></span> | <span data-ttu-id="0cf9c-429">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-429">Default Value</span></span> | <span data-ttu-id="0cf9c-430">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0cf9c-431">90 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-431">90 seconds</span></span> | <span data-ttu-id="0cf9c-432">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0cf9c-433">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0cf9c-434">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0cf9c-435">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-435">Option</span></span> | <span data-ttu-id="0cf9c-436">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-436">Default Value</span></span> | <span data-ttu-id="0cf9c-437">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0cf9c-438">5 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-438">5 seconds</span></span> | <span data-ttu-id="0cf9c-439">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0cf9c-440">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0cf9c-441">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0cf9c-442">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="0cf9c-442">Configure client options</span></span>

<span data-ttu-id="0cf9c-443">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0cf9c-444">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0cf9c-445">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="0cf9c-445">Configure logging</span></span>

<span data-ttu-id="0cf9c-446">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0cf9c-447">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0cf9c-448">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0cf9c-449">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0cf9c-450">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0cf9c-451">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0cf9c-452">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0cf9c-453">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0cf9c-454">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0cf9c-455">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="0cf9c-456">Au lieu d’une `LogLevel` valeur, vous pouvez également fournir une `string` valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="0cf9c-457">Cela est utile lors SignalR de la configuration de la journalisation dans les environnements où vous n’avez pas accès aux `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-457">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="0cf9c-458">Le tableau suivant répertorie les niveaux de journalisation disponibles.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-458">The following table lists the available log levels.</span></span> <span data-ttu-id="0cf9c-459">La valeur que vous fournissez pour `configureLogging` définir le niveau de journalisation **minimale** qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="0cf9c-460">Les messages enregistrés à ce niveau, **ou les niveaux indiqués après celui-ci dans la table**, sont enregistrés.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-460">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="0cf9c-461">String</span><span class="sxs-lookup"><span data-stu-id="0cf9c-461">String</span></span>                      | <span data-ttu-id="0cf9c-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="0cf9c-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="0cf9c-463">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="0cf9c-464">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="0cf9c-465">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0cf9c-466">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-466">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0cf9c-467">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-467">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0cf9c-468">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0cf9c-469">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-469">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0cf9c-470">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0cf9c-471">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0cf9c-472">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="0cf9c-472">Configure allowed transports</span></span>

<span data-ttu-id="0cf9c-473">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-473">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0cf9c-474">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0cf9c-475">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-475">All transports are enabled by default.</span></span>

<span data-ttu-id="0cf9c-476">Par exemple, pour désactiver le transport des événements Server-Sent, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0cf9c-477">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="0cf9c-478">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="0cf9c-479">Dans le client Java, le transport est sélectionné à l’aide de la `withTransport` méthode sur le `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="0cf9c-480">Le client Java utilise le transport WebSocket par défaut.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0cf9c-481">Le SignalR client Java ne prend pas encore en charge le secours pour le transport.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-481">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0cf9c-482">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="0cf9c-482">Configure bearer authentication</span></span>

<span data-ttu-id="0cf9c-483">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-483">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0cf9c-484">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0cf9c-485">Dans le client JavaScript, le jeton d’accès est utilisé en tant que jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (plus précisément, dans Server-Sent les événements et les requêtes WebSocket).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0cf9c-486">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0cf9c-487">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0cf9c-488">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0cf9c-489">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-489">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0cf9c-490">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0cf9c-491">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0cf9c-492">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="0cf9c-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0cf9c-493">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0cf9c-494">.NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0cf9c-495">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-495">Option</span></span> | <span data-ttu-id="0cf9c-496">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-496">Default value</span></span> | <span data-ttu-id="0cf9c-497">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0cf9c-498">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-499">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-499">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-500">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0cf9c-501">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-502">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0cf9c-503">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-503">15 seconds</span></span> | <span data-ttu-id="0cf9c-504">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="0cf9c-505">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0cf9c-506">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-507">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-507">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0cf9c-508">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-508">15 seconds</span></span> | <span data-ttu-id="0cf9c-509">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0cf9c-510">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0cf9c-511">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="0cf9c-512">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0cf9c-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0cf9c-514">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-514">Option</span></span> | <span data-ttu-id="0cf9c-515">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-515">Default value</span></span> | <span data-ttu-id="0cf9c-516">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0cf9c-517">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-518">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-518">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-519">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0cf9c-520">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-521">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="0cf9c-522">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-523">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0cf9c-524">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0cf9c-525">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0cf9c-526">Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-526">Java</span></span>](#tab/java)

| <span data-ttu-id="0cf9c-527">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-527">Option</span></span> | <span data-ttu-id="0cf9c-528">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-528">Default value</span></span> | <span data-ttu-id="0cf9c-529">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0cf9c-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0cf9c-531">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-532">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-532">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-533">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0cf9c-534">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-535">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0cf9c-536">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-536">15 seconds</span></span> | <span data-ttu-id="0cf9c-537">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="0cf9c-538">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0cf9c-539">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-540">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-540">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="0cf9c-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="0cf9c-542">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-543">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0cf9c-544">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0cf9c-545">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0cf9c-546">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0cf9c-546">Configure additional options</span></span>

<span data-ttu-id="0cf9c-547">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0cf9c-548">.NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0cf9c-549">Option .NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-549">.NET Option</span></span> |  <span data-ttu-id="0cf9c-550">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-550">Default value</span></span> | <span data-ttu-id="0cf9c-551">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0cf9c-552">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0cf9c-553">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-554">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-554">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-555">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-555">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0cf9c-556">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-556">Empty</span></span> | <span data-ttu-id="0cf9c-557">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0cf9c-558">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-558">Empty</span></span> | <span data-ttu-id="0cf9c-559">Collection de HTTP cookie s à envoyer avec chaque requête http.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-559">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0cf9c-560">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-560">Empty</span></span> | <span data-ttu-id="0cf9c-561">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0cf9c-562">5 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-562">5 seconds</span></span> | <span data-ttu-id="0cf9c-563">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-563">WebSockets only.</span></span> <span data-ttu-id="0cf9c-564">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0cf9c-565">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0cf9c-566">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-566">Empty</span></span> | <span data-ttu-id="0cf9c-567">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0cf9c-568">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0cf9c-569">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="0cf9c-570">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0cf9c-571">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0cf9c-572">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que Cookie s et en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="0cf9c-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0cf9c-573">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0cf9c-574">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0cf9c-575">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0cf9c-576">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0cf9c-577">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0cf9c-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0cf9c-579">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-579">JavaScript Option</span></span> | <span data-ttu-id="0cf9c-580">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-580">Default Value</span></span> | <span data-ttu-id="0cf9c-581">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0cf9c-582">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="0cf9c-583"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valeur spécifiant le transport à utiliser pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="0cf9c-584">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0cf9c-585">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-586">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-587">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0cf9c-588">Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-588">Java</span></span>](#tab/java)

| <span data-ttu-id="0cf9c-589">Option Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-589">Java Option</span></span> | <span data-ttu-id="0cf9c-590">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-590">Default Value</span></span> | <span data-ttu-id="0cf9c-591">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0cf9c-592">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0cf9c-593">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-594">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-594">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-595">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-595">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0cf9c-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0cf9c-597">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-597">Empty</span></span> | <span data-ttu-id="0cf9c-598">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0cf9c-599">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0cf9c-600">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0cf9c-601">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0cf9c-602">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0cf9c-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0cf9c-603">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="0cf9c-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0cf9c-604">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-604">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0cf9c-605">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0cf9c-606">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="0cf9c-607">`AddJsonProtocol`peut être ajouté après [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-607">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0cf9c-608">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0cf9c-609">La propriété [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur cet objet est un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objet qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0cf9c-610">Pour plus d’informations, consultez la [System.Text.Jsdans la documentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="0cf9c-611">Par exemple, pour configurer le sérialiseur de manière à ce qu’il ne change pas la casse des noms de propriétés, au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="0cf9c-612">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0cf9c-613">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="0cf9c-614">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="0cf9c-615">Basculer vers Newtonsoft.Jssur</span><span class="sxs-lookup"><span data-stu-id="0cf9c-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="0cf9c-616">Si vous avez besoin de fonctionnalités de `Newtonsoft.Json` qui ne sont pas prises en charge dans `System.Text.Json` , consultez [basculer vers Newtonsoft.Jssur](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0cf9c-617">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="0cf9c-617">MessagePack serialization options</span></span>

<span data-ttu-id="0cf9c-618">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0cf9c-619">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-619">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0cf9c-620">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0cf9c-621">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="0cf9c-621">Configure server options</span></span>

<span data-ttu-id="0cf9c-622">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-622">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0cf9c-623">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-623">Option</span></span> | <span data-ttu-id="0cf9c-624">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-624">Default Value</span></span> | <span data-ttu-id="0cf9c-625">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="0cf9c-626">30 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-626">30 seconds</span></span> | <span data-ttu-id="0cf9c-627">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="0cf9c-628">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="0cf9c-629">La valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="0cf9c-630">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-630">15 seconds</span></span> | <span data-ttu-id="0cf9c-631">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0cf9c-632">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-633">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-633">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0cf9c-634">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-634">15 seconds</span></span> | <span data-ttu-id="0cf9c-635">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0cf9c-636">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0cf9c-637">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0cf9c-638">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="0cf9c-638">All installed protocols</span></span> | <span data-ttu-id="0cf9c-639">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-639">Protocols supported by this hub.</span></span> <span data-ttu-id="0cf9c-640">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0cf9c-641">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0cf9c-642">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="0cf9c-643">Nombre maximal d’éléments pouvant être mis en mémoire tampon pour les flux de téléchargement du client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="0cf9c-644">Si cette limite est atteinte, le traitement des appels est bloqué jusqu’à ce que le serveur traite les éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="0cf9c-645">32 Ko</span><span class="sxs-lookup"><span data-stu-id="0cf9c-645">32 KB</span></span> | <span data-ttu-id="0cf9c-646">Taille maximale d’un seul message de concentrateur entrant.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="0cf9c-647">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-647">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="0cf9c-648">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-648">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0cf9c-649">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="0cf9c-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0cf9c-650">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0cf9c-651">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="0cf9c-652">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-652">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0cf9c-653">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-653">Option</span></span> | <span data-ttu-id="0cf9c-654">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-654">Default Value</span></span> | <span data-ttu-id="0cf9c-655">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0cf9c-656">32 Ko</span><span class="sxs-lookup"><span data-stu-id="0cf9c-656">32 KB</span></span> | <span data-ttu-id="0cf9c-657">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon avant d’appliquer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="0cf9c-658">L’augmentation de cette valeur permet au serveur de recevoir plus rapidement des messages plus volumineux sans appliquer la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0cf9c-659">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0cf9c-660">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0cf9c-661">32 Ko</span><span class="sxs-lookup"><span data-stu-id="0cf9c-661">32 KB</span></span> | <span data-ttu-id="0cf9c-662">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon avant d’observer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="0cf9c-663">L’augmentation de cette valeur permet au serveur de mettre plus rapidement en mémoire tampon des messages plus volumineux sans attendre la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0cf9c-664">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-664">All Transports are enabled.</span></span> | <span data-ttu-id="0cf9c-665">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0cf9c-666">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-666">See below.</span></span> | <span data-ttu-id="0cf9c-667">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0cf9c-668">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-668">See below.</span></span> | <span data-ttu-id="0cf9c-669">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="0cf9c-670">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0cf9c-671">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-671">Option</span></span> | <span data-ttu-id="0cf9c-672">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-672">Default Value</span></span> | <span data-ttu-id="0cf9c-673">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0cf9c-674">90 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-674">90 seconds</span></span> | <span data-ttu-id="0cf9c-675">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0cf9c-676">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0cf9c-677">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0cf9c-678">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-678">Option</span></span> | <span data-ttu-id="0cf9c-679">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-679">Default Value</span></span> | <span data-ttu-id="0cf9c-680">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0cf9c-681">5 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-681">5 seconds</span></span> | <span data-ttu-id="0cf9c-682">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0cf9c-683">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0cf9c-684">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0cf9c-685">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="0cf9c-685">Configure client options</span></span>

<span data-ttu-id="0cf9c-686">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0cf9c-687">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0cf9c-688">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="0cf9c-688">Configure logging</span></span>

<span data-ttu-id="0cf9c-689">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0cf9c-690">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0cf9c-691">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0cf9c-692">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0cf9c-693">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0cf9c-694">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0cf9c-695">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0cf9c-696">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0cf9c-697">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0cf9c-698">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="0cf9c-699">Au lieu d’une `LogLevel` valeur, vous pouvez également fournir une `string` valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="0cf9c-700">Cela est utile lors SignalR de la configuration de la journalisation dans les environnements où vous n’avez pas accès aux `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-700">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="0cf9c-701">Le tableau suivant répertorie les niveaux de journalisation disponibles.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-701">The following table lists the available log levels.</span></span> <span data-ttu-id="0cf9c-702">La valeur que vous fournissez pour `configureLogging` définir le niveau de journalisation **minimale** qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="0cf9c-703">Les messages enregistrés à ce niveau, **ou les niveaux indiqués après celui-ci dans la table**, sont enregistrés.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-703">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="0cf9c-704">String</span><span class="sxs-lookup"><span data-stu-id="0cf9c-704">String</span></span>                      | <span data-ttu-id="0cf9c-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="0cf9c-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="0cf9c-706">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="0cf9c-707">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="0cf9c-708">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0cf9c-709">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-709">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0cf9c-710">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-710">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0cf9c-711">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0cf9c-712">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-712">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0cf9c-713">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0cf9c-714">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0cf9c-715">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="0cf9c-715">Configure allowed transports</span></span>

<span data-ttu-id="0cf9c-716">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-716">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0cf9c-717">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0cf9c-718">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-718">All transports are enabled by default.</span></span>

<span data-ttu-id="0cf9c-719">Par exemple, pour désactiver le transport des événements Server-Sent, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0cf9c-720">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="0cf9c-721">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="0cf9c-722">Dans le client Java, le transport est sélectionné à l’aide de la `withTransport` méthode sur le `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="0cf9c-723">Le client Java utilise le transport WebSocket par défaut.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0cf9c-724">Le SignalR client Java ne prend pas encore en charge le secours pour le transport.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-724">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0cf9c-725">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="0cf9c-725">Configure bearer authentication</span></span>

<span data-ttu-id="0cf9c-726">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-726">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0cf9c-727">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0cf9c-728">Dans le client JavaScript, le jeton d’accès est utilisé en tant que jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (plus précisément, dans Server-Sent les événements et les requêtes WebSocket).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0cf9c-729">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0cf9c-730">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0cf9c-731">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0cf9c-732">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-732">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0cf9c-733">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0cf9c-734">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0cf9c-735">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="0cf9c-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0cf9c-736">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0cf9c-737">.NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0cf9c-738">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-738">Option</span></span> | <span data-ttu-id="0cf9c-739">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-739">Default value</span></span> | <span data-ttu-id="0cf9c-740">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0cf9c-741">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-742">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-742">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-743">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0cf9c-744">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-745">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0cf9c-746">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-746">15 seconds</span></span> | <span data-ttu-id="0cf9c-747">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="0cf9c-748">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0cf9c-749">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-750">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-750">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0cf9c-751">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-751">15 seconds</span></span> | <span data-ttu-id="0cf9c-752">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0cf9c-753">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0cf9c-754">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="0cf9c-755">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0cf9c-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0cf9c-757">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-757">Option</span></span> | <span data-ttu-id="0cf9c-758">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-758">Default value</span></span> | <span data-ttu-id="0cf9c-759">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0cf9c-760">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-761">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-761">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-762">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0cf9c-763">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-764">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="0cf9c-765">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-766">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0cf9c-767">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0cf9c-768">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0cf9c-769">Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-769">Java</span></span>](#tab/java)

| <span data-ttu-id="0cf9c-770">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-770">Option</span></span> | <span data-ttu-id="0cf9c-771">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-771">Default value</span></span> | <span data-ttu-id="0cf9c-772">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0cf9c-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0cf9c-774">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-775">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-775">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-776">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0cf9c-777">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-778">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0cf9c-779">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-779">15 seconds</span></span> | <span data-ttu-id="0cf9c-780">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="0cf9c-781">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0cf9c-782">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-783">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-783">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="0cf9c-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="0cf9c-785">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-786">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0cf9c-787">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0cf9c-788">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0cf9c-789">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0cf9c-789">Configure additional options</span></span>

<span data-ttu-id="0cf9c-790">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0cf9c-791">.NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0cf9c-792">Option .NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-792">.NET Option</span></span> |  <span data-ttu-id="0cf9c-793">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-793">Default value</span></span> | <span data-ttu-id="0cf9c-794">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0cf9c-795">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0cf9c-796">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-797">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-797">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-798">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-798">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0cf9c-799">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-799">Empty</span></span> | <span data-ttu-id="0cf9c-800">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0cf9c-801">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-801">Empty</span></span> | <span data-ttu-id="0cf9c-802">Collection de HTTP cookie s à envoyer avec chaque requête http.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-802">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0cf9c-803">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-803">Empty</span></span> | <span data-ttu-id="0cf9c-804">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0cf9c-805">5 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-805">5 seconds</span></span> | <span data-ttu-id="0cf9c-806">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-806">WebSockets only.</span></span> <span data-ttu-id="0cf9c-807">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0cf9c-808">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0cf9c-809">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-809">Empty</span></span> | <span data-ttu-id="0cf9c-810">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0cf9c-811">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0cf9c-812">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="0cf9c-813">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0cf9c-814">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0cf9c-815">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que Cookie s et en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="0cf9c-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0cf9c-816">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0cf9c-817">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0cf9c-818">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0cf9c-819">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0cf9c-820">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0cf9c-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0cf9c-822">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-822">JavaScript Option</span></span> | <span data-ttu-id="0cf9c-823">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-823">Default Value</span></span> | <span data-ttu-id="0cf9c-824">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0cf9c-825">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="0cf9c-826"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valeur spécifiant le transport à utiliser pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="0cf9c-827">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0cf9c-828">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-829">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-829">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-830">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-830">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0cf9c-831">Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-831">Java</span></span>](#tab/java)

| <span data-ttu-id="0cf9c-832">Option Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-832">Java Option</span></span> | <span data-ttu-id="0cf9c-833">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-833">Default Value</span></span> | <span data-ttu-id="0cf9c-834">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0cf9c-835">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0cf9c-836">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-837">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-837">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-838">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-838">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0cf9c-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0cf9c-840">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-840">Empty</span></span> | <span data-ttu-id="0cf9c-841">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0cf9c-842">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0cf9c-843">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0cf9c-844">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0cf9c-845">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0cf9c-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0cf9c-846">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="0cf9c-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0cf9c-847">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-847">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0cf9c-848">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0cf9c-849">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , qui peut être ajoutée après l' [ SignalR Ajout](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) de votre `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0cf9c-850">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0cf9c-851">La propriété [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet est un `JsonSerializerSettings` objet JSON.net qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0cf9c-852">Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="0cf9c-853">Par exemple, pour configurer le sérialiseur afin d’utiliser les noms de propriété « casse Pascal », au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="0cf9c-854">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0cf9c-855">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="0cf9c-856">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0cf9c-857">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="0cf9c-857">MessagePack serialization options</span></span>

<span data-ttu-id="0cf9c-858">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0cf9c-859">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-859">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0cf9c-860">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0cf9c-861">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="0cf9c-861">Configure server options</span></span>

<span data-ttu-id="0cf9c-862">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-862">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0cf9c-863">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-863">Option</span></span> | <span data-ttu-id="0cf9c-864">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-864">Default Value</span></span> | <span data-ttu-id="0cf9c-865">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="0cf9c-866">30 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-866">30 seconds</span></span> | <span data-ttu-id="0cf9c-867">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="0cf9c-868">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="0cf9c-869">La valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="0cf9c-870">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-870">15 seconds</span></span> | <span data-ttu-id="0cf9c-871">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0cf9c-872">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-873">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-873">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0cf9c-874">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-874">15 seconds</span></span> | <span data-ttu-id="0cf9c-875">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0cf9c-876">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0cf9c-877">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0cf9c-878">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="0cf9c-878">All installed protocols</span></span> | <span data-ttu-id="0cf9c-879">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-879">Protocols supported by this hub.</span></span> <span data-ttu-id="0cf9c-880">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0cf9c-881">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0cf9c-882">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="0cf9c-883">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-883">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="0cf9c-884">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-884">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0cf9c-885">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="0cf9c-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0cf9c-886">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0cf9c-887">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="0cf9c-888">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-888">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0cf9c-889">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-889">Option</span></span> | <span data-ttu-id="0cf9c-890">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-890">Default Value</span></span> | <span data-ttu-id="0cf9c-891">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0cf9c-892">32 Ko</span><span class="sxs-lookup"><span data-stu-id="0cf9c-892">32 KB</span></span> | <span data-ttu-id="0cf9c-893">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="0cf9c-894">L’augmentation de cette valeur permet au serveur de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0cf9c-895">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0cf9c-896">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0cf9c-897">32 Ko</span><span class="sxs-lookup"><span data-stu-id="0cf9c-897">32 KB</span></span> | <span data-ttu-id="0cf9c-898">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="0cf9c-899">L’augmentation de cette valeur permet au serveur d’envoyer des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0cf9c-900">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-900">All Transports are enabled.</span></span> | <span data-ttu-id="0cf9c-901">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0cf9c-902">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-902">See below.</span></span> | <span data-ttu-id="0cf9c-903">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0cf9c-904">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-904">See below.</span></span> | <span data-ttu-id="0cf9c-905">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="0cf9c-906">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0cf9c-907">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-907">Option</span></span> | <span data-ttu-id="0cf9c-908">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-908">Default Value</span></span> | <span data-ttu-id="0cf9c-909">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0cf9c-910">90 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-910">90 seconds</span></span> | <span data-ttu-id="0cf9c-911">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0cf9c-912">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0cf9c-913">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0cf9c-914">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-914">Option</span></span> | <span data-ttu-id="0cf9c-915">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-915">Default Value</span></span> | <span data-ttu-id="0cf9c-916">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0cf9c-917">5 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-917">5 seconds</span></span> | <span data-ttu-id="0cf9c-918">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0cf9c-919">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0cf9c-920">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0cf9c-921">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="0cf9c-921">Configure client options</span></span>

<span data-ttu-id="0cf9c-922">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0cf9c-923">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0cf9c-924">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="0cf9c-924">Configure logging</span></span>

<span data-ttu-id="0cf9c-925">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0cf9c-926">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0cf9c-927">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0cf9c-928">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0cf9c-929">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0cf9c-930">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0cf9c-931">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0cf9c-932">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0cf9c-933">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0cf9c-934">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0cf9c-935">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0cf9c-936">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-936">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0cf9c-937">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-937">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0cf9c-938">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0cf9c-939">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-939">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0cf9c-940">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0cf9c-941">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0cf9c-942">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="0cf9c-942">Configure allowed transports</span></span>

<span data-ttu-id="0cf9c-943">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-943">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0cf9c-944">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0cf9c-945">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-945">All transports are enabled by default.</span></span>

<span data-ttu-id="0cf9c-946">Par exemple, pour désactiver le transport des événements Server-Sent, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0cf9c-947">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="0cf9c-948">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0cf9c-949">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="0cf9c-949">Configure bearer authentication</span></span>

<span data-ttu-id="0cf9c-950">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-950">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0cf9c-951">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0cf9c-952">Dans le client JavaScript, le jeton d’accès est utilisé en tant que jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (plus précisément, dans Server-Sent les événements et les requêtes WebSocket).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0cf9c-953">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0cf9c-954">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0cf9c-955">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0cf9c-956">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-956">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0cf9c-957">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0cf9c-958">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0cf9c-959">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="0cf9c-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0cf9c-960">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0cf9c-961">.NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0cf9c-962">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-962">Option</span></span> | <span data-ttu-id="0cf9c-963">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-963">Default value</span></span> | <span data-ttu-id="0cf9c-964">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0cf9c-965">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-966">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-966">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-967">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0cf9c-968">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-969">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0cf9c-970">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-970">15 seconds</span></span> | <span data-ttu-id="0cf9c-971">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="0cf9c-972">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0cf9c-973">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-974">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-974">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0cf9c-975">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-975">15 seconds</span></span> | <span data-ttu-id="0cf9c-976">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0cf9c-977">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0cf9c-978">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="0cf9c-979">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0cf9c-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0cf9c-981">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-981">Option</span></span> | <span data-ttu-id="0cf9c-982">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-982">Default value</span></span> | <span data-ttu-id="0cf9c-983">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0cf9c-984">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-985">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-985">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-986">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0cf9c-987">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-988">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="0cf9c-989">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-990">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0cf9c-991">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0cf9c-992">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0cf9c-993">Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-993">Java</span></span>](#tab/java)

| <span data-ttu-id="0cf9c-994">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-994">Option</span></span> | <span data-ttu-id="0cf9c-995">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-995">Default value</span></span> | <span data-ttu-id="0cf9c-996">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0cf9c-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0cf9c-998">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-999">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-999">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-1000">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0cf9c-1001">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-1002">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0cf9c-1003">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1003">15 seconds</span></span> | <span data-ttu-id="0cf9c-1004">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="0cf9c-1005">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0cf9c-1006">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-1007">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1007">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="0cf9c-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="0cf9c-1009">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-1010">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0cf9c-1011">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0cf9c-1012">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0cf9c-1013">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1013">Configure additional options</span></span>

<span data-ttu-id="0cf9c-1014">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0cf9c-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0cf9c-1016">Option .NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1016">.NET Option</span></span> |  <span data-ttu-id="0cf9c-1017">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1017">Default value</span></span> | <span data-ttu-id="0cf9c-1018">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0cf9c-1019">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0cf9c-1020">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-1021">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1021">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-1022">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1022">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0cf9c-1023">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1023">Empty</span></span> | <span data-ttu-id="0cf9c-1024">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0cf9c-1025">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1025">Empty</span></span> | <span data-ttu-id="0cf9c-1026">Collection de HTTP cookie s à envoyer avec chaque requête http.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1026">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0cf9c-1027">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1027">Empty</span></span> | <span data-ttu-id="0cf9c-1028">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0cf9c-1029">5 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1029">5 seconds</span></span> | <span data-ttu-id="0cf9c-1030">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1030">WebSockets only.</span></span> <span data-ttu-id="0cf9c-1031">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0cf9c-1032">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0cf9c-1033">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1033">Empty</span></span> | <span data-ttu-id="0cf9c-1034">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0cf9c-1035">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0cf9c-1036">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="0cf9c-1037">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0cf9c-1038">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0cf9c-1039">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que Cookie s et en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0cf9c-1040">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0cf9c-1041">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0cf9c-1042">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0cf9c-1043">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0cf9c-1044">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0cf9c-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0cf9c-1046">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1046">JavaScript Option</span></span> | <span data-ttu-id="0cf9c-1047">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1047">Default Value</span></span> | <span data-ttu-id="0cf9c-1048">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0cf9c-1049">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="0cf9c-1050"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valeur spécifiant le transport à utiliser pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="0cf9c-1051">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0cf9c-1052">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-1053">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1053">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-1054">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1054">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0cf9c-1055">Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="0cf9c-1056">Option Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1056">Java Option</span></span> | <span data-ttu-id="0cf9c-1057">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1057">Default Value</span></span> | <span data-ttu-id="0cf9c-1058">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0cf9c-1059">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0cf9c-1060">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-1061">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1061">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-1062">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1062">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0cf9c-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0cf9c-1064">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1064">Empty</span></span> | <span data-ttu-id="0cf9c-1065">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0cf9c-1066">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0cf9c-1067">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0cf9c-1068">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0cf9c-1069">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0cf9c-1070">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0cf9c-1071">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1071">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0cf9c-1072">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0cf9c-1073">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , qui peut être ajoutée après l' [ SignalR Ajout](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) de votre `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0cf9c-1074">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0cf9c-1075">La propriété [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet est un `JsonSerializerSettings` objet JSON.net qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0cf9c-1076">Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="0cf9c-1077">Par exemple, pour configurer le sérialiseur afin d’utiliser les noms de propriété « casse Pascal », au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="0cf9c-1078">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0cf9c-1079">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="0cf9c-1080">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0cf9c-1081">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1081">MessagePack serialization options</span></span>

<span data-ttu-id="0cf9c-1082">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0cf9c-1083">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1083">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0cf9c-1084">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0cf9c-1085">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1085">Configure server options</span></span>

<span data-ttu-id="0cf9c-1086">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1086">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0cf9c-1087">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1087">Option</span></span> | <span data-ttu-id="0cf9c-1088">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1088">Default Value</span></span> | <span data-ttu-id="0cf9c-1089">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="0cf9c-1090">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1090">15 seconds</span></span> | <span data-ttu-id="0cf9c-1091">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0cf9c-1092">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-1093">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1093">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0cf9c-1094">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1094">15 seconds</span></span> | <span data-ttu-id="0cf9c-1095">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0cf9c-1096">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0cf9c-1097">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0cf9c-1098">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1098">All installed protocols</span></span> | <span data-ttu-id="0cf9c-1099">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="0cf9c-1100">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0cf9c-1101">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0cf9c-1102">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="0cf9c-1103">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1103">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="0cf9c-1104">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1104">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0cf9c-1105">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0cf9c-1106">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0cf9c-1107">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="0cf9c-1108">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1108">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0cf9c-1109">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1109">Option</span></span> | <span data-ttu-id="0cf9c-1110">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1110">Default Value</span></span> | <span data-ttu-id="0cf9c-1111">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0cf9c-1112">32 Ko</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1112">32 KB</span></span> | <span data-ttu-id="0cf9c-1113">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="0cf9c-1114">L’augmentation de cette valeur permet au serveur de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0cf9c-1115">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0cf9c-1116">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0cf9c-1117">32 Ko</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1117">32 KB</span></span> | <span data-ttu-id="0cf9c-1118">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="0cf9c-1119">L’augmentation de cette valeur permet au serveur d’envoyer des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0cf9c-1120">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1120">All Transports are enabled.</span></span> | <span data-ttu-id="0cf9c-1121">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0cf9c-1122">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1122">See below.</span></span> | <span data-ttu-id="0cf9c-1123">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0cf9c-1124">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1124">See below.</span></span> | <span data-ttu-id="0cf9c-1125">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="0cf9c-1126">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0cf9c-1127">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1127">Option</span></span> | <span data-ttu-id="0cf9c-1128">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1128">Default Value</span></span> | <span data-ttu-id="0cf9c-1129">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0cf9c-1130">90 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1130">90 seconds</span></span> | <span data-ttu-id="0cf9c-1131">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0cf9c-1132">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0cf9c-1133">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0cf9c-1134">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1134">Option</span></span> | <span data-ttu-id="0cf9c-1135">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1135">Default Value</span></span> | <span data-ttu-id="0cf9c-1136">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0cf9c-1137">5 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1137">5 seconds</span></span> | <span data-ttu-id="0cf9c-1138">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0cf9c-1139">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0cf9c-1140">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0cf9c-1141">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1141">Configure client options</span></span>

<span data-ttu-id="0cf9c-1142">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0cf9c-1143">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0cf9c-1144">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1144">Configure logging</span></span>

<span data-ttu-id="0cf9c-1145">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0cf9c-1146">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0cf9c-1147">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0cf9c-1148">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0cf9c-1149">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0cf9c-1150">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0cf9c-1151">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0cf9c-1152">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0cf9c-1153">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0cf9c-1154">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0cf9c-1155">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0cf9c-1156">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1156">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0cf9c-1157">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1157">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0cf9c-1158">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0cf9c-1159">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1159">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0cf9c-1160">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0cf9c-1161">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0cf9c-1162">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1162">Configure allowed transports</span></span>

<span data-ttu-id="0cf9c-1163">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1163">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0cf9c-1164">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0cf9c-1165">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="0cf9c-1166">Par exemple, pour désactiver le transport des événements Server-Sent, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0cf9c-1167">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0cf9c-1168">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1168">Configure bearer authentication</span></span>

<span data-ttu-id="0cf9c-1169">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1169">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0cf9c-1170">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0cf9c-1171">Dans le client JavaScript, le jeton d’accès est utilisé en tant que jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (plus précisément, dans Server-Sent les événements et les requêtes WebSocket).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0cf9c-1172">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0cf9c-1173">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0cf9c-1174">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0cf9c-1175">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1175">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0cf9c-1176">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0cf9c-1177">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0cf9c-1178">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0cf9c-1179">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0cf9c-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0cf9c-1181">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1181">Option</span></span> | <span data-ttu-id="0cf9c-1182">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1182">Default value</span></span> | <span data-ttu-id="0cf9c-1183">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0cf9c-1184">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-1185">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1185">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-1186">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0cf9c-1187">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-1188">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0cf9c-1189">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1189">15 seconds</span></span> | <span data-ttu-id="0cf9c-1190">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="0cf9c-1191">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0cf9c-1192">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-1193">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1193">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="0cf9c-1194">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0cf9c-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0cf9c-1196">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1196">Option</span></span> | <span data-ttu-id="0cf9c-1197">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1197">Default value</span></span> | <span data-ttu-id="0cf9c-1198">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0cf9c-1199">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-1200">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1200">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-1201">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0cf9c-1202">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-1203">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0cf9c-1204">Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="0cf9c-1205">Option</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1205">Option</span></span> | <span data-ttu-id="0cf9c-1206">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1206">Default value</span></span> | <span data-ttu-id="0cf9c-1207">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0cf9c-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0cf9c-1209">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0cf9c-1210">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1210">Timeout for server activity.</span></span> <span data-ttu-id="0cf9c-1211">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0cf9c-1212">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0cf9c-1213">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` , pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0cf9c-1214">15 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1214">15 seconds</span></span> | <span data-ttu-id="0cf9c-1215">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="0cf9c-1216">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0cf9c-1217">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0cf9c-1218">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1218">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0cf9c-1219">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1219">Configure additional options</span></span>

<span data-ttu-id="0cf9c-1220">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0cf9c-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0cf9c-1222">Option .NET</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1222">.NET Option</span></span> |  <span data-ttu-id="0cf9c-1223">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1223">Default value</span></span> | <span data-ttu-id="0cf9c-1224">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0cf9c-1225">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0cf9c-1226">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-1227">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1227">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-1228">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1228">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0cf9c-1229">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1229">Empty</span></span> | <span data-ttu-id="0cf9c-1230">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0cf9c-1231">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1231">Empty</span></span> | <span data-ttu-id="0cf9c-1232">Collection de HTTP cookie s à envoyer avec chaque requête http.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1232">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0cf9c-1233">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1233">Empty</span></span> | <span data-ttu-id="0cf9c-1234">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0cf9c-1235">5 secondes</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1235">5 seconds</span></span> | <span data-ttu-id="0cf9c-1236">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1236">WebSockets only.</span></span> <span data-ttu-id="0cf9c-1237">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0cf9c-1238">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0cf9c-1239">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1239">Empty</span></span> | <span data-ttu-id="0cf9c-1240">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0cf9c-1241">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0cf9c-1242">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="0cf9c-1243">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0cf9c-1244">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0cf9c-1245">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que Cookie s et en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0cf9c-1246">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0cf9c-1247">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0cf9c-1248">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0cf9c-1249">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0cf9c-1250">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0cf9c-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0cf9c-1252">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1252">JavaScript Option</span></span> | <span data-ttu-id="0cf9c-1253">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1253">Default Value</span></span> | <span data-ttu-id="0cf9c-1254">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0cf9c-1255">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="0cf9c-1256"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valeur spécifiant le transport à utiliser pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="0cf9c-1257">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0cf9c-1258">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-1259">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1259">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-1260">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1260">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0cf9c-1261">Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="0cf9c-1262">Option Java</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1262">Java Option</span></span> | <span data-ttu-id="0cf9c-1263">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1263">Default Value</span></span> | <span data-ttu-id="0cf9c-1264">Description</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0cf9c-1265">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0cf9c-1266">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0cf9c-1267">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1267">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0cf9c-1268">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1268">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0cf9c-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0cf9c-1270">Empty</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1270">Empty</span></span> | <span data-ttu-id="0cf9c-1271">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0cf9c-1272">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0cf9c-1273">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0cf9c-1274">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0cf9c-1275">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0cf9c-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
