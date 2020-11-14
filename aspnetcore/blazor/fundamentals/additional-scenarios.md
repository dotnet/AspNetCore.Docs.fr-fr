---
title: :::no-loc(Blazor):::Configuration du modèle d’hébergement ASP.net Core
author: guardrex
description: 'En savoir plus sur les scénarios supplémentaires pour ASP.NET Core la :::no-loc(Blazor)::: configuration du modèle d’hébergement.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 9ec3900b67897255e321462b83117f5c2e32ab45
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595413"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="d7f16-103">:::no-loc(Blazor):::Configuration du modèle d’hébergement ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="d7f16-103">ASP.NET Core :::no-loc(Blazor)::: hosting model configuration</span></span>

<span data-ttu-id="d7f16-104">Par [Daniel Roth](https://github.com/danroth27), [MacKinnon argent](https://github.com/MackinnonBuck)et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d7f16-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d7f16-105">Cet article traite de l’hébergement de la configuration du modèle.</span><span class="sxs-lookup"><span data-stu-id="d7f16-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="d7f16-106">:::no-loc(SignalR)::: négociation Cross-Origin pour l’authentification</span><span class="sxs-lookup"><span data-stu-id="d7f16-106">:::no-loc(SignalR)::: cross-origin negotiation for authentication</span></span>

<span data-ttu-id="d7f16-107">*Cette section s’applique à :::no-loc(Blazor WebAssembly)::: .*</span><span class="sxs-lookup"><span data-stu-id="d7f16-107">*This section applies to :::no-loc(Blazor WebAssembly):::.*</span></span>

<span data-ttu-id="d7f16-108">Pour configurer le :::no-loc(SignalR)::: client sous-jacent pour envoyer des informations d’identification, telles que :::no-loc(cookie)::: les en-têtes s ou http authentication :</span><span class="sxs-lookup"><span data-stu-id="d7f16-108">To configure :::no-loc(SignalR):::'s underlying client to send credentials, such as :::no-loc(cookie):::s or HTTP authentication headers:</span></span>

* <span data-ttu-id="d7f16-109">Utilisez <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> pour définir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> les demandes Cross-Origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="d7f16-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="d7f16-110">Affectez <xref:System.Net.Http.HttpMessageHandler> à l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option :</span><span class="sxs-lookup"><span data-stu-id="d7f16-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="d7f16-111">Pour plus d'informations, consultez <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="d7f16-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="d7f16-112">Refléter l’état de la connexion dans l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="d7f16-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="d7f16-113">*Cette section s’applique à :::no-loc(Blazor Server)::: .*</span><span class="sxs-lookup"><span data-stu-id="d7f16-113">*This section applies to :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="d7f16-114">Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="d7f16-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="d7f16-115">En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.</span><span class="sxs-lookup"><span data-stu-id="d7f16-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="d7f16-116">Pour personnaliser l’interface utilisateur, définissez un élément avec un `id` de `components-reconnect-modal` dans le `<body>` de la `_Host.cshtml` :::no-loc(Razor)::: page :</span><span class="sxs-lookup"><span data-stu-id="d7f16-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` :::no-loc(Razor)::: page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="d7f16-117">Ajoutez le code suivant à la feuille de style de l’application ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ) :</span><span class="sxs-lookup"><span data-stu-id="d7f16-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="d7f16-118">Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément.</span><span class="sxs-lookup"><span data-stu-id="d7f16-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="d7f16-119">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="d7f16-119">CSS class</span></span>                       | <span data-ttu-id="d7f16-120">Détermine&hellip;</span><span class="sxs-lookup"><span data-stu-id="d7f16-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="d7f16-121">Connexion perdue.</span><span class="sxs-lookup"><span data-stu-id="d7f16-121">A lost connection.</span></span> <span data-ttu-id="d7f16-122">Le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="d7f16-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="d7f16-123">Affichez le modal.</span><span class="sxs-lookup"><span data-stu-id="d7f16-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="d7f16-124">Une connexion active est rétablie sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="d7f16-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="d7f16-125">Masquez le modal.</span><span class="sxs-lookup"><span data-stu-id="d7f16-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="d7f16-126">Échec de la reconnexion, probablement en raison d’une défaillance du réseau.</span><span class="sxs-lookup"><span data-stu-id="d7f16-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="d7f16-127">Pour tenter une reconnexion, appelez `window.:::no-loc(Blazor):::.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="d7f16-127">To attempt reconnection, call `window.:::no-loc(Blazor):::.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="d7f16-128">Reconnexion refusée.</span><span class="sxs-lookup"><span data-stu-id="d7f16-128">Reconnection rejected.</span></span> <span data-ttu-id="d7f16-129">Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu.</span><span class="sxs-lookup"><span data-stu-id="d7f16-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="d7f16-130">Pour recharger l’application, appelez `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="d7f16-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="d7f16-131">Cet état de connexion peut se produire dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="d7f16-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="d7f16-132">Un blocage dans le circuit côté serveur se produit.</span><span class="sxs-lookup"><span data-stu-id="d7f16-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="d7f16-133">Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d7f16-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="d7f16-134">Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="d7f16-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="d7f16-135">Le serveur est redémarré ou le processus de travail de l’application est recyclé.</span><span class="sxs-lookup"><span data-stu-id="d7f16-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="d7f16-136">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="d7f16-136">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d7f16-137">*Cette section s’applique à Hosted :::no-loc(Blazor WebAssembly)::: et à :::no-loc(Blazor Server)::: .*</span><span class="sxs-lookup"><span data-stu-id="d7f16-137">*This section applies to hosted :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="d7f16-138">:::no-loc(Blazor)::: les applications sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="d7f16-138">:::no-loc(Blazor)::: apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="d7f16-139">Pour plus d'informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="d7f16-139">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d7f16-140">*Cette section s’applique à :::no-loc(Blazor Server)::: .*</span><span class="sxs-lookup"><span data-stu-id="d7f16-140">*This section applies to :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="d7f16-141">:::no-loc(Blazor Server)::: les applications sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie.</span><span class="sxs-lookup"><span data-stu-id="d7f16-141">:::no-loc(Blazor Server)::: apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="d7f16-142">Pour plus d'informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="d7f16-142">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="d7f16-143">Initialiser le :::no-loc(Blazor)::: circuit</span><span class="sxs-lookup"><span data-stu-id="d7f16-143">Initialize the :::no-loc(Blazor)::: circuit</span></span>

<span data-ttu-id="d7f16-144">*Cette section s’applique à :::no-loc(Blazor Server)::: .*</span><span class="sxs-lookup"><span data-stu-id="d7f16-144">*This section applies to :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="d7f16-145">Configurez le démarrage manuel du :::no-loc(Blazor Server)::: [ :::no-loc(SignalR)::: circuit](xref:blazor/hosting-models#circuits) d’une application dans le `Pages/_Host.cshtml` fichier :</span><span class="sxs-lookup"><span data-stu-id="d7f16-145">Configure the manual start of a :::no-loc(Blazor Server)::: app's [:::no-loc(SignalR)::: circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="d7f16-146">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="d7f16-146">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="d7f16-147">Placez un script qui appelle `:::no-loc(Blazor):::.start` après la `blazor.server.js` balise du script et à l’intérieur de la `</body>` balise de fermeture.</span><span class="sxs-lookup"><span data-stu-id="d7f16-147">Place a script that calls `:::no-loc(Blazor):::.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="d7f16-148">Lorsque `autostart` est désactivé, tous les aspects de l’application qui ne dépendent pas du circuit fonctionnent normalement.</span><span class="sxs-lookup"><span data-stu-id="d7f16-148">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="d7f16-149">Par exemple, le routage côté client est opérationnel.</span><span class="sxs-lookup"><span data-stu-id="d7f16-149">For example, client-side routing is operational.</span></span> <span data-ttu-id="d7f16-150">Toutefois, tous les aspects qui dépendent du circuit ne sont pas opérationnels tant que `:::no-loc(Blazor):::.start` n’est pas appelé.</span><span class="sxs-lookup"><span data-stu-id="d7f16-150">However, any aspect that depends on the circuit isn't operational until `:::no-loc(Blazor):::.start` is called.</span></span> <span data-ttu-id="d7f16-151">Le comportement de l’application n’est pas prévisible sans circuit établi.</span><span class="sxs-lookup"><span data-stu-id="d7f16-151">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="d7f16-152">Par exemple, les méthodes de composant ne peuvent pas s’exécuter pendant que le circuit est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="d7f16-152">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="d7f16-153">Initialiser :::no-loc(Blazor)::: lorsque le document est prêt</span><span class="sxs-lookup"><span data-stu-id="d7f16-153">Initialize :::no-loc(Blazor)::: when the document is ready</span></span>

<span data-ttu-id="d7f16-154">Pour initialiser l' :::no-loc(Blazor)::: application lorsque le document est prêt :</span><span class="sxs-lookup"><span data-stu-id="d7f16-154">To initialize the :::no-loc(Blazor)::: app when the document is ready:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        :::no-loc(Blazor):::.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="d7f16-155">Chaîne à `Promise` qui résulte d’un démarrage manuel</span><span class="sxs-lookup"><span data-stu-id="d7f16-155">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="d7f16-156">Pour effectuer des tâches supplémentaires, telles que l’initialisation de l’interopérabilité JS, utilisez `then` pour chaîner à `Promise` qui résulte d’un démarrage manuel de l' :::no-loc(Blazor)::: application :</span><span class="sxs-lookup"><span data-stu-id="d7f16-156">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual :::no-loc(Blazor)::: app start:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      :::no-loc(Blazor):::.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="d7f16-157">Configurer le :::no-loc(SignalR)::: client</span><span class="sxs-lookup"><span data-stu-id="d7f16-157">Configure the :::no-loc(SignalR)::: client</span></span>

#### <a name="logging"></a><span data-ttu-id="d7f16-158">Journalisation</span><span class="sxs-lookup"><span data-stu-id="d7f16-158">Logging</span></span>

<span data-ttu-id="d7f16-159">Pour configurer :::no-loc(SignalR)::: la journalisation du client, transmettez un objet de configuration ( `configure:::no-loc(SignalR):::` ) qui appelle `configureLogging` avec le niveau de journalisation sur le générateur client :</span><span class="sxs-lookup"><span data-stu-id="d7f16-159">To configure :::no-loc(SignalR)::: client logging, pass in a configuration object (`configure:::no-loc(SignalR):::`) that calls `configureLogging` with the log level on the client builder:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      :::no-loc(Blazor):::.start({
        configure:::no-loc(SignalR):::: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="d7f16-160">Dans l’exemple précédent, `information` est équivalent à un niveau de journal de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d7f16-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="d7f16-161">Modifier le gestionnaire de reconnexion</span><span class="sxs-lookup"><span data-stu-id="d7f16-161">Modify the reconnection handler</span></span>

<span data-ttu-id="d7f16-162">Les événements de connexion du circuit du gestionnaire de reconnexion peuvent être modifiés pour les comportements personnalisés, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d7f16-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="d7f16-163">Pour avertir l’utilisateur si la connexion est abandonnée.</span><span class="sxs-lookup"><span data-stu-id="d7f16-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="d7f16-164">Pour effectuer la journalisation (à partir du client) lorsqu’un circuit est connecté.</span><span class="sxs-lookup"><span data-stu-id="d7f16-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="d7f16-165">Pour modifier les événements de connexion, enregistrez les rappels pour les modifications de connexion suivantes :</span><span class="sxs-lookup"><span data-stu-id="d7f16-165">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="d7f16-166">Utilisation de connexions abandonnées `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="d7f16-166">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="d7f16-167">Les connexions établies/rétablies utilisent `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="d7f16-167">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="d7f16-168">**Les deux** `onConnectionDown` et `onConnectionUp` doivent être spécifiés :</span><span class="sxs-lookup"><span data-stu-id="d7f16-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      :::no-loc(Blazor):::.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="d7f16-169">Ajuster le nombre et l’intervalle de tentatives de reconnexion</span><span class="sxs-lookup"><span data-stu-id="d7f16-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="d7f16-170">Pour régler le nombre et l’intervalle de tentatives de reconnexion, définissez le nombre de nouvelles tentatives ( `maxRetries` ) et le délai (en millisecondes) autorisé pour chaque nouvelle tentative ( `retryIntervalMilliseconds` ) :</span><span class="sxs-lookup"><span data-stu-id="d7f16-170">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      :::no-loc(Blazor):::.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="d7f16-171">Masquer ou remplacer l’affichage de reconnexion</span><span class="sxs-lookup"><span data-stu-id="d7f16-171">Hide or replace the reconnection display</span></span>

<span data-ttu-id="d7f16-172">Pour masquer l’affichage de reconnexion, définissez le gestionnaire de reconnexion `_reconnectionDisplay` sur un objet vide ( `{}` ou `new Object()` ) :</span><span class="sxs-lookup"><span data-stu-id="d7f16-172">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        :::no-loc(Blazor):::.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      :::no-loc(Blazor):::.start();
    </script>
</body>
```

<span data-ttu-id="d7f16-173">Pour remplacer l’affichage de reconnexion, `_reconnectionDisplay` dans l’exemple précédent, définissez l’élément pour l’affichage :</span><span class="sxs-lookup"><span data-stu-id="d7f16-173">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
:::no-loc(Blazor):::.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="d7f16-174">L’espace réservé `{ELEMENT ID}` est l’ID de l’élément HTML à afficher.</span><span class="sxs-lookup"><span data-stu-id="d7f16-174">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d7f16-175">Personnalisez le délai avant que l’affichage de reconnexion ne s’affiche en définissant la `transition-delay` propriété dans le CSS () de l’application `wwwroot/css/site.css` pour l’élément modal.</span><span class="sxs-lookup"><span data-stu-id="d7f16-175">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="d7f16-176">L’exemple suivant définit le délai de transition de 500 ms (par défaut) à 1 000 MS (1 seconde) :</span><span class="sxs-lookup"><span data-stu-id="d7f16-176">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="d7f16-177">Déconnecter le :::no-loc(Blazor)::: circuit du client</span><span class="sxs-lookup"><span data-stu-id="d7f16-177">Disconnect the :::no-loc(Blazor)::: circuit from the client</span></span>

<span data-ttu-id="d7f16-178">Par défaut, un :::no-loc(Blazor)::: circuit est déconnecté lorsque l' [ `unload` événement de page](https://developer.mozilla.org/docs/Web/API/Window/unload_event) est déclenché.</span><span class="sxs-lookup"><span data-stu-id="d7f16-178">By default, a :::no-loc(Blazor)::: circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="d7f16-179">Pour déconnecter le circuit d’autres scénarios sur le client, appelez `:::no-loc(Blazor):::.disconnect` dans le gestionnaire d’événements approprié.</span><span class="sxs-lookup"><span data-stu-id="d7f16-179">To disconnect the circuit for other scenarios on the client, invoke `:::no-loc(Blazor):::.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="d7f16-180">Dans l’exemple suivant, le circuit est déconnecté lorsque la page est masquée ([ `pagehide` événement](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) :</span><span class="sxs-lookup"><span data-stu-id="d7f16-180">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  :::no-loc(Blazor):::.disconnect();
});
```

<!-- HOLD for reactivation at 5x

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server):::.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, :::no-loc(Razor)::: variables, or :::no-loc(Razor)::: expressions.

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

## <a name="static-files"></a><span data-ttu-id="d7f16-181">Fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="d7f16-181">Static files</span></span>

<span data-ttu-id="d7f16-182">*Cette section s’applique à :::no-loc(Blazor Server)::: .*</span><span class="sxs-lookup"><span data-stu-id="d7f16-182">*This section applies to :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="d7f16-183">Pour créer des mappages de fichiers supplémentaires avec <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> ou configurer autre <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , utilisez l' **une** des approches suivantes.</span><span class="sxs-lookup"><span data-stu-id="d7f16-183">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="d7f16-184">Dans les exemples suivants, l' `{EXTENSION}` espace réservé est l’extension de fichier et l' `{CONTENT TYPE}` espace réservé est le type de contenu.</span><span class="sxs-lookup"><span data-stu-id="d7f16-184">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="d7f16-185">Configurez les options par [injection de dépendance (di)](xref:blazor/fundamentals/dependency-injection) dans `Startup.ConfigureServices` ( `Startup.cs` ) à l’aide de <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :</span><span class="sxs-lookup"><span data-stu-id="d7f16-185">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  <span data-ttu-id="d7f16-186">Étant donné que cette approche configure le même fournisseur de fichiers que celui utilisé, assurez-vous `blazor.server.js` que votre configuration personnalisée n’interfère pas avec service `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="d7f16-186">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="d7f16-187">Par exemple, ne supprimez pas le mappage des fichiers JavaScript en configurant le fournisseur avec `provider.Mappings.Remove(".js")` .</span><span class="sxs-lookup"><span data-stu-id="d7f16-187">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="d7f16-188">Utilisez deux appels à <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` ( `Startup.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="d7f16-188">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="d7f16-189">Configurez le fournisseur de fichiers personnalisé dans le premier appel avec <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .</span><span class="sxs-lookup"><span data-stu-id="d7f16-189">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="d7f16-190">Le deuxième intergiciel sert `blazor.server.js` , qui utilise la configuration de fichiers statiques par défaut fournie par l' :::no-loc(Blazor)::: infrastructure.</span><span class="sxs-lookup"><span data-stu-id="d7f16-190">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the :::no-loc(Blazor)::: framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="d7f16-191">Vous pouvez éviter les interférences avec les services `_framework/blazor.server.js` en utilisant <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> pour exécuter un intergiciel (middleware) de fichiers statiques personnalisé :</span><span class="sxs-lookup"><span data-stu-id="d7f16-191">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a><span data-ttu-id="d7f16-192">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d7f16-192">Additional resources</span></span>

* <xref:fundamentals/logging/index>
