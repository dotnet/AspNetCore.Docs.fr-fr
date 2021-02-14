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
ms.openlocfilehash: 3198f45819020ca551617aa12a146f2b8a9a9f8e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279858"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="ad6de-103">Blazor SignalR Aide ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ad6de-103">ASP.NET Core Blazor SignalR guidance</span></span>

<span data-ttu-id="ad6de-104">Pour obtenir des conseils généraux sur la configuration de ASP.NET Core SignalR , consultez les rubriques dans la <xref:signalr/introduction> section de la documentation.</span><span class="sxs-lookup"><span data-stu-id="ad6de-104">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="ad6de-105">Pour configurer l' SignalR [Ajout à une Blazor WebAssembly solution hébergée](xref:tutorials/signalr-blazor), consultez <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="ad6de-105">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="ad6de-106">Options du gestionnaire de circuit</span><span class="sxs-lookup"><span data-stu-id="ad6de-106">Circuit handler options</span></span>

<span data-ttu-id="ad6de-107">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="ad6de-107">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ad6de-108">Configurez le Blazor Server circuit comme <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> indiqué dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ad6de-108">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="ad6de-109">Option</span><span class="sxs-lookup"><span data-stu-id="ad6de-109">Option</span></span> | <span data-ttu-id="ad6de-110">Default</span><span class="sxs-lookup"><span data-stu-id="ad6de-110">Default</span></span> | <span data-ttu-id="ad6de-111">Description</span><span class="sxs-lookup"><span data-stu-id="ad6de-111">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="ad6de-112">Envoyer des messages d’exception détaillés à JavaScript lorsqu’une exception non gérée se produit sur le circuit ou lorsqu’un appel de méthode .NET via l’interopérabilité JS entraîne une exception.</span><span class="sxs-lookup"><span data-stu-id="ad6de-112">Send detailed exception messages to JavaScript when an unhandled exception happens on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="ad6de-113">100</span><span class="sxs-lookup"><span data-stu-id="ad6de-113">100</span></span> | <span data-ttu-id="ad6de-114">Nombre maximal de circuits déconnectés qu’un serveur donné détient en mémoire à la fois.</span><span class="sxs-lookup"><span data-stu-id="ad6de-114">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="ad6de-115">3 minutes</span><span class="sxs-lookup"><span data-stu-id="ad6de-115">3 minutes</span></span> | <span data-ttu-id="ad6de-116">Durée maximale pendant laquelle un circuit déconnecté est maintenu en mémoire avant d’être détruit.</span><span class="sxs-lookup"><span data-stu-id="ad6de-116">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="ad6de-117">1 minute</span><span class="sxs-lookup"><span data-stu-id="ad6de-117">1 minute</span></span> | <span data-ttu-id="ad6de-118">Durée maximale pendant laquelle le serveur attend avant d’expirer un appel de fonction JavaScript asynchrone.</span><span class="sxs-lookup"><span data-stu-id="ad6de-118">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="ad6de-119">10</span><span class="sxs-lookup"><span data-stu-id="ad6de-119">10</span></span> | <span data-ttu-id="ad6de-120">Nombre maximal de lots de rendu sans accusé de réception le serveur conserve en mémoire par circuit à un moment donné pour prendre en charge une reconnexion fiable.</span><span class="sxs-lookup"><span data-stu-id="ad6de-120">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="ad6de-121">Après avoir atteint la limite, le serveur cesse de produire de nouveaux lots de rendu jusqu’à ce qu’un ou plusieurs lots aient été reconnus par le client.</span><span class="sxs-lookup"><span data-stu-id="ad6de-121">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> |

<span data-ttu-id="ad6de-122">Configurez les options dans `Startup.ConfigureServices` avec un délégué d’options à <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> .</span><span class="sxs-lookup"><span data-stu-id="ad6de-122">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="ad6de-123">L’exemple suivant affecte les valeurs d’option par défaut indiquées dans le tableau précédent :</span><span class="sxs-lookup"><span data-stu-id="ad6de-123">The following example assigns the default option values shown in the preceding table:</span></span>

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

<span data-ttu-id="ad6de-124">Pour configurer le <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> , utilisez <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> avec <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> .</span><span class="sxs-lookup"><span data-stu-id="ad6de-124">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="ad6de-125">Pour obtenir une description des options, consultez <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="ad6de-125">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="ad6de-126">L’exemple suivant affecte les valeurs d’option par défaut :</span><span class="sxs-lookup"><span data-stu-id="ad6de-126">The following example assigns the default option values:</span></span>

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

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="ad6de-127">SignalR négociation Cross-Origin pour l’authentification</span><span class="sxs-lookup"><span data-stu-id="ad6de-127">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="ad6de-128">*Cette section s’applique à Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="ad6de-128">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="ad6de-129">Pour configurer le SignalR client sous-jacent pour envoyer des informations d’identification, telles que cookie les en-têtes s ou http authentication :</span><span class="sxs-lookup"><span data-stu-id="ad6de-129">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="ad6de-130">Utilisez <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> pour définir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> les demandes Cross-Origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="ad6de-130">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="ad6de-131">Affectez <xref:System.Net.Http.HttpMessageHandler> à l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option :</span><span class="sxs-lookup"><span data-stu-id="ad6de-131">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="ad6de-132">Pour plus d’informations, consultez <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="ad6de-132">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="ad6de-133">Refléter l’état de la connexion dans l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="ad6de-133">Reflect the connection state in the UI</span></span>

<span data-ttu-id="ad6de-134">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="ad6de-134">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ad6de-135">Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="ad6de-135">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="ad6de-136">En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.</span><span class="sxs-lookup"><span data-stu-id="ad6de-136">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="ad6de-137">Pour personnaliser l’interface utilisateur, définissez un élément avec un `id` de `components-reconnect-modal` dans le `<body>` de la `_Host.cshtml` Razor page :</span><span class="sxs-lookup"><span data-stu-id="ad6de-137">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="ad6de-138">Ajoutez le code suivant à la feuille de style de l’application ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ) :</span><span class="sxs-lookup"><span data-stu-id="ad6de-138">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="ad6de-139">Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément.</span><span class="sxs-lookup"><span data-stu-id="ad6de-139">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="ad6de-140">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="ad6de-140">CSS class</span></span>                       | <span data-ttu-id="ad6de-141">Détermine&hellip;</span><span class="sxs-lookup"><span data-stu-id="ad6de-141">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="ad6de-142">Connexion perdue.</span><span class="sxs-lookup"><span data-stu-id="ad6de-142">A lost connection.</span></span> <span data-ttu-id="ad6de-143">Le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="ad6de-143">The client is attempting to reconnect.</span></span> <span data-ttu-id="ad6de-144">Affichez le modal.</span><span class="sxs-lookup"><span data-stu-id="ad6de-144">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="ad6de-145">Une connexion active est rétablie sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="ad6de-145">An active connection is re-established to the server.</span></span> <span data-ttu-id="ad6de-146">Masquez le modal.</span><span class="sxs-lookup"><span data-stu-id="ad6de-146">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="ad6de-147">Échec de la reconnexion, probablement en raison d’une défaillance du réseau.</span><span class="sxs-lookup"><span data-stu-id="ad6de-147">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="ad6de-148">Pour tenter une reconnexion, appelez `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="ad6de-148">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="ad6de-149">Reconnexion refusée.</span><span class="sxs-lookup"><span data-stu-id="ad6de-149">Reconnection rejected.</span></span> <span data-ttu-id="ad6de-150">Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu.</span><span class="sxs-lookup"><span data-stu-id="ad6de-150">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="ad6de-151">Pour recharger l’application, appelez `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="ad6de-151">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="ad6de-152">Cet état de connexion peut se produire dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="ad6de-152">This connection state may result when:</span></span><ul><li><span data-ttu-id="ad6de-153">Un blocage dans le circuit côté serveur se produit.</span><span class="sxs-lookup"><span data-stu-id="ad6de-153">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="ad6de-154">Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ad6de-154">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="ad6de-155">Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="ad6de-155">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="ad6de-156">Le serveur est redémarré ou le processus de travail de l’application est recyclé.</span><span class="sxs-lookup"><span data-stu-id="ad6de-156">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="ad6de-157">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="ad6de-157">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ad6de-158">*Cette section s’applique à Hosted Blazor WebAssembly et à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="ad6de-158">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="ad6de-159">Blazor les applications sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="ad6de-159">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="ad6de-160">Pour plus d’informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="ad6de-160">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ad6de-161">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="ad6de-161">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ad6de-162">Blazor Server les applications sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie.</span><span class="sxs-lookup"><span data-stu-id="ad6de-162">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="ad6de-163">Pour plus d’informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="ad6de-163">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="ad6de-164">Initialiser le Blazor circuit</span><span class="sxs-lookup"><span data-stu-id="ad6de-164">Initialize the Blazor circuit</span></span>

<span data-ttu-id="ad6de-165">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="ad6de-165">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ad6de-166">Configurez le démarrage manuel du Blazor Server [ SignalR circuit](xref:blazor/hosting-models#circuits) d’une application dans le `Pages/_Host.cshtml` fichier :</span><span class="sxs-lookup"><span data-stu-id="ad6de-166">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="ad6de-167">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="ad6de-167">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="ad6de-168">Placez un script qui appelle `Blazor.start` après la `blazor.server.js` balise du script et à l’intérieur de la `</body>` balise de fermeture.</span><span class="sxs-lookup"><span data-stu-id="ad6de-168">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="ad6de-169">Lorsque `autostart` est désactivé, tous les aspects de l’application qui ne dépendent pas du circuit fonctionnent normalement.</span><span class="sxs-lookup"><span data-stu-id="ad6de-169">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="ad6de-170">Par exemple, le routage côté client est opérationnel.</span><span class="sxs-lookup"><span data-stu-id="ad6de-170">For example, client-side routing is operational.</span></span> <span data-ttu-id="ad6de-171">Toutefois, tous les aspects qui dépendent du circuit ne sont pas opérationnels tant que `Blazor.start` n’est pas appelé.</span><span class="sxs-lookup"><span data-stu-id="ad6de-171">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="ad6de-172">Le comportement de l’application n’est pas prévisible sans circuit établi.</span><span class="sxs-lookup"><span data-stu-id="ad6de-172">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="ad6de-173">Par exemple, les méthodes de composant ne peuvent pas s’exécuter pendant que le circuit est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="ad6de-173">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="ad6de-174">Initialiser Blazor lorsque le document est prêt</span><span class="sxs-lookup"><span data-stu-id="ad6de-174">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="ad6de-175">Pour initialiser l' Blazor application lorsque le document est prêt :</span><span class="sxs-lookup"><span data-stu-id="ad6de-175">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="ad6de-176">Chaîne à `Promise` qui résulte d’un démarrage manuel</span><span class="sxs-lookup"><span data-stu-id="ad6de-176">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="ad6de-177">Pour effectuer des tâches supplémentaires, telles que l’initialisation de l’interopérabilité JS, utilisez `then` pour chaîner à `Promise` qui résulte d’un démarrage manuel de l' Blazor application :</span><span class="sxs-lookup"><span data-stu-id="ad6de-177">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-signalr-client"></a><span data-ttu-id="ad6de-178">Configurer le SignalR client</span><span class="sxs-lookup"><span data-stu-id="ad6de-178">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="ad6de-179">Journalisation</span><span class="sxs-lookup"><span data-stu-id="ad6de-179">Logging</span></span>

<span data-ttu-id="ad6de-180">Pour configurer SignalR la journalisation du client, transmettez un objet de configuration ( `configureSignalR` ) qui appelle `configureLogging` avec le niveau de journalisation sur le générateur client :</span><span class="sxs-lookup"><span data-stu-id="ad6de-180">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="ad6de-181">Dans l’exemple précédent, `information` est équivalent à un niveau de journal de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ad6de-181">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="ad6de-182">Modifier le gestionnaire de reconnexion</span><span class="sxs-lookup"><span data-stu-id="ad6de-182">Modify the reconnection handler</span></span>

<span data-ttu-id="ad6de-183">Les événements de connexion du circuit du gestionnaire de reconnexion peuvent être modifiés pour les comportements personnalisés, par exemple :</span><span class="sxs-lookup"><span data-stu-id="ad6de-183">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="ad6de-184">Pour avertir l’utilisateur si la connexion est abandonnée.</span><span class="sxs-lookup"><span data-stu-id="ad6de-184">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="ad6de-185">Pour effectuer la journalisation (à partir du client) lorsqu’un circuit est connecté.</span><span class="sxs-lookup"><span data-stu-id="ad6de-185">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="ad6de-186">Pour modifier les événements de connexion, enregistrez les rappels pour les modifications de connexion suivantes :</span><span class="sxs-lookup"><span data-stu-id="ad6de-186">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="ad6de-187">Utilisation de connexions abandonnées `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="ad6de-187">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="ad6de-188">Les connexions établies/rétablies utilisent `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="ad6de-188">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="ad6de-189">**Les deux** `onConnectionDown` et `onConnectionUp` doivent être spécifiés :</span><span class="sxs-lookup"><span data-stu-id="ad6de-189">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="ad6de-190">Ajuster le nombre et l’intervalle de tentatives de reconnexion</span><span class="sxs-lookup"><span data-stu-id="ad6de-190">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="ad6de-191">Pour régler le nombre et l’intervalle de tentatives de reconnexion, définissez le nombre de nouvelles tentatives ( `maxRetries` ) et le délai (en millisecondes) autorisé pour chaque nouvelle tentative ( `retryIntervalMilliseconds` ) :</span><span class="sxs-lookup"><span data-stu-id="ad6de-191">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="ad6de-192">Masquer ou remplacer l’affichage de reconnexion</span><span class="sxs-lookup"><span data-stu-id="ad6de-192">Hide or replace the reconnection display</span></span>

<span data-ttu-id="ad6de-193">Pour masquer l’affichage de reconnexion, définissez le gestionnaire de reconnexion `_reconnectionDisplay` sur un objet vide ( `{}` ou `new Object()` ) :</span><span class="sxs-lookup"><span data-stu-id="ad6de-193">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="ad6de-194">Pour remplacer l’affichage de reconnexion, `_reconnectionDisplay` dans l’exemple précédent, définissez l’élément pour l’affichage :</span><span class="sxs-lookup"><span data-stu-id="ad6de-194">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="ad6de-195">L’espace réservé `{ELEMENT ID}` est l’ID de l’élément HTML à afficher.</span><span class="sxs-lookup"><span data-stu-id="ad6de-195">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ad6de-196">Personnalisez le délai avant que l’affichage de reconnexion ne s’affiche en définissant la `transition-delay` propriété dans le CSS () de l’application `wwwroot/css/site.css` pour l’élément modal.</span><span class="sxs-lookup"><span data-stu-id="ad6de-196">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="ad6de-197">L’exemple suivant définit le délai de transition de 500 ms (par défaut) à 1 000 MS (1 seconde) :</span><span class="sxs-lookup"><span data-stu-id="ad6de-197">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="ad6de-198">Déconnecter le Blazor circuit du client</span><span class="sxs-lookup"><span data-stu-id="ad6de-198">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="ad6de-199">Par défaut, un Blazor circuit est déconnecté lorsque l' [ `unload` événement de page](https://developer.mozilla.org/docs/Web/API/Window/unload_event) est déclenché.</span><span class="sxs-lookup"><span data-stu-id="ad6de-199">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="ad6de-200">Pour déconnecter le circuit d’autres scénarios sur le client, appelez `Blazor.disconnect` dans le gestionnaire d’événements approprié.</span><span class="sxs-lookup"><span data-stu-id="ad6de-200">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="ad6de-201">Dans l’exemple suivant, le circuit est déconnecté lorsque la page est masquée ([ `pagehide` événement](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) :</span><span class="sxs-lookup"><span data-stu-id="ad6de-201">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ad6de-202">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ad6de-202">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="ad6de-203">Blazor Server événements de reconnexion et événements de cycle de vie du composant</span><span class="sxs-lookup"><span data-stu-id="ad6de-203">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
