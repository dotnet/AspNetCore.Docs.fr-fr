---
title: Blazor SignalR Aide ASP.net Core
author: guardrex
description: Découvrez comment configurer et gérer les Blazor SignalR connexions.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2021
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
uid: blazor/fundamentals/signalr
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 63dfd93fbc42a869211bc5cd481a8dbee6eb6c91
ms.sourcegitcommit: 3982ff9dabb5b12aeb0a61cde2686b5253364f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118913"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="a7856-103">Blazor SignalR Aide ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="a7856-103">ASP.NET Core Blazor SignalR guidance</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="a7856-104">Cet article explique comment configurer et gérer SignalR des connexions dans des Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="a7856-104">This article explains how to configure and manage SignalR connections in Blazor apps.</span></span>

<span data-ttu-id="a7856-105">Pour obtenir des conseils généraux sur la configuration de ASP.NET Core SignalR , consultez les rubriques dans la <xref:signalr/introduction> section de la documentation.</span><span class="sxs-lookup"><span data-stu-id="a7856-105">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="a7856-106">Pour configurer l' SignalR [Ajout à une Blazor WebAssembly solution hébergée](xref:tutorials/signalr-blazor), consultez <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="a7856-106">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="a7856-107">SignalR négociation Cross-Origin pour l’authentification</span><span class="sxs-lookup"><span data-stu-id="a7856-107">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="a7856-108">Pour configurer le SignalR client sous-jacent pour envoyer des informations d’identification, telles que cookie les en-têtes s ou http authentication :</span><span class="sxs-lookup"><span data-stu-id="a7856-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="a7856-109">Utilisez <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> pour définir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> les demandes Cross-Origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) .</span><span class="sxs-lookup"><span data-stu-id="a7856-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests.</span></span>

  <span data-ttu-id="a7856-110">`IncludeRequestCredentialsMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="a7856-110">`IncludeRequestCredentialsMessageHandler.cs`:</span></span>

  ```csharp
  using System.Net.Http;
  using System.Threading;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Components.WebAssembly.Http;

  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="a7856-111">En cas de création d’une connexion de concentrateur, attribuez <xref:System.Net.Http.HttpMessageHandler> à l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option :</span><span class="sxs-lookup"><span data-stu-id="a7856-111">Where a hub connection is built, assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  HubConnectionBuilder hubConnecton;

  ...

  hubConnecton = new HubConnectionBuilder()
      .WithUrl(new Uri(NavigationManager.ToAbsoluteUri("/chathub")), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

  <span data-ttu-id="a7856-112">L’exemple précédent configure l’URL de connexion du concentrateur sur l’adresse URI absolue à `/chathub` , qui est l’URL utilisée dans le [ SignalR Blazor didacticiel with](xref:tutorials/signalr-blazor) du `Index` composant ( `Pages/Index.razor` ).</span><span class="sxs-lookup"><span data-stu-id="a7856-112">The preceding example configures the hub connection URL to the absolute URI address at `/chathub`, which is the URL used in the [SignalR with Blazor tutorial](xref:tutorials/signalr-blazor) in the `Index` component (`Pages/Index.razor`).</span></span> <span data-ttu-id="a7856-113">L’URI peut également être défini via une chaîne, par exemple `https://signalr.example.com` , ou via la [configuration](xref:blazor/fundamentals/configuration).</span><span class="sxs-lookup"><span data-stu-id="a7856-113">The URI can also be set via a string, for example `https://signalr.example.com`, or via [configuration](xref:blazor/fundamentals/configuration).</span></span>

<span data-ttu-id="a7856-114">Pour plus d’informations, consultez <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="a7856-114">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="render-mode"></a><span data-ttu-id="a7856-115">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="a7856-115">Render mode</span></span>

<span data-ttu-id="a7856-116">Si une Blazor WebAssembly application qui utilise SignalR est configurée pour être prérendu sur le serveur, le prérendu se produit avant l’établissement de la connexion cliente au serveur.</span><span class="sxs-lookup"><span data-stu-id="a7856-116">If a Blazor WebAssembly app that uses SignalR is configured to prerender on the server, prerendering occurs before the client connection to the server is established.</span></span> <span data-ttu-id="a7856-117">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="a7856-117">For more information, see the following articles:</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a7856-118">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a7856-118">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="a7856-119">Cet article explique comment configurer et gérer SignalR des connexions dans des Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="a7856-119">This article explains how to configure and manage SignalR connections in Blazor apps.</span></span>

<span data-ttu-id="a7856-120">Pour obtenir des conseils généraux sur la configuration de ASP.NET Core SignalR , consultez les rubriques dans la <xref:signalr/introduction> section de la documentation.</span><span class="sxs-lookup"><span data-stu-id="a7856-120">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="a7856-121">Pour configurer l' SignalR [Ajout à une Blazor WebAssembly solution hébergée](xref:tutorials/signalr-blazor), consultez <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="a7856-121">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="a7856-122">Options du gestionnaire de circuit</span><span class="sxs-lookup"><span data-stu-id="a7856-122">Circuit handler options</span></span>

<span data-ttu-id="a7856-123">Configurez le Blazor Server circuit comme <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> indiqué dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="a7856-123">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="a7856-124">Option</span><span class="sxs-lookup"><span data-stu-id="a7856-124">Option</span></span> | <span data-ttu-id="a7856-125">Default</span><span class="sxs-lookup"><span data-stu-id="a7856-125">Default</span></span> | <span data-ttu-id="a7856-126">Description</span><span class="sxs-lookup"><span data-stu-id="a7856-126">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="a7856-127">Envoyer des messages d’exception détaillés à JavaScript lorsqu’une exception non gérée se produit sur le circuit ou lorsqu’un appel de méthode .NET via l’interopérabilité JS entraîne une exception.</span><span class="sxs-lookup"><span data-stu-id="a7856-127">Send detailed exception messages to JavaScript when an unhandled exception occurs on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="a7856-128">100</span><span class="sxs-lookup"><span data-stu-id="a7856-128">100</span></span> | <span data-ttu-id="a7856-129">Nombre maximal de circuits déconnectés que le serveur détient en mémoire à la fois.</span><span class="sxs-lookup"><span data-stu-id="a7856-129">Maximum number of disconnected circuits that the server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="a7856-130">3 minutes</span><span class="sxs-lookup"><span data-stu-id="a7856-130">3 minutes</span></span> | <span data-ttu-id="a7856-131">Durée maximale pendant laquelle un circuit déconnecté est maintenu en mémoire avant d’être détruit.</span><span class="sxs-lookup"><span data-stu-id="a7856-131">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="a7856-132">1 minute</span><span class="sxs-lookup"><span data-stu-id="a7856-132">1 minute</span></span> | <span data-ttu-id="a7856-133">Durée maximale pendant laquelle le serveur attend avant d’expirer un appel de fonction JavaScript asynchrone.</span><span class="sxs-lookup"><span data-stu-id="a7856-133">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="a7856-134">10</span><span class="sxs-lookup"><span data-stu-id="a7856-134">10</span></span> | <span data-ttu-id="a7856-135">Nombre maximal de lots de rendu sans accusé de réception le serveur conserve en mémoire par circuit à un moment donné pour prendre en charge une reconnexion fiable.</span><span class="sxs-lookup"><span data-stu-id="a7856-135">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="a7856-136">Après avoir atteint la limite, le serveur cesse de produire de nouveaux lots de rendu jusqu’à ce qu’un ou plusieurs lots soient reconnus par le client.</span><span class="sxs-lookup"><span data-stu-id="a7856-136">After reaching the limit, the server stops producing new render batches until one or more batches are acknowledged by the client.</span></span> |

<span data-ttu-id="a7856-137">Configurez les options dans `Startup.ConfigureServices` avec un délégué d’options à <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> .</span><span class="sxs-lookup"><span data-stu-id="a7856-137">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="a7856-138">L’exemple suivant affecte les valeurs d’option par défaut indiquées dans le tableau précédent.</span><span class="sxs-lookup"><span data-stu-id="a7856-138">The following example assigns the default option values shown in the preceding table.</span></span> <span data-ttu-id="a7856-139">Vérifiez que `Startup.cs` utilise l' <xref:System> espace de noms ( `using System;` ).</span><span class="sxs-lookup"><span data-stu-id="a7856-139">Confirm that `Startup.cs` uses the <xref:System> namespace (`using System;`).</span></span>

<span data-ttu-id="a7856-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a7856-140">`Startup.ConfigureServices`:</span></span>

```csharp
services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

<span data-ttu-id="a7856-141">Pour configurer le <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> , utilisez <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> avec <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> .</span><span class="sxs-lookup"><span data-stu-id="a7856-141">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="a7856-142">Pour obtenir une description des options, consultez <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="a7856-142">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="a7856-143">L’exemple suivant affecte les valeurs d’option par défaut.</span><span class="sxs-lookup"><span data-stu-id="a7856-143">The following example assigns the default option values.</span></span> <span data-ttu-id="a7856-144">Vérifiez que `Startup.cs` utilise l' <xref:System> espace de noms ( `using System;` ).</span><span class="sxs-lookup"><span data-stu-id="a7856-144">Confirm that `Startup.cs` uses the <xref:System> namespace (`using System;`).</span></span>

<span data-ttu-id="a7856-145">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a7856-145">`Startup.ConfigureServices`:</span></span>

```csharp
services.AddServerSideBlazor()
    .AddHubOptions(options =>
    {
        options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);
        options.EnableDetailedErrors = false;
        options.HandshakeTimeout = TimeSpan.FromSeconds(15);
        options.KeepAliveInterval = TimeSpan.FromSeconds(15);
        options.MaximumParallelInvocationsPerClient = 1;
        options.MaximumReceiveMessageSize = 32 * 1024;
        options.StreamBufferCapacity = 10;
    });
```

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="a7856-146">Refléter l’état de la connexion dans l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="a7856-146">Reflect the connection state in the UI</span></span>

<span data-ttu-id="a7856-147">Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="a7856-147">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="a7856-148">En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.</span><span class="sxs-lookup"><span data-stu-id="a7856-148">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="a7856-149">Pour personnaliser l’interface utilisateur, définissez un élément avec un `id` de `components-reconnect-modal` dans le `<body>` de la `_Host.cshtml` Razor page.</span><span class="sxs-lookup"><span data-stu-id="a7856-149">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page.</span></span>

<span data-ttu-id="a7856-150">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="a7856-150">`Pages/_Host.cshtml`:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="a7856-151">Ajoutez les styles CSS suivants à la feuille de style du site.</span><span class="sxs-lookup"><span data-stu-id="a7856-151">Add the following CSS styles to the site's stylesheet.</span></span>

<span data-ttu-id="a7856-152">`wwwroot/css/site.css`:</span><span class="sxs-lookup"><span data-stu-id="a7856-152">`wwwroot/css/site.css`:</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="a7856-153">Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément par le Blazor Framework.</span><span class="sxs-lookup"><span data-stu-id="a7856-153">The following table describes the CSS classes applied to the `components-reconnect-modal` element by the Blazor framework.</span></span>

| <span data-ttu-id="a7856-154">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="a7856-154">CSS class</span></span>                       | <span data-ttu-id="a7856-155">Détermine&hellip;</span><span class="sxs-lookup"><span data-stu-id="a7856-155">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="a7856-156">Connexion perdue.</span><span class="sxs-lookup"><span data-stu-id="a7856-156">A lost connection.</span></span> <span data-ttu-id="a7856-157">Le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="a7856-157">The client is attempting to reconnect.</span></span> <span data-ttu-id="a7856-158">Affichez le modal.</span><span class="sxs-lookup"><span data-stu-id="a7856-158">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="a7856-159">Une connexion active est rétablie sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="a7856-159">An active connection is re-established to the server.</span></span> <span data-ttu-id="a7856-160">Masquez le modal.</span><span class="sxs-lookup"><span data-stu-id="a7856-160">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="a7856-161">Échec de la reconnexion, probablement en raison d’une défaillance du réseau.</span><span class="sxs-lookup"><span data-stu-id="a7856-161">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="a7856-162">Pour tenter une reconnexion, appelez `window.Blazor.reconnect()` en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a7856-162">To attempt reconnection, call `window.Blazor.reconnect()` in JavaScript.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="a7856-163">Reconnexion refusée.</span><span class="sxs-lookup"><span data-stu-id="a7856-163">Reconnection rejected.</span></span> <span data-ttu-id="a7856-164">Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu.</span><span class="sxs-lookup"><span data-stu-id="a7856-164">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="a7856-165">Pour recharger l’application, appelez `location.reload()` en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a7856-165">To reload the app, call `location.reload()` in JavaScript.</span></span> <span data-ttu-id="a7856-166">Cet état de connexion peut se produire dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="a7856-166">This connection state may result when:</span></span><ul><li><span data-ttu-id="a7856-167">Un blocage dans le circuit côté serveur se produit.</span><span class="sxs-lookup"><span data-stu-id="a7856-167">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="a7856-168">Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a7856-168">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="a7856-169">Les instances des composants de l’utilisateur sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="a7856-169">Instances of the user's components are disposed.</span></span></li><li><span data-ttu-id="a7856-170">Le serveur est redémarré ou le processus de travail de l’application est recyclé.</span><span class="sxs-lookup"><span data-stu-id="a7856-170">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="a7856-171">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="a7856-171">Render mode</span></span>

<span data-ttu-id="a7856-172">Par défaut, Blazor Server les applications préaffichent l’interface utilisateur sur le serveur avant l’établissement de la connexion cliente au serveur.</span><span class="sxs-lookup"><span data-stu-id="a7856-172">By default, Blazor Server apps prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="a7856-173">Pour plus d’informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="a7856-173">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="a7856-174">Initialiser le Blazor circuit</span><span class="sxs-lookup"><span data-stu-id="a7856-174">Initialize the Blazor circuit</span></span>

<span data-ttu-id="a7856-175">Configurez le démarrage manuel du Blazor Server [ SignalR circuit](xref:blazor/hosting-models#circuits) d’une application dans le `Pages/_Host.cshtml` fichier :</span><span class="sxs-lookup"><span data-stu-id="a7856-175">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="a7856-176">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="a7856-176">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="a7856-177">Placez un script qui appelle `Blazor.start` après la `blazor.server.js` balise du script et à l’intérieur de la `</body>` balise de fermeture.</span><span class="sxs-lookup"><span data-stu-id="a7856-177">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="a7856-178">Lorsque `autostart` est désactivé, tous les aspects de l’application qui ne dépendent pas du circuit fonctionnent normalement.</span><span class="sxs-lookup"><span data-stu-id="a7856-178">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="a7856-179">Par exemple, le routage côté client est opérationnel.</span><span class="sxs-lookup"><span data-stu-id="a7856-179">For example, client-side routing is operational.</span></span> <span data-ttu-id="a7856-180">Toutefois, tous les aspects qui dépendent du circuit ne sont pas opérationnels tant que `Blazor.start` n’est pas appelé.</span><span class="sxs-lookup"><span data-stu-id="a7856-180">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="a7856-181">Le comportement de l’application n’est pas prévisible sans circuit établi.</span><span class="sxs-lookup"><span data-stu-id="a7856-181">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="a7856-182">Par exemple, les méthodes de composant ne peuvent pas s’exécuter pendant que le circuit est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="a7856-182">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="a7856-183">Initialiser Blazor lorsque le document est prêt</span><span class="sxs-lookup"><span data-stu-id="a7856-183">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="a7856-184">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="a7856-184">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="a7856-185">Chaîne à `Promise` qui résulte d’un démarrage manuel</span><span class="sxs-lookup"><span data-stu-id="a7856-185">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="a7856-186">Pour effectuer des tâches supplémentaires, telles que l’initialisation de l’interopérabilité JS, utilisez [`then`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) pour chaîner à [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) qui résulte d’un démarrage manuel de l' Blazor application.</span><span class="sxs-lookup"><span data-stu-id="a7856-186">To perform additional tasks, such as JS interop initialization, use [`then`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) to chain to the [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) that results from a manual Blazor app start.</span></span>

<span data-ttu-id="a7856-187">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="a7856-187">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-signalr-client-logging"></a><span data-ttu-id="a7856-188">Configurer la SignalR journalisation du client</span><span class="sxs-lookup"><span data-stu-id="a7856-188">Configure SignalR client logging</span></span>

<span data-ttu-id="a7856-189">Sur le générateur de clients, transmettez l' `configureSignalR` objet de configuration qui appelle `configureLogging` avec le niveau de journalisation.</span><span class="sxs-lookup"><span data-stu-id="a7856-189">On the client builder, pass in the `configureSignalR` configuration object that calls `configureLogging` with the log level.</span></span>

<span data-ttu-id="a7856-190">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="a7856-190">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="a7856-191">Dans l’exemple précédent, `information` est équivalent à un niveau de journal de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a7856-191">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="a7856-192">Modifier le gestionnaire de reconnexion</span><span class="sxs-lookup"><span data-stu-id="a7856-192">Modify the reconnection handler</span></span>

<span data-ttu-id="a7856-193">Les événements de connexion du circuit du gestionnaire de reconnexion peuvent être modifiés pour les comportements personnalisés, par exemple :</span><span class="sxs-lookup"><span data-stu-id="a7856-193">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="a7856-194">Pour avertir l’utilisateur si la connexion est abandonnée.</span><span class="sxs-lookup"><span data-stu-id="a7856-194">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="a7856-195">Pour effectuer la journalisation (à partir du client) lorsqu’un circuit est connecté.</span><span class="sxs-lookup"><span data-stu-id="a7856-195">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="a7856-196">Pour modifier les événements de connexion, enregistrez les rappels pour les modifications de connexion suivantes :</span><span class="sxs-lookup"><span data-stu-id="a7856-196">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="a7856-197">Utilisation de connexions abandonnées `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="a7856-197">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="a7856-198">Les connexions établies/rétablies utilisent `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="a7856-198">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="a7856-199">**`onConnectionDown`Et `onConnectionUp` doivent être spécifiés.**</span><span class="sxs-lookup"><span data-stu-id="a7856-199">**Both `onConnectionDown` and `onConnectionUp` must be specified.**</span></span>

<span data-ttu-id="a7856-200">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="a7856-200">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="a7856-201">Ajuster le nombre et l’intervalle de tentatives de reconnexion</span><span class="sxs-lookup"><span data-stu-id="a7856-201">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="a7856-202">Pour régler le nombre et l’intervalle de tentatives de reconnexion, définissez le nombre de nouvelles tentatives ( `maxRetries` ) et le délai (en millisecondes) autorisé pour chaque nouvelle tentative ( `retryIntervalMilliseconds` ).</span><span class="sxs-lookup"><span data-stu-id="a7856-202">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

<span data-ttu-id="a7856-203">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="a7856-203">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="a7856-204">Masquer ou remplacer l’affichage de reconnexion</span><span class="sxs-lookup"><span data-stu-id="a7856-204">Hide or replace the reconnection display</span></span>

<span data-ttu-id="a7856-205">Pour masquer l’affichage de reconnexion, définissez le gestionnaire de reconnexion `_reconnectionDisplay` sur un objet vide ( `{}` ou `new Object()` ).</span><span class="sxs-lookup"><span data-stu-id="a7856-205">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

<span data-ttu-id="a7856-206">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="a7856-206">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

<span data-ttu-id="a7856-207">Pour remplacer l’affichage de reconnexion, `_reconnectionDisplay` dans l’exemple précédent, définissez l’élément pour l’affichage :</span><span class="sxs-lookup"><span data-stu-id="a7856-207">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="a7856-208">L’espace réservé `{ELEMENT ID}` est l’ID de l’élément HTML à afficher.</span><span class="sxs-lookup"><span data-stu-id="a7856-208">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a7856-209">Personnalisez le délai avant que l’affichage de reconnexion ne s’affiche en définissant la `transition-delay` propriété dans le CSS du site pour l’élément modal.</span><span class="sxs-lookup"><span data-stu-id="a7856-209">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the site's CSS for the modal element.</span></span> <span data-ttu-id="a7856-210">L’exemple suivant définit le délai de transition de 500 ms (par défaut) à 1 000 MS (1 seconde).</span><span class="sxs-lookup"><span data-stu-id="a7856-210">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second).</span></span>

<span data-ttu-id="a7856-211">`wwwroot/css/site.css`:</span><span class="sxs-lookup"><span data-stu-id="a7856-211">`wwwroot/css/site.css`:</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="a7856-212">Déconnecter le Blazor circuit du client</span><span class="sxs-lookup"><span data-stu-id="a7856-212">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="a7856-213">Par défaut, un Blazor circuit est déconnecté lorsque l' [ `unload` événement de page](https://developer.mozilla.org/docs/Web/API/Window/unload_event) est déclenché.</span><span class="sxs-lookup"><span data-stu-id="a7856-213">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="a7856-214">Pour déconnecter le circuit d’autres scénarios sur le client, appelez `Blazor.disconnect` dans le gestionnaire d’événements approprié.</span><span class="sxs-lookup"><span data-stu-id="a7856-214">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="a7856-215">Dans l’exemple suivant, le circuit est déconnecté lorsque la page est masquée ([ `pagehide` événement](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) :</span><span class="sxs-lookup"><span data-stu-id="a7856-215">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a7856-216">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a7856-216">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="a7856-217">Blazor Server événements de reconnexion et événements de cycle de vie du composant</span><span class="sxs-lookup"><span data-stu-id="a7856-217">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)

::: zone-end
