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
# <a name="aspnet-core-blazor-signalr-guidance"></a>Blazor SignalR Aide ASP.net Core

::: zone pivot="webassembly"

Cet article explique comment configurer et gérer SignalR des connexions dans des Blazor applications.

Pour obtenir des conseils généraux sur la configuration de ASP.NET Core SignalR , consultez les rubriques dans la <xref:signalr/introduction> section de la documentation. Pour configurer l' SignalR [Ajout à une Blazor WebAssembly solution hébergée](xref:tutorials/signalr-blazor), consultez <xref:signalr/configuration#configure-server-options> .

## <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR négociation Cross-Origin pour l’authentification

Pour configurer le SignalR client sous-jacent pour envoyer des informations d’identification, telles que cookie les en-têtes s ou http authentication :

* Utilisez <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> pour définir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> les demandes Cross-Origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) .

  `IncludeRequestCredentialsMessageHandler.cs`:

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

* En cas de création d’une connexion de concentrateur, attribuez <xref:System.Net.Http.HttpMessageHandler> à l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option :

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

  L’exemple précédent configure l’URL de connexion du concentrateur sur l’adresse URI absolue à `/chathub` , qui est l’URL utilisée dans le [ SignalR Blazor didacticiel with](xref:tutorials/signalr-blazor) du `Index` composant ( `Pages/Index.razor` ). L’URI peut également être défini via une chaîne, par exemple `https://signalr.example.com` , ou via la [configuration](xref:blazor/fundamentals/configuration).

Pour plus d’informations, consultez <xref:signalr/configuration#configure-additional-options>.

::: moniker range=">= aspnetcore-5.0"

## <a name="render-mode"></a>Mode d’affichage

Si une Blazor WebAssembly application qui utilise SignalR est configurée pour être prérendu sur le serveur, le prérendu se produit avant l’établissement de la connexion cliente au serveur. Pour plus d’informations, consultez les articles suivants :

* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:signalr/introduction>
* <xref:signalr/configuration>

::: zone-end

::: zone pivot="server"

Cet article explique comment configurer et gérer SignalR des connexions dans des Blazor applications.

Pour obtenir des conseils généraux sur la configuration de ASP.NET Core SignalR , consultez les rubriques dans la <xref:signalr/introduction> section de la documentation. Pour configurer l' SignalR [Ajout à une Blazor WebAssembly solution hébergée](xref:tutorials/signalr-blazor), consultez <xref:signalr/configuration#configure-server-options> .

## <a name="circuit-handler-options"></a>Options du gestionnaire de circuit

Configurez le Blazor Server circuit comme <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> indiqué dans le tableau suivant.

| Option | Default | Description |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | Envoyer des messages d’exception détaillés à JavaScript lorsqu’une exception non gérée se produit sur le circuit ou lorsqu’un appel de méthode .NET via l’interopérabilité JS entraîne une exception. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | 100 | Nombre maximal de circuits déconnectés que le serveur détient en mémoire à la fois. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | 3 minutes | Durée maximale pendant laquelle un circuit déconnecté est maintenu en mémoire avant d’être détruit. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | 1 minute | Durée maximale pendant laquelle le serveur attend avant d’expirer un appel de fonction JavaScript asynchrone. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | 10 | Nombre maximal de lots de rendu sans accusé de réception le serveur conserve en mémoire par circuit à un moment donné pour prendre en charge une reconnexion fiable. Après avoir atteint la limite, le serveur cesse de produire de nouveaux lots de rendu jusqu’à ce qu’un ou plusieurs lots soient reconnus par le client. |

Configurez les options dans `Startup.ConfigureServices` avec un délégué d’options à <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> . L’exemple suivant affecte les valeurs d’option par défaut indiquées dans le tableau précédent. Vérifiez que `Startup.cs` utilise l' <xref:System> espace de noms ( `using System;` ).

`Startup.ConfigureServices`:

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

Pour configurer le <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> , utilisez <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> avec <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> . Pour obtenir une description des options, consultez <xref:signalr/configuration#configure-server-options> . L’exemple suivant affecte les valeurs d’option par défaut. Vérifiez que `Startup.cs` utilise l' <xref:System> espace de noms ( `using System;` ).

`Startup.ConfigureServices`:

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

## <a name="reflect-the-connection-state-in-the-ui"></a>Refléter l’état de la connexion dans l’interface utilisateur

Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter. En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.

Pour personnaliser l’interface utilisateur, définissez un élément avec un `id` de `components-reconnect-modal` dans le `<body>` de la `_Host.cshtml` Razor page.

`Pages/_Host.cshtml`:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Ajoutez les styles CSS suivants à la feuille de style du site.

`wwwroot/css/site.css`:

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément par le Blazor Framework.

| Classe CSS                       | Détermine&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Connexion perdue. Le client tente de se reconnecter. Affichez le modal. |
| `components-reconnect-hide`     | Une connexion active est rétablie sur le serveur. Masquez le modal. |
| `components-reconnect-failed`   | Échec de la reconnexion, probablement en raison d’une défaillance du réseau. Pour tenter une reconnexion, appelez `window.Blazor.reconnect()` en JavaScript. |
| `components-reconnect-rejected` | Reconnexion refusée. Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu. Pour recharger l’application, appelez `location.reload()` en JavaScript. Cet état de connexion peut se produire dans les cas suivants :<ul><li>Un blocage dans le circuit côté serveur se produit.</li><li>Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur. Les instances des composants de l’utilisateur sont supprimées.</li><li>Le serveur est redémarré ou le processus de travail de l’application est recyclé.</li></ul> |

## <a name="render-mode"></a>Mode d’affichage

Par défaut, Blazor Server les applications préaffichent l’interface utilisateur sur le serveur avant l’établissement de la connexion cliente au serveur. Pour plus d’informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="initialize-the-blazor-circuit"></a>Initialiser le Blazor circuit

Configurez le démarrage manuel du Blazor Server [ SignalR circuit](xref:blazor/hosting-models#circuits) d’une application dans le `Pages/_Host.cshtml` fichier :

* Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.
* Placez un script qui appelle `Blazor.start` après la `blazor.server.js` balise du script et à l’intérieur de la `</body>` balise de fermeture.

Lorsque `autostart` est désactivé, tous les aspects de l’application qui ne dépendent pas du circuit fonctionnent normalement. Par exemple, le routage côté client est opérationnel. Toutefois, tous les aspects qui dépendent du circuit ne sont pas opérationnels tant que `Blazor.start` n’est pas appelé. Le comportement de l’application n’est pas prévisible sans circuit établi. Par exemple, les méthodes de composant ne peuvent pas s’exécuter pendant que le circuit est déconnecté.

### <a name="initialize-blazor-when-the-document-is-ready"></a>Initialiser Blazor lorsque le document est prêt

`Pages/_Host.cshtml`:

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a>Chaîne à `Promise` qui résulte d’un démarrage manuel

Pour effectuer des tâches supplémentaires, telles que l’initialisation de l’interopérabilité JS, utilisez [`then`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) pour chaîner à [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) qui résulte d’un démarrage manuel de l' Blazor application.

`Pages/_Host.cshtml`:

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

### <a name="configure-signalr-client-logging"></a>Configurer la SignalR journalisation du client

Sur le générateur de clients, transmettez l' `configureSignalR` objet de configuration qui appelle `configureLogging` avec le niveau de journalisation.

`Pages/_Host.cshtml`:

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

Dans l’exemple précédent, `information` est équivalent à un niveau de journal de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .

### <a name="modify-the-reconnection-handler"></a>Modifier le gestionnaire de reconnexion

Les événements de connexion du circuit du gestionnaire de reconnexion peuvent être modifiés pour les comportements personnalisés, par exemple :

* Pour avertir l’utilisateur si la connexion est abandonnée.
* Pour effectuer la journalisation (à partir du client) lorsqu’un circuit est connecté.

Pour modifier les événements de connexion, enregistrez les rappels pour les modifications de connexion suivantes :

* Utilisation de connexions abandonnées `onConnectionDown` .
* Les connexions établies/rétablies utilisent `onConnectionUp` .

**`onConnectionDown`Et `onConnectionUp` doivent être spécifiés.**

`Pages/_Host.cshtml`:

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>Ajuster le nombre et l’intervalle de tentatives de reconnexion

Pour régler le nombre et l’intervalle de tentatives de reconnexion, définissez le nombre de nouvelles tentatives ( `maxRetries` ) et le délai (en millisecondes) autorisé pour chaque nouvelle tentative ( `retryIntervalMilliseconds` ).

`Pages/_Host.cshtml`:

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

## <a name="hide-or-replace-the-reconnection-display"></a>Masquer ou remplacer l’affichage de reconnexion

Pour masquer l’affichage de reconnexion, définissez le gestionnaire de reconnexion `_reconnectionDisplay` sur un objet vide ( `{}` ou `new Object()` ).

`Pages/_Host.cshtml`:

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

Pour remplacer l’affichage de reconnexion, `_reconnectionDisplay` dans l’exemple précédent, définissez l’élément pour l’affichage :

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

L’espace réservé `{ELEMENT ID}` est l’ID de l’élément HTML à afficher.

::: moniker range=">= aspnetcore-5.0"

Personnalisez le délai avant que l’affichage de reconnexion ne s’affiche en définissant la `transition-delay` propriété dans le CSS du site pour l’élément modal. L’exemple suivant définit le délai de transition de 500 ms (par défaut) à 1 000 MS (1 seconde).

`wwwroot/css/site.css`:

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a>Déconnecter le Blazor circuit du client

Par défaut, un Blazor circuit est déconnecté lorsque l' [ `unload` événement de page](https://developer.mozilla.org/docs/Web/API/Window/unload_event) est déclenché. Pour déconnecter le circuit d’autres scénarios sur le client, appelez `Blazor.disconnect` dans le gestionnaire d’événements approprié. Dans l’exemple suivant, le circuit est déconnecté lorsque la page est masquée ([ `pagehide` événement](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) :

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [Blazor Server événements de reconnexion et événements de cycle de vie du composant](xref:blazor/components/lifecycle#blazor-server-reconnection-events)

::: zone-end
