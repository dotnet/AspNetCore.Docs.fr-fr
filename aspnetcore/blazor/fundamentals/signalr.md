---
title: Blazor SignalR Aide ASP.net Core
author: guardrex
description: Découvrez comment configurer et gérer les Blazor SignalR connexions.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
ms.openlocfilehash: 31cd68baa0fbc773f09fe3a1b37091b2dfc0b0a0
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100110037"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="2928f-103">Blazor SignalR Aide ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="2928f-103">ASP.NET Core Blazor SignalR guidance</span></span>

<span data-ttu-id="2928f-104">Par [Daniel Roth](https://github.com/danroth27), [MacKinnon argent](https://github.com/MackinnonBuck)et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2928f-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2928f-105">Pour obtenir des conseils généraux sur la configuration de ASP.NET Core SignalR , consultez les rubriques dans la <xref:signalr/introduction> section de la documentation.</span><span class="sxs-lookup"><span data-stu-id="2928f-105">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="2928f-106">Pour configurer l' SignalR [Ajout à une Blazor WebAssembly solution hébergée](xref:tutorials/signalr-blazor), consultez <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="2928f-106">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="2928f-107">Options du gestionnaire de circuit</span><span class="sxs-lookup"><span data-stu-id="2928f-107">Circuit handler options</span></span>

<span data-ttu-id="2928f-108">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="2928f-108">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="2928f-109">Configurez le Blazor Server circuit comme <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> indiqué dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="2928f-109">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="2928f-110">Option</span><span class="sxs-lookup"><span data-stu-id="2928f-110">Option</span></span> | <span data-ttu-id="2928f-111">Default</span><span class="sxs-lookup"><span data-stu-id="2928f-111">Default</span></span> | <span data-ttu-id="2928f-112">Description</span><span class="sxs-lookup"><span data-stu-id="2928f-112">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="2928f-113">Envoyer des messages d’exception détaillés à JavaScript lorsqu’une exception non gérée se produit sur le circuit ou lorsqu’un appel de méthode .NET via l’interopérabilité JS entraîne une exception.</span><span class="sxs-lookup"><span data-stu-id="2928f-113">Send detailed exception messages to JavaScript when an unhandled exception happens on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="2928f-114">100</span><span class="sxs-lookup"><span data-stu-id="2928f-114">100</span></span> | <span data-ttu-id="2928f-115">Nombre maximal de circuits déconnectés qu’un serveur donné détient en mémoire à la fois.</span><span class="sxs-lookup"><span data-stu-id="2928f-115">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="2928f-116">3 minutes</span><span class="sxs-lookup"><span data-stu-id="2928f-116">3 minutes</span></span> | <span data-ttu-id="2928f-117">Durée maximale pendant laquelle un circuit déconnecté est maintenu en mémoire avant d’être détruit.</span><span class="sxs-lookup"><span data-stu-id="2928f-117">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="2928f-118">1 minute</span><span class="sxs-lookup"><span data-stu-id="2928f-118">1 minute</span></span> | <span data-ttu-id="2928f-119">Durée maximale pendant laquelle le serveur attend avant d’expirer un appel de fonction JavaScript asynchrone.</span><span class="sxs-lookup"><span data-stu-id="2928f-119">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="2928f-120">10</span><span class="sxs-lookup"><span data-stu-id="2928f-120">10</span></span> | <span data-ttu-id="2928f-121">Nombre maximal de lots de rendu sans accusé de réception le serveur conserve en mémoire par circuit à un moment donné pour prendre en charge une reconnexion fiable.</span><span class="sxs-lookup"><span data-stu-id="2928f-121">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="2928f-122">Après avoir atteint la limite, le serveur cesse de produire de nouveaux lots de rendu jusqu’à ce qu’un ou plusieurs lots aient été reconnus par le client.</span><span class="sxs-lookup"><span data-stu-id="2928f-122">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> |

<span data-ttu-id="2928f-123">Configurez les options dans `Startup.ConfigureServices` avec un délégué d’options à <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> .</span><span class="sxs-lookup"><span data-stu-id="2928f-123">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="2928f-124">L’exemple suivant affecte les valeurs d’option par défaut indiquées dans le tableau précédent :</span><span class="sxs-lookup"><span data-stu-id="2928f-124">The following example assigns the default option values shown in the preceding table:</span></span>

```csharp
using System;

...

services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

<span data-ttu-id="2928f-125">Pour configurer le <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> , utilisez <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> avec <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> .</span><span class="sxs-lookup"><span data-stu-id="2928f-125">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="2928f-126">Pour obtenir une description des options, consultez <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="2928f-126">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="2928f-127">L’exemple suivant affecte les valeurs d’option par défaut :</span><span class="sxs-lookup"><span data-stu-id="2928f-127">The following example assigns the default option values:</span></span>

```csharp
using System;

...

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

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="2928f-128">SignalR négociation Cross-Origin pour l’authentification</span><span class="sxs-lookup"><span data-stu-id="2928f-128">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="2928f-129">*Cette section s’applique à Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="2928f-129">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="2928f-130">Pour configurer le SignalR client sous-jacent pour envoyer des informations d’identification, telles que cookie les en-têtes s ou http authentication :</span><span class="sxs-lookup"><span data-stu-id="2928f-130">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="2928f-131">Utilisez <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> pour définir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> les demandes Cross-Origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="2928f-131">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
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

* <span data-ttu-id="2928f-132">Affectez <xref:System.Net.Http.HttpMessageHandler> à l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option :</span><span class="sxs-lookup"><span data-stu-id="2928f-132">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="2928f-133">Pour plus d’informations, consultez <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="2928f-133">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="2928f-134">Refléter l’état de la connexion dans l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="2928f-134">Reflect the connection state in the UI</span></span>

<span data-ttu-id="2928f-135">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="2928f-135">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="2928f-136">Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="2928f-136">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="2928f-137">En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.</span><span class="sxs-lookup"><span data-stu-id="2928f-137">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="2928f-138">Pour personnaliser l’interface utilisateur, définissez un élément avec un `id` de `components-reconnect-modal` dans le `<body>` de la `_Host.cshtml` Razor page :</span><span class="sxs-lookup"><span data-stu-id="2928f-138">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="2928f-139">Ajoutez le code suivant à la feuille de style de l’application ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ) :</span><span class="sxs-lookup"><span data-stu-id="2928f-139">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="2928f-140">Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément.</span><span class="sxs-lookup"><span data-stu-id="2928f-140">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="2928f-141">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="2928f-141">CSS class</span></span>                       | <span data-ttu-id="2928f-142">Détermine&hellip;</span><span class="sxs-lookup"><span data-stu-id="2928f-142">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="2928f-143">Connexion perdue.</span><span class="sxs-lookup"><span data-stu-id="2928f-143">A lost connection.</span></span> <span data-ttu-id="2928f-144">Le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="2928f-144">The client is attempting to reconnect.</span></span> <span data-ttu-id="2928f-145">Affichez le modal.</span><span class="sxs-lookup"><span data-stu-id="2928f-145">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="2928f-146">Une connexion active est rétablie sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="2928f-146">An active connection is re-established to the server.</span></span> <span data-ttu-id="2928f-147">Masquez le modal.</span><span class="sxs-lookup"><span data-stu-id="2928f-147">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="2928f-148">Échec de la reconnexion, probablement en raison d’une défaillance du réseau.</span><span class="sxs-lookup"><span data-stu-id="2928f-148">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="2928f-149">Pour tenter une reconnexion, appelez `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="2928f-149">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="2928f-150">Reconnexion refusée.</span><span class="sxs-lookup"><span data-stu-id="2928f-150">Reconnection rejected.</span></span> <span data-ttu-id="2928f-151">Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu.</span><span class="sxs-lookup"><span data-stu-id="2928f-151">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="2928f-152">Pour recharger l’application, appelez `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="2928f-152">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="2928f-153">Cet état de connexion peut se produire dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="2928f-153">This connection state may result when:</span></span><ul><li><span data-ttu-id="2928f-154">Un blocage dans le circuit côté serveur se produit.</span><span class="sxs-lookup"><span data-stu-id="2928f-154">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="2928f-155">Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="2928f-155">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="2928f-156">Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="2928f-156">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="2928f-157">Le serveur est redémarré ou le processus de travail de l’application est recyclé.</span><span class="sxs-lookup"><span data-stu-id="2928f-157">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="2928f-158">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="2928f-158">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2928f-159">*Cette section s’applique à Hosted Blazor WebAssembly et à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="2928f-159">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="2928f-160">Blazor les applications sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="2928f-160">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="2928f-161">Pour plus d’informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="2928f-161">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="2928f-162">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="2928f-162">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="2928f-163">Blazor Server les applications sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie.</span><span class="sxs-lookup"><span data-stu-id="2928f-163">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="2928f-164">Pour plus d’informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="2928f-164">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="2928f-165">Initialiser le Blazor circuit</span><span class="sxs-lookup"><span data-stu-id="2928f-165">Initialize the Blazor circuit</span></span>

<span data-ttu-id="2928f-166">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="2928f-166">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="2928f-167">Configurez le démarrage manuel du Blazor Server [ SignalR circuit](xref:blazor/hosting-models#circuits) d’une application dans le `Pages/_Host.cshtml` fichier :</span><span class="sxs-lookup"><span data-stu-id="2928f-167">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="2928f-168">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="2928f-168">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="2928f-169">Placez un script qui appelle `Blazor.start` après la `blazor.server.js` balise du script et à l’intérieur de la `</body>` balise de fermeture.</span><span class="sxs-lookup"><span data-stu-id="2928f-169">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="2928f-170">Lorsque `autostart` est désactivé, tous les aspects de l’application qui ne dépendent pas du circuit fonctionnent normalement.</span><span class="sxs-lookup"><span data-stu-id="2928f-170">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="2928f-171">Par exemple, le routage côté client est opérationnel.</span><span class="sxs-lookup"><span data-stu-id="2928f-171">For example, client-side routing is operational.</span></span> <span data-ttu-id="2928f-172">Toutefois, tous les aspects qui dépendent du circuit ne sont pas opérationnels tant que `Blazor.start` n’est pas appelé.</span><span class="sxs-lookup"><span data-stu-id="2928f-172">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="2928f-173">Le comportement de l’application n’est pas prévisible sans circuit établi.</span><span class="sxs-lookup"><span data-stu-id="2928f-173">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="2928f-174">Par exemple, les méthodes de composant ne peuvent pas s’exécuter pendant que le circuit est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="2928f-174">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="2928f-175">Initialiser Blazor lorsque le document est prêt</span><span class="sxs-lookup"><span data-stu-id="2928f-175">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="2928f-176">Pour initialiser l' Blazor application lorsque le document est prêt :</span><span class="sxs-lookup"><span data-stu-id="2928f-176">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="2928f-177">Chaîne à `Promise` qui résulte d’un démarrage manuel</span><span class="sxs-lookup"><span data-stu-id="2928f-177">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="2928f-178">Pour effectuer des tâches supplémentaires, telles que l’initialisation de l’interopérabilité JS, utilisez `then` pour chaîner à `Promise` qui résulte d’un démarrage manuel de l' Blazor application :</span><span class="sxs-lookup"><span data-stu-id="2928f-178">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-signalr-client"></a><span data-ttu-id="2928f-179">Configurer le SignalR client</span><span class="sxs-lookup"><span data-stu-id="2928f-179">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="2928f-180">Journalisation</span><span class="sxs-lookup"><span data-stu-id="2928f-180">Logging</span></span>

<span data-ttu-id="2928f-181">Pour configurer SignalR la journalisation du client, transmettez un objet de configuration ( `configureSignalR` ) qui appelle `configureLogging` avec le niveau de journalisation sur le générateur client :</span><span class="sxs-lookup"><span data-stu-id="2928f-181">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="2928f-182">Dans l’exemple précédent, `information` est équivalent à un niveau de journal de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="2928f-182">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="2928f-183">Modifier le gestionnaire de reconnexion</span><span class="sxs-lookup"><span data-stu-id="2928f-183">Modify the reconnection handler</span></span>

<span data-ttu-id="2928f-184">Les événements de connexion du circuit du gestionnaire de reconnexion peuvent être modifiés pour les comportements personnalisés, par exemple :</span><span class="sxs-lookup"><span data-stu-id="2928f-184">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="2928f-185">Pour avertir l’utilisateur si la connexion est abandonnée.</span><span class="sxs-lookup"><span data-stu-id="2928f-185">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="2928f-186">Pour effectuer la journalisation (à partir du client) lorsqu’un circuit est connecté.</span><span class="sxs-lookup"><span data-stu-id="2928f-186">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="2928f-187">Pour modifier les événements de connexion, enregistrez les rappels pour les modifications de connexion suivantes :</span><span class="sxs-lookup"><span data-stu-id="2928f-187">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="2928f-188">Utilisation de connexions abandonnées `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="2928f-188">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="2928f-189">Les connexions établies/rétablies utilisent `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="2928f-189">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="2928f-190">**Les deux** `onConnectionDown` et `onConnectionUp` doivent être spécifiés :</span><span class="sxs-lookup"><span data-stu-id="2928f-190">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="2928f-191">Ajuster le nombre et l’intervalle de tentatives de reconnexion</span><span class="sxs-lookup"><span data-stu-id="2928f-191">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="2928f-192">Pour régler le nombre et l’intervalle de tentatives de reconnexion, définissez le nombre de nouvelles tentatives ( `maxRetries` ) et le délai (en millisecondes) autorisé pour chaque nouvelle tentative ( `retryIntervalMilliseconds` ) :</span><span class="sxs-lookup"><span data-stu-id="2928f-192">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="2928f-193">Masquer ou remplacer l’affichage de reconnexion</span><span class="sxs-lookup"><span data-stu-id="2928f-193">Hide or replace the reconnection display</span></span>

<span data-ttu-id="2928f-194">Pour masquer l’affichage de reconnexion, définissez le gestionnaire de reconnexion `_reconnectionDisplay` sur un objet vide ( `{}` ou `new Object()` ) :</span><span class="sxs-lookup"><span data-stu-id="2928f-194">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="2928f-195">Pour remplacer l’affichage de reconnexion, `_reconnectionDisplay` dans l’exemple précédent, définissez l’élément pour l’affichage :</span><span class="sxs-lookup"><span data-stu-id="2928f-195">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="2928f-196">L’espace réservé `{ELEMENT ID}` est l’ID de l’élément HTML à afficher.</span><span class="sxs-lookup"><span data-stu-id="2928f-196">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2928f-197">Personnalisez le délai avant que l’affichage de reconnexion ne s’affiche en définissant la `transition-delay` propriété dans le CSS () de l’application `wwwroot/css/site.css` pour l’élément modal.</span><span class="sxs-lookup"><span data-stu-id="2928f-197">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="2928f-198">L’exemple suivant définit le délai de transition de 500 ms (par défaut) à 1 000 MS (1 seconde) :</span><span class="sxs-lookup"><span data-stu-id="2928f-198">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="2928f-199">Déconnecter le Blazor circuit du client</span><span class="sxs-lookup"><span data-stu-id="2928f-199">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="2928f-200">Par défaut, un Blazor circuit est déconnecté lorsque l' [ `unload` événement de page](https://developer.mozilla.org/docs/Web/API/Window/unload_event) est déclenché.</span><span class="sxs-lookup"><span data-stu-id="2928f-200">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="2928f-201">Pour déconnecter le circuit d’autres scénarios sur le client, appelez `Blazor.disconnect` dans le gestionnaire d’événements approprié.</span><span class="sxs-lookup"><span data-stu-id="2928f-201">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="2928f-202">Dans l’exemple suivant, le circuit est déconnecté lorsque la page est masquée ([ `pagehide` événement](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) :</span><span class="sxs-lookup"><span data-stu-id="2928f-202">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

THIS WILL BE MOVED TO ANOTHER TOPIC WHEN RE-ACTIVATED.

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2928f-203">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2928f-203">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="2928f-204">Blazor Server événements de reconnexion et événements de cycle de vie du composant</span><span class="sxs-lookup"><span data-stu-id="2928f-204">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
