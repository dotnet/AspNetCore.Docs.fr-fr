---
title: Intégrer Razor des composants de ASP.net core dans Razor des pages et des applications MVC
author: guardrex
description: En savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
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
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: e56a08be082cef4ba3a0a58fdfa9d3800d244f75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056255"
---
# <a name="integrate-aspnet-core-no-locrazor-components-into-no-locrazor-pages-and-mvc-apps"></a>Intégrer Razor des composants de ASP.net core dans Razor des pages et des applications MVC

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

Razor les composants peuvent être intégrés dans Razor des pages et des applications MVC. Lorsque la page ou la vue est restituée, les composants peuvent être prérendus en même temps.

Après avoir [préparé l’application](#prepare-the-app), suivez les instructions des sections suivantes en fonction des exigences de l’application :

* Composants routables : pour les composants qui sont directement routables à partir des demandes de l’utilisateur. Suivez ces instructions lorsque les visiteurs doivent être en mesure de faire une requête HTTP dans leur navigateur pour un composant avec une [`@page`](xref:mvc/views/razor#page) directive.
  * [Utiliser des composants routables dans une Razor application pages](#use-routable-components-in-a-razor-pages-app)
  * [Utiliser des composants routables dans une application MVC](#use-routable-components-in-an-mvc-app)
* [Rendez les composants à partir d’une page ou d’une vue](#render-components-from-a-page-or-view): pour les composants qui ne sont pas directement routables à partir des demandes de l’utilisateur. Suivez ces instructions lorsque l’application incorpore des composants dans des pages et des vues existantes avec le [tag Helper Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="prepare-the-app"></a>Préparer l’application

Une Razor application de pages ou MVC existante peut intégrer Razor des composants dans des pages et des vues :

1. Dans le fichier de disposition de l’application ( `_Layout.cshtml` ) :

   * Ajoutez la `<base>` balise suivante à l' `<head>` élément :

     ```html
     <base href="~/" />
     ```

     La `href` valeur (le *chemin d’accès de base* de l’application) dans l’exemple précédent suppose que l’application se trouve dans le chemin d’URL racine ( `/` ). Si l’application est une sous-application, suivez les instructions de la section *chemin d’accès de base* de l’application de l' <xref:blazor/host-and-deploy/index#app-base-path> article.

     Le `_Layout.cshtml` fichier se trouve dans le dossier *pages/Shared* dans une Razor application pages ou un dossier *Views/Shared* dans une application MVC.

   * Ajoutez une `<script>` balise pour le *blazor.server.js* script immédiatement avant la `</body>` balise de fermeture :

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     L’infrastructure ajoute le script *blazor.server.js* à l’application. Il n’est pas nécessaire d’ajouter manuellement le script à l’application.

1. Ajoutez un `_Imports.razor` fichier au dossier racine du projet avec le contenu suivant (modifiez le dernier espace de noms,, en lui attribuant l' `MyAppNamespace` espace de noms de l’application) :

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. Dans `Startup.ConfigureServices` , inscrivez le Blazor Server service :

   ```csharp
   services.AddServerSideBlazor();
   ```

1. Dans `Startup.Configure` , ajoutez le Blazor point de terminaison Hub à `app.UseEndpoints` :

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Intégrer des composants dans n’importe quelle page ou vue. Pour plus d’informations, consultez la section [rendre les composants à partir d’une page ou d’une vue](#render-components-from-a-page-or-view) .

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a>Utiliser des composants routables dans une Razor application pages

*Cette section concerne l’ajout de composants qui sont directement routables à partir des demandes des utilisateurs.*

Pour prendre en charge les composants routables Razor dans les Razor applications pages :

1. Suivez les instructions de la section [préparer l’application](#prepare-the-app) .

1. Ajoutez un `App.razor` fichier à la racine du projet avec le contenu suivant :

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Ajoutez un `_Host.cshtml` fichier au `Pages` dossier avec le contenu suivant :

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Les composants utilisent le `_Layout.cshtml` fichier partagé pour leur disposition.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configure si le `App` composant :

   * Est prérendu dans la page.
   * Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.

   | Mode de rendu | Description |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Restitue le `App` composant en HTML statique et comprend un marqueur pour une Blazor Server application. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Restitue un marqueur pour une Blazor Server application. La sortie du `App` composant n’est pas incluse. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Génère le rendu du `App` composant en HTML statique. |

   Pour plus d’informations sur le tag Helper composant, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

1. Ajoutez un itinéraire de priorité basse pour la `_Host.cshtml` page à la configuration de point de terminaison dans `Startup.Configure` :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Ajoutez des composants routables à l’application. Exemple :

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Pour plus d’informations sur les espaces de noms, consultez la section [espaces de noms de composants](#component-namespaces) .

## <a name="use-routable-components-in-an-mvc-app"></a>Utiliser des composants routables dans une application MVC

*Cette section concerne l’ajout de composants qui sont directement routables à partir des demandes des utilisateurs.*

Pour prendre en charge les composants routables Razor dans les applications MVC :

1. Suivez les instructions de la section [préparer l’application](#prepare-the-app) .

1. Ajoutez un `App.razor` fichier à la racine du projet avec le contenu suivant :

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Ajoutez un `_Host.cshtml` fichier au `Views/Home` dossier avec le contenu suivant :

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Les composants utilisent le `_Layout.cshtml` fichier partagé pour leur disposition.
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configure si le `App` composant :

   * Est prérendu dans la page.
   * Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.

   | Mode de rendu | Description |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Restitue le `App` composant en HTML statique et comprend un marqueur pour une Blazor Server application. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Restitue un marqueur pour une Blazor Server application. La sortie du `App` composant n’est pas incluse. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Génère le rendu du `App` composant en HTML statique. |

   Pour plus d’informations sur le tag Helper composant, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

1. Ajoutez une action au contrôleur d’hébergement :

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Ajoutez un itinéraire de faible priorité pour l’action de contrôleur qui retourne la `_Host.cshtml` vue à la configuration du point de terminaison dans `Startup.Configure` :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Créez un `Pages` dossier et ajoutez des composants routables à l’application. Exemple :

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Pour plus d’informations sur les espaces de noms, consultez la section [espaces de noms de composants](#component-namespaces) .

## <a name="render-components-from-a-page-or-view"></a>Rendre les composants à partir d’une page ou d’une vue

*Cette section se rapporte à l’ajout de composants à des pages ou à des vues, où les composants ne sont pas directement routés à partir des demandes de l’utilisateur.*

Pour afficher un composant à partir d’une page ou d’une vue, utilisez le [tag Helper Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

### <a name="render-stateful-interactive-components"></a>Rendu des composants interactifs avec état

Les composants interactifs avec état peuvent être ajoutés à une Razor page ou à une vue.

Lors du rendu de la page ou de la vue :

* Le composant est prérendu avec la page ou la vue.
* L’état initial du composant utilisé pour le prérendu est perdu.
* Un nouvel état de composant est créé lorsque la SignalR connexion est établie.

La Razor page suivante affiche un `Counter` composant :

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Pour plus d'informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

### <a name="render-noninteractive-components"></a>Rendre les composants non interactifs

Dans la Razor page suivante, le `Counter` composant est rendu statiquement avec une valeur initiale spécifiée à l’aide d’un formulaire. Étant donné que le composant est rendu statiquement, le composant n’est pas interactif :

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Pour plus d'informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="component-namespaces"></a>Espaces de noms de composants

Lorsque vous utilisez un dossier personnalisé pour stocker les composants de l’application, ajoutez l’espace de noms représentant le dossier à la page/la vue ou au `_ViewImports.cshtml` fichier. Dans l’exemple suivant :

* Accédez `MyAppNamespace` à l’espace de noms de l’application.
* Si un dossier nommé *Components* n’est pas utilisé pour contenir les composants, accédez `Components` au dossier dans lequel se trouvent les composants.

```cshtml
@using MyAppNamespace.Components
```

Le `_ViewImports.cshtml` fichier se trouve dans le `Pages` dossier d’une Razor application pages ou dans le `Views` dossier d’une application MVC.

Pour plus d'informations, consultez <xref:blazor/components/index#namespaces>.
