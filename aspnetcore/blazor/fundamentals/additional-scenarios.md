---
title: BlazorConfiguration du modèle d’hébergement ASP.net Core
author: guardrex
description: En savoir plus sur les scénarios supplémentaires pour ASP.NET Core la Blazor configuration du modèle d’hébergement.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: f8b6e65424948aaa7b28023497bbbf2a1ceb47dd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056047"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>BlazorConfiguration du modèle d’hébergement ASP.net Core

Par [Daniel Roth](https://github.com/danroth27), [MacKinnon argent](https://github.com/MackinnonBuck)et [Luke Latham](https://github.com/guardrex)

Cet article traite de l’hébergement de la configuration du modèle.

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>SignalR négociation Cross-Origin pour l’authentification

*Cette section s’applique à Blazor WebAssembly .*

Pour configurer le SignalR client sous-jacent pour envoyer des informations d’identification, telles que cookie les en-têtes s ou http authentication :

* Utilisez <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> pour définir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> les demandes Cross-Origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :

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

* Affectez <xref:System.Net.Http.HttpMessageHandler> à l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option :

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Pour plus d'informations, consultez <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Refléter l’état de la connexion dans l’interface utilisateur

*Cette section s’applique à Blazor Server .*

Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter. En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.

Pour personnaliser l’interface utilisateur, définissez un élément avec un `id` de `components-reconnect-modal` dans le `<body>` de la `_Host.cshtml` Razor page :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Ajoutez le code suivant à la feuille de style de l’application ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ) :

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément.

| Classe CSS                       | Détermine&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Connexion perdue. Le client tente de se reconnecter. Affichez le modal. |
| `components-reconnect-hide`     | Une connexion active est rétablie sur le serveur. Masquez le modal. |
| `components-reconnect-failed`   | Échec de la reconnexion, probablement en raison d’une défaillance du réseau. Pour tenter une reconnexion, appelez `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Reconnexion refusée. Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu. Pour recharger l’application, appelez `location.reload()` . Cet état de connexion peut se produire dans les cas suivants :<ul><li>Un blocage dans le circuit côté serveur se produit.</li><li>Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur. Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</li><li>Le serveur est redémarré ou le processus de travail de l’application est recyclé.</li></ul> |

## <a name="render-mode"></a>Mode d’affichage

*Cette section s’applique à Blazor Server .*

Blazor Server les applications sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie. Il est configuré dans la `_Host.cshtml` Razor page :

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> Configure si le composant :

* Est prérendu dans la page.
* Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.

| Mode d’affichage | Description |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Restitue le composant en HTML statique et comprend un marqueur pour une Blazor Server application. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Restitue un marqueur pour une Blazor Server application. La sortie du composant n’est pas incluse. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Génère le rendu du composant en HTML statique. |

Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.

## <a name="initialize-the-no-locblazor-circuit"></a>Initialiser le Blazor circuit

*Cette section s’applique à Blazor Server .*

Configurez le démarrage manuel du Blazor Server [ SignalR circuit](xref:blazor/hosting-models#circuits) d’une application dans le `Pages/_Host.cshtml` fichier :

* Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.
* Placez un script qui appelle `Blazor.start` après la `blazor.server.js` balise du script et à l’intérieur de la `</body>` balise de fermeture.

Lorsque `autostart` est désactivé, tous les aspects de l’application qui ne dépendent pas du circuit fonctionnent normalement. Par exemple, le routage côté client est opérationnel. Toutefois, tous les aspects qui dépendent du circuit ne sont pas opérationnels tant que `Blazor.start` n’est pas appelé. Le comportement de l’application n’est pas prévisible sans circuit établi. Par exemple, les méthodes de composant ne peuvent pas s’exécuter pendant que le circuit est déconnecté.

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a>Initialiser Blazor lorsque le document est prêt

Pour initialiser l' Blazor application lorsque le document est prêt :

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

Pour effectuer des tâches supplémentaires, telles que l’initialisation de l’interopérabilité JS, utilisez `then` pour chaîner à `Promise` qui résulte d’un démarrage manuel de l' Blazor application :

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

### <a name="configure-the-no-locsignalr-client"></a>Configurer le SignalR client

#### <a name="logging"></a>Journalisation

Pour configurer SignalR la journalisation du client, transmettez un objet de configuration ( `configureSignalR` ) qui appelle `configureLogging` avec le niveau de journalisation sur le générateur client :

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

**Les deux** `onConnectionDown` et `onConnectionUp` doivent être spécifiés :

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>Ajuster le nombre et l’intervalle de tentatives de reconnexion

Pour régler le nombre et l’intervalle de tentatives de reconnexion, définissez le nombre de nouvelles tentatives ( `maxRetries` ) et le délai (en millisecondes) autorisé pour chaque nouvelle tentative ( `retryIntervalMilliseconds` ) :

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

Pour masquer l’affichage de reconnexion, définissez le gestionnaire de reconnexion `_reconnectionDisplay` sur un objet vide ( `{}` ou `new Object()` ) :

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

Personnalisez le délai avant que l’affichage de reconnexion ne s’affiche en définissant la `transition-delay` propriété dans le CSS () de l’application `wwwroot/css/site.css` pour l’élément modal. L’exemple suivant définit le délai de transition de 500 ms (par défaut) à 1 000 MS (1 seconde) :

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a>Déconnecter le Blazor circuit du client

Par défaut, un Blazor circuit est déconnecté lorsque l' [ `unload` événement de page](https://developer.mozilla.org/docs/Web/API/Window/unload_event) est déclenché. Pour déconnecter le circuit d’autres scénarios sur le client, appelez `Blazor.disconnect` dans le gestionnaire d’événements approprié. Dans l’exemple suivant, le circuit est déconnecté lorsque la page est masquée ([ `pagehide` événement](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) :

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

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

## <a name="static-files"></a>Fichiers statiques

*Cette section s’applique à Blazor Server .*

Pour créer des mappages de fichiers supplémentaires avec <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> ou configurer autre <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , utilisez l' **une** des approches suivantes. Dans les exemples suivants, l' `{EXTENSION}` espace réservé est l’extension de fichier et l' `{CONTENT TYPE}` espace réservé est le type de contenu.

* Configurez les options par [injection de dépendance (di)](xref:blazor/fundamentals/dependency-injection) dans `Startup.ConfigureServices` ( `Startup.cs` ) à l’aide de <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :

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

  Étant donné que cette approche configure le même fournisseur de fichiers que celui utilisé, assurez-vous `blazor.server.js` que votre configuration personnalisée n’interfère pas avec service `blazor.server.js` . Par exemple, ne supprimez pas le mappage des fichiers JavaScript en configurant le fournisseur avec `provider.Mappings.Remove(".js")` .

* Utilisez deux appels à <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` ( `Startup.cs` ) :
  * Configurez le fournisseur de fichiers personnalisé dans le premier appel avec <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .
  * Le deuxième intergiciel sert `blazor.server.js` , qui utilise la configuration de fichiers statiques par défaut fournie par l' Blazor infrastructure.

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* Vous pouvez éviter les interférences avec les services `_framework/blazor.server.js` en utilisant <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> pour exécuter un intergiciel (middleware) de fichiers statiques personnalisé :

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/logging/index>
