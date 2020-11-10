---
title: Prérendu et intégration des Razor composants ASP.net Core
author: guardrex
description: Découvrez Razor les scénarios d’intégration de composants pour Blazor les applications, y compris le prérendu des Razor composants sur le serveur.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: affca6c9b585b91787f94a13144d07bedfefdd37
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431704"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a>Prérendu et intégration des Razor composants ASP.net Core

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

Razor les composants peuvent être intégrés dans des Razor pages et des applications MVC dans une solution hébergée Blazor WebAssembly . Lorsque la page ou la vue est restituée, les composants peuvent être prérendus en même temps.

## <a name="configuration"></a>Configuration

Pour configurer le prérendu d’une Blazor WebAssembly application :

1. Hébergez l' Blazor WebAssembly application dans une application ASP.net core. Une Blazor WebAssembly application autonome peut être ajoutée à une solution ASP.net Core, ou vous pouvez utiliser une application hébergée Blazor WebAssembly créée à partir du Blazor modèle de projet hébergé.

1. Supprimez le fichier statique par défaut `wwwroot/index.html` du Blazor WebAssembly projet client.

1. Supprimez la ligne suivante dans `Program.Main` le projet client :

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. Ajoutez un `Pages/_Host.cshtml` fichier au projet serveur. Vous pouvez obtenir un `_Host.cshtml` fichier à partir d’une application créée à partir du Blazor Server modèle à l’aide `dotnet new blazorserver -o BlazorServer` de la commande dans un interpréteur de commandes. Après avoir placé le `Pages/_Host.cshtml` fichier dans l’application serveur de la Blazor WebAssembly solution hébergée, apportez les modifications suivantes au fichier :

   * Définissez l’espace de noms sur le dossier de l’application serveur `Pages` (par exemple, `@namespace BlazorHosted.Server.Pages` ).
   * Définissez une [`@using`](xref:mvc/views/razor#using) directive pour le projet client (par exemple, `@using BlazorHosted.Client` ).
   * Mettez à jour les liens de feuille de style pour pointer vers la feuille de style de l’application webassembly. Dans l’exemple suivant, l’espace de noms de l’application cliente est `BlazorHosted.Client` :

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * Mettez à jour le `render-mode` du [tag Helper du composant](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) pour prérestituer le `App` composant racine :

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * Mettez à jour la Blazor source du script pour utiliser le Blazor WebAssembly script côté client :

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. Dans `Startup.Configure` ( `Startup.cs` ) du projet serveur :

   * Appelez `UseDeveloperExceptionPage` sur le générateur d’applications dans l’environnement de développement.
   * Appelez `UseBlazorFrameworkFiles` sur le générateur d’applications.
   * Modifiez le secours de la `index.html` page ( `endpoints.MapFallbackToFile("index.html");` ) à la `_Host.cshtml` page.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a>Rendre des composants dans une page ou une vue à l’aide du tag Helper Component

Le tag Helper Component prend en charge deux modes de rendu pour le rendu d’un composant à partir d’une Blazor WebAssembly application dans une page ou une vue :

* `WebAssembly`: Restitue un marqueur pour une Blazor WebAssembly application à utiliser pour inclure un composant interactif lorsqu’il est chargé dans le navigateur. Le composant n’est pas prérendu. Cette option facilite le rendu de différents Blazor WebAssembly composants sur des pages différentes.
* `WebAssemblyPrerendered`: Prérestitue le composant en HTML statique et comprend un marqueur pour une Blazor WebAssembly application pour une utilisation ultérieure afin de rendre le composant interactif lorsqu’il est chargé dans le navigateur.

Dans l' Razor exemple de pages suivantes, le `Counter` composant est rendu dans une page. Pour rendre le composant interactif, le Blazor WebAssembly script est inclus dans la section de [rendu](xref:mvc/views/layout#sections)de la page. Pour éviter d’utiliser l’espace de noms complet du `Counter` composant avec le tag Helper Component ( `{APP ASSEMBLY}.Pages.Counter` ), ajoutez une [`@using`](xref:mvc/views/razor#using) directive pour l’espace de noms de l’application cliente `Pages` . Dans l’exemple suivant, l’espace de noms de l’application cliente est `BlazorHosted.Client` :

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configure si le composant :

* Est prérendu dans la page.
* Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.

Pour plus d’informations sur le tag Helper Component, y compris le passage de paramètres et de la <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

L’exemple précédent exige que la disposition de l’application serveur ( `_Layout.cshtml` ) inclue une [section Render](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> ) pour le script à l’intérieur de la `</body>` balise de fermeture :

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

Le `_Layout.cshtml` fichier se trouve dans le `Pages/Shared` dossier d’une Razor application de pages ou `Views/Shared` d’un dossier dans une application MVC.

Si l’application doit également styliser les composants avec les styles de l' Blazor WebAssembly application, incluez les styles de l’application dans le `_Layout.cshtml` fichier. Dans l’exemple suivant, l’espace de noms de l’application cliente est `BlazorHosted.Client` :

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a>Afficher des composants dans une page ou une vue à l’aide d’un sélecteur CSS

Ajoutez des composants racine au projet *client* dans `Program.Main` ( `Program.cs` ). Dans l’exemple suivant, le `Counter` composant est déclaré en tant que composant racine avec un sélecteur CSS qui sélectionne l’élément avec le `id` qui correspond `my-counter` . Dans l’exemple suivant, l’espace de noms de l’application cliente est `BlazorHosted.Client` :

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

Dans l' Razor exemple de pages suivantes, le `Counter` composant est rendu dans une page. Pour rendre le composant interactif, le Blazor WebAssembly script est inclus dans la [section Render](xref:mvc/views/layout#sections)de la page :

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

L’exemple précédent exige que la disposition de l’application serveur ( `_Layout.cshtml` ) inclue une [section Render](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> ) pour le script à l’intérieur de la `</body>` balise de fermeture :

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

Le `_Layout.cshtml` fichier se trouve dans le `Pages/Shared` dossier d’une Razor application de pages ou `Views/Shared` d’un dossier dans une application MVC.

Si l’application doit également styliser les composants avec les styles de l' Blazor WebAssembly application, incluez les styles de l’application dans le `_Layout.cshtml` fichier. Dans l’exemple suivant, l’espace de noms de l’application cliente est `BlazorHosted.Client` :

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

L’intégration de composants dans des Razor Razor pages et des applications MVC dans une solution hébergée Blazor WebAssembly est prise en charge dans ASP.net core dans .net 5 ou version ultérieure. Sélectionnez une version .NET 5 ou ultérieure de cet article.

::: moniker-end

::: zone-end

::: zone pivot="server"

Razor les composants peuvent être intégrés dans des Razor pages et des applications MVC dans une Blazor Server application. Lorsque la page ou la vue est restituée, les composants peuvent être prérendus en même temps.

Après avoir [configuré l’application](#configuration), suivez les instructions des sections suivantes en fonction des exigences de l’application :

* Composants routables : pour les composants qui sont directement routables à partir des demandes de l’utilisateur. Suivez ces instructions lorsque les visiteurs doivent être en mesure de faire une requête HTTP dans leur navigateur pour un composant avec une [`@page`](xref:mvc/views/razor#page) directive.
  * [Utiliser des composants routables dans une Razor application pages](#use-routable-components-in-a-razor-pages-app)
  * [Utiliser des composants routables dans une application MVC](#use-routable-components-in-an-mvc-app)
* [Rendez les composants à partir d’une page ou d’une vue](#render-components-from-a-page-or-view): pour les composants qui ne sont pas directement routables à partir des demandes de l’utilisateur. Suivez ces instructions lorsque l’application incorpore des composants dans des pages et des vues existantes avec le [tag Helper Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="configuration"></a>Configuration

Une Razor application de pages ou MVC existante peut intégrer Razor des composants dans des pages et des vues :

1. Dans le fichier de disposition de l’application ( `_Layout.cshtml` ) :

   * Ajoutez la `<base>` balise suivante à l' `<head>` élément :

     ```html
     <base href="~/" />
     ```

     La `href` valeur (le *chemin d’accès de base* de l’application) dans l’exemple précédent suppose que l’application se trouve dans le chemin d’URL racine ( `/` ). Si l’application est une sous-application, suivez les instructions de la section *chemin d’accès de base* de l’application de l' <xref:blazor/host-and-deploy/index#app-base-path> article.

     Le `_Layout.cshtml` fichier se trouve dans le `Pages/Shared` dossier d’une Razor application de pages ou `Views/Shared` d’un dossier dans une application MVC.

   * Ajoutez une `<script>` balise pour le `blazor.server.js` script immédiatement avant la `Scripts` section Render :

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     L’infrastructure ajoute le `blazor.server.js` script à l’application. Il n’est pas nécessaire d’ajouter manuellement le script à l’application.

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

1. Suivez les instructions de la section [configuration](#configuration) .

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

   Pour plus d’informations sur le tag Helper Component, y compris le passage de paramètres et de la <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

1. Ajoutez un itinéraire de priorité basse pour la `_Host.cshtml` page à la configuration de point de terminaison dans `Startup.Configure` :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Ajoutez des composants routables à l’application. Par exemple :

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Pour plus d’informations sur les espaces de noms, consultez la section [espaces de noms de composants](#component-namespaces) .

## <a name="use-routable-components-in-an-mvc-app"></a>Utiliser des composants routables dans une application MVC

*Cette section concerne l’ajout de composants qui sont directement routables à partir des demandes des utilisateurs.*

Pour prendre en charge les composants routables Razor dans les applications MVC :

1. Suivez les instructions de la section [configuration](#configuration) .

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

   Pour plus d’informations sur le tag Helper Component, y compris le passage de paramètres et de la <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

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

1. Créez un `Pages` dossier et ajoutez des composants routables à l’application. Par exemple :

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
* Si un dossier nommé `Components` n’est pas utilisé pour contenir les composants, accédez `Components` au dossier dans lequel se trouvent les composants.

```cshtml
@using MyAppNamespace.Components
```

Le `_ViewImports.cshtml` fichier se trouve dans le `Pages` dossier d’une Razor application pages ou dans le `Views` dossier d’une application MVC.

Pour plus d'informations, consultez <xref:blazor/components/index#namespaces>.

::: zone-end
