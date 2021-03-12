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
ms.openlocfilehash: a0c5cc0bdc78f2ea70b8c128616ad09328ccf87d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587383"
---
# <a name="prerender-and-integrate-aspnet-core-razor-components"></a><span data-ttu-id="4def2-103">Prérendu et intégration des Razor composants ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="4def2-103">Prerender and integrate ASP.NET Core Razor components</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4def2-104">Razor les composants peuvent être intégrés dans des Razor pages et des applications MVC dans une solution hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="4def2-104">Razor components can be integrated into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="4def2-105">Lorsque la page ou la vue est restituée, les composants peuvent être prérendus en même temps.</span><span class="sxs-lookup"><span data-stu-id="4def2-105">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="4def2-106">Configuration</span><span class="sxs-lookup"><span data-stu-id="4def2-106">Configuration</span></span>

<span data-ttu-id="4def2-107">Pour configurer le prérendu d’une Blazor WebAssembly application :</span><span class="sxs-lookup"><span data-stu-id="4def2-107">To set up prerendering for a Blazor WebAssembly app:</span></span>

1. <span data-ttu-id="4def2-108">Hébergez l' Blazor WebAssembly application dans une application ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="4def2-108">Host the Blazor WebAssembly app in an ASP.NET Core app.</span></span> <span data-ttu-id="4def2-109">Une Blazor WebAssembly application autonome peut être ajoutée à une solution ASP.net Core, ou vous pouvez utiliser une application hébergée Blazor WebAssembly créée à partir du [ Blazor WebAssembly modèle de projet](xref:blazor/project-structure).</span><span class="sxs-lookup"><span data-stu-id="4def2-109">A standalone Blazor WebAssembly app can be added to an ASP.NET Core solution, or you can use a hosted Blazor WebAssembly app created from the [Blazor WebAssembly project template](xref:blazor/project-structure).</span></span>

1. <span data-ttu-id="4def2-110">Supprimez le fichier statique par défaut `wwwroot/index.html` du Blazor WebAssembly projet client.</span><span class="sxs-lookup"><span data-stu-id="4def2-110">Remove the default static `wwwroot/index.html` file from the Blazor WebAssembly client project.</span></span>

1. <span data-ttu-id="4def2-111">Supprimez la ligne suivante dans `Program.Main` le projet client :</span><span class="sxs-lookup"><span data-stu-id="4def2-111">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="4def2-112">Ajoutez un `Pages/_Host.cshtml` fichier au projet serveur.</span><span class="sxs-lookup"><span data-stu-id="4def2-112">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="4def2-113">Vous pouvez obtenir un `_Host.cshtml` fichier à partir d’une application créée à partir du Blazor Server modèle à l’aide `dotnet new blazorserver -o BlazorServer` de la commande dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="4def2-113">You can obtain a `_Host.cshtml` file from an app created from the Blazor Server template with the `dotnet new blazorserver -o BlazorServer` command in a command shell.</span></span> <span data-ttu-id="4def2-114">Après avoir placé le `Pages/_Host.cshtml` fichier dans l’application serveur de la Blazor WebAssembly solution hébergée, apportez les modifications suivantes au fichier :</span><span class="sxs-lookup"><span data-stu-id="4def2-114">After placing the `Pages/_Host.cshtml` file into the server app of the hosted Blazor WebAssembly solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="4def2-115">Définissez l’espace de noms sur le dossier de l’application serveur `Pages` (par exemple, `@namespace BlazorHosted.Server.Pages` ).</span><span class="sxs-lookup"><span data-stu-id="4def2-115">Set the namespace to the server app's `Pages` folder (for example, `@namespace BlazorHosted.Server.Pages`).</span></span>
   * <span data-ttu-id="4def2-116">Définissez une [`@using`](xref:mvc/views/razor#using) directive pour le projet client (par exemple, `@using BlazorHosted.Client` ).</span><span class="sxs-lookup"><span data-stu-id="4def2-116">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using BlazorHosted.Client`).</span></span>
   * <span data-ttu-id="4def2-117">Mettez à jour les liens de feuille de style pour pointer vers la feuille de style de l’application webassembly.</span><span class="sxs-lookup"><span data-stu-id="4def2-117">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="4def2-118">Dans l’exemple suivant, l’espace de noms de l’application cliente est `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="4def2-118">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="4def2-119">Mettez à jour le `render-mode` du [tag Helper du composant](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) pour prérestituer le `App` composant racine :</span><span class="sxs-lookup"><span data-stu-id="4def2-119">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="4def2-120">Mettez à jour la Blazor source du script pour utiliser le Blazor WebAssembly script côté client :</span><span class="sxs-lookup"><span data-stu-id="4def2-120">Update the Blazor script source to use the client-side Blazor WebAssembly script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="4def2-121">Dans `Startup.Configure` ( `Startup.cs` ) du projet serveur :</span><span class="sxs-lookup"><span data-stu-id="4def2-121">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="4def2-122">Appelez `UseDeveloperExceptionPage` sur le générateur d’applications dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="4def2-122">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="4def2-123">Appelez `UseBlazorFrameworkFiles` sur le générateur d’applications.</span><span class="sxs-lookup"><span data-stu-id="4def2-123">Call `UseBlazorFrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="4def2-124">Remplacez la valeur de secours du `index.html` fichier ( `endpoints.MapFallbackToFile("index.html");` ) par la `_Host.cshtml` page : `endpoints.MapFallbackToPage("/_Host");` .</span><span class="sxs-lookup"><span data-stu-id="4def2-124">Change the fallback from the `index.html` file (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page: `endpoints.MapFallbackToPage("/_Host");`.</span></span>

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

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="4def2-125">Rendre des composants dans une page ou une vue à l’aide du tag Helper Component</span><span class="sxs-lookup"><span data-stu-id="4def2-125">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="4def2-126">Le tag Helper Component prend en charge deux modes de rendu pour le rendu d’un composant à partir d’une Blazor WebAssembly application dans une page ou une vue :</span><span class="sxs-lookup"><span data-stu-id="4def2-126">The Component Tag Helper supports two render modes for rendering a component from a Blazor WebAssembly app in a page or view:</span></span>

* <span data-ttu-id="4def2-127">`WebAssembly`: Restitue un marqueur pour une Blazor WebAssembly application à utiliser pour inclure un composant interactif lorsqu’il est chargé dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="4def2-127">`WebAssembly`: Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="4def2-128">Le composant n’est pas prérendu.</span><span class="sxs-lookup"><span data-stu-id="4def2-128">The component isn't prerendered.</span></span> <span data-ttu-id="4def2-129">Cette option facilite le rendu de différents Blazor WebAssembly composants sur des pages différentes.</span><span class="sxs-lookup"><span data-stu-id="4def2-129">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span>
* <span data-ttu-id="4def2-130">`WebAssemblyPrerendered`: Prérestitue le composant en HTML statique et comprend un marqueur pour une Blazor WebAssembly application pour une utilisation ultérieure afin de rendre le composant interactif lorsqu’il est chargé dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="4def2-130">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="4def2-131">Dans l' Razor exemple de pages suivantes, le `Counter` composant est rendu dans une page.</span><span class="sxs-lookup"><span data-stu-id="4def2-131">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="4def2-132">Pour rendre le composant interactif, le Blazor WebAssembly script est inclus dans la section de [rendu](xref:mvc/views/layout#sections)de la page.</span><span class="sxs-lookup"><span data-stu-id="4def2-132">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="4def2-133">Pour éviter d’utiliser l’espace de noms complet du `Counter` composant avec le tag Helper Component ( `{APP ASSEMBLY}.Pages.Counter` ), ajoutez une [`@using`](xref:mvc/views/razor#using) directive pour l’espace de noms de l’application cliente `Pages` .</span><span class="sxs-lookup"><span data-stu-id="4def2-133">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="4def2-134">Dans l’exemple suivant, l’espace de noms de l’application cliente est `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="4def2-134">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="4def2-135"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="4def2-135"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="4def2-136">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="4def2-136">Is prerendered into the page.</span></span>
* <span data-ttu-id="4def2-137">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4def2-137">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

<span data-ttu-id="4def2-138">Pour plus d’informations sur le tag Helper Component, y compris le passage de paramètres et de la <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="4def2-138">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="4def2-139">L’exemple précédent exige que la disposition de l’application serveur ( `_Layout.cshtml` ) inclue une [section Render](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> ) pour le script à l’intérieur de la `</body>` balise de fermeture :</span><span class="sxs-lookup"><span data-stu-id="4def2-139">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="4def2-140">Le `_Layout.cshtml` fichier se trouve dans le `Pages/Shared` dossier d’une Razor application de pages ou `Views/Shared` d’un dossier dans une application MVC.</span><span class="sxs-lookup"><span data-stu-id="4def2-140">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="4def2-141">Si l’application doit également styliser les composants avec les styles de l' Blazor WebAssembly application, incluez les styles de l’application dans le `_Layout.cshtml` fichier.</span><span class="sxs-lookup"><span data-stu-id="4def2-141">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="4def2-142">Dans l’exemple suivant, l’espace de noms de l’application cliente est `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="4def2-142">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="4def2-143">Afficher des composants dans une page ou une vue à l’aide d’un sélecteur CSS</span><span class="sxs-lookup"><span data-stu-id="4def2-143">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="4def2-144">Ajoutez des composants racine au projet *client* dans `Program.Main` ( `Program.cs` ).</span><span class="sxs-lookup"><span data-stu-id="4def2-144">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="4def2-145">Dans l’exemple suivant, le `Counter` composant est déclaré en tant que composant racine avec un sélecteur CSS qui sélectionne l’élément avec le `id` qui correspond `my-counter` .</span><span class="sxs-lookup"><span data-stu-id="4def2-145">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="4def2-146">Dans l’exemple suivant, l’espace de noms de l’application cliente est `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="4def2-146">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="4def2-147">Dans l' Razor exemple de pages suivantes, le `Counter` composant est rendu dans une page.</span><span class="sxs-lookup"><span data-stu-id="4def2-147">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="4def2-148">Pour rendre le composant interactif, le Blazor WebAssembly script est inclus dans la [section Render](xref:mvc/views/layout#sections)de la page :</span><span class="sxs-lookup"><span data-stu-id="4def2-148">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="4def2-149">L’exemple précédent exige que la disposition de l’application serveur ( `_Layout.cshtml` ) inclue une [section Render](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> ) pour le script à l’intérieur de la `</body>` balise de fermeture :</span><span class="sxs-lookup"><span data-stu-id="4def2-149">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="4def2-150">Le `_Layout.cshtml` fichier se trouve dans le `Pages/Shared` dossier d’une Razor application de pages ou `Views/Shared` d’un dossier dans une application MVC.</span><span class="sxs-lookup"><span data-stu-id="4def2-150">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="4def2-151">Si l’application doit également styliser les composants avec les styles de l' Blazor WebAssembly application, incluez les styles de l’application dans le `_Layout.cshtml` fichier.</span><span class="sxs-lookup"><span data-stu-id="4def2-151">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="4def2-152">Dans l’exemple suivant, l’espace de noms de l’application cliente est `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="4def2-152">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="additional-resources"></a><span data-ttu-id="4def2-153">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4def2-153">Additional resources</span></span>

* [<span data-ttu-id="4def2-154">Prendre en charge le prérendu avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="4def2-154">Support prerendering with authentication</span></span>](xref:blazor/security/webassembly/additional-scenarios#support-prerendering-with-authentication)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="4def2-155">L’intégration de composants dans des Razor Razor pages et des applications MVC dans une solution hébergée Blazor WebAssembly est prise en charge dans ASP.net core dans .net 5 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="4def2-155">Integrating Razor components into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="4def2-156">Sélectionnez une version .NET 5 ou ultérieure de cet article.</span><span class="sxs-lookup"><span data-stu-id="4def2-156">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="4def2-157">Razor les composants peuvent être intégrés dans des Razor pages et des applications MVC dans une Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="4def2-157">Razor components can be integrated into Razor Pages and MVC apps in a Blazor Server app.</span></span> <span data-ttu-id="4def2-158">Lorsque la page ou la vue est restituée, les composants peuvent être prérendus en même temps.</span><span class="sxs-lookup"><span data-stu-id="4def2-158">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="4def2-159">Après avoir [configuré l’application](#configuration), suivez les instructions des sections suivantes en fonction des exigences de l’application :</span><span class="sxs-lookup"><span data-stu-id="4def2-159">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="4def2-160">Composants routables : pour les composants qui sont directement routables à partir des demandes de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4def2-160">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="4def2-161">Suivez ces instructions lorsque les visiteurs doivent être en mesure de faire une requête HTTP dans leur navigateur pour un composant avec une [`@page`](xref:mvc/views/razor#page) directive.</span><span class="sxs-lookup"><span data-stu-id="4def2-161">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="4def2-162">Utiliser des composants routables dans une Razor application pages</span><span class="sxs-lookup"><span data-stu-id="4def2-162">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="4def2-163">Utiliser des composants routables dans une application MVC</span><span class="sxs-lookup"><span data-stu-id="4def2-163">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="4def2-164">[Rendez les composants à partir d’une page ou d’une vue](#render-components-from-a-page-or-view): pour les composants qui ne sont pas directement routables à partir des demandes de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4def2-164">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="4def2-165">Suivez ces instructions lorsque l’application incorpore des composants dans des pages et des vues existantes avec le [tag Helper Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4def2-165">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="4def2-166">Configuration</span><span class="sxs-lookup"><span data-stu-id="4def2-166">Configuration</span></span>

<span data-ttu-id="4def2-167">Une Razor application de pages ou MVC existante peut intégrer Razor des composants dans des pages et des vues :</span><span class="sxs-lookup"><span data-stu-id="4def2-167">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="4def2-168">Dans le fichier de disposition de l’application ( `_Layout.cshtml` ) :</span><span class="sxs-lookup"><span data-stu-id="4def2-168">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="4def2-169">Ajoutez la `<base>` balise suivante à l' `<head>` élément :</span><span class="sxs-lookup"><span data-stu-id="4def2-169">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="4def2-170">La `href` valeur (le *chemin d’accès de base* de l’application) dans l’exemple précédent suppose que l’application se trouve dans le chemin d’URL racine ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="4def2-170">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="4def2-171">Si l’application est une sous-application, suivez les instructions de la section *chemin d’accès de base* de l’application de l' <xref:blazor/host-and-deploy/index#app-base-path> article.</span><span class="sxs-lookup"><span data-stu-id="4def2-171">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="4def2-172">Le `_Layout.cshtml` fichier se trouve dans le `Pages/Shared` dossier d’une Razor application de pages ou `Views/Shared` d’un dossier dans une application MVC.</span><span class="sxs-lookup"><span data-stu-id="4def2-172">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="4def2-173">Ajoutez une `<script>` balise pour le `blazor.server.js` script immédiatement avant la `Scripts` section Render :</span><span class="sxs-lookup"><span data-stu-id="4def2-173">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="4def2-174">L’infrastructure ajoute le `blazor.server.js` script à l’application.</span><span class="sxs-lookup"><span data-stu-id="4def2-174">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="4def2-175">Il n’est pas nécessaire d’ajouter manuellement le script à l’application.</span><span class="sxs-lookup"><span data-stu-id="4def2-175">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="4def2-176">Ajoutez un `_Imports.razor` fichier au dossier racine du projet avec le contenu suivant (modifiez le dernier espace de noms,, en lui attribuant l' `MyAppNamespace` espace de noms de l’application) :</span><span class="sxs-lookup"><span data-stu-id="4def2-176">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="4def2-177">Dans `Startup.ConfigureServices` , inscrivez le Blazor Server service :</span><span class="sxs-lookup"><span data-stu-id="4def2-177">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="4def2-178">Dans `Startup.Configure` , ajoutez le Blazor point de terminaison Hub à `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="4def2-178">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="4def2-179">Intégrer des composants dans n’importe quelle page ou vue.</span><span class="sxs-lookup"><span data-stu-id="4def2-179">Integrate components into any page or view.</span></span> <span data-ttu-id="4def2-180">Pour plus d’informations, consultez la section [rendre les composants à partir d’une page ou d’une vue](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="4def2-180">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="4def2-181">Utiliser des composants routables dans une Razor application pages</span><span class="sxs-lookup"><span data-stu-id="4def2-181">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="4def2-182">*Cette section concerne l’ajout de composants qui sont directement routables à partir des demandes des utilisateurs.*</span><span class="sxs-lookup"><span data-stu-id="4def2-182">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="4def2-183">Pour prendre en charge les composants routables Razor dans les Razor applications pages :</span><span class="sxs-lookup"><span data-stu-id="4def2-183">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="4def2-184">Suivez les instructions de la section [configuration](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="4def2-184">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="4def2-185">Ajoutez un `App.razor` fichier à la racine du projet avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="4def2-185">Add an `App.razor` file to the project root with the following content:</span></span>

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

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="4def2-186">Ajoutez un `_Host.cshtml` fichier au `Pages` dossier avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="4def2-186">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="4def2-187">Les composants utilisent le `_Layout.cshtml` fichier partagé pour leur disposition.</span><span class="sxs-lookup"><span data-stu-id="4def2-187">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="4def2-188"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configure si le `App` composant :</span><span class="sxs-lookup"><span data-stu-id="4def2-188"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="4def2-189">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="4def2-189">Is prerendered into the page.</span></span>
   * <span data-ttu-id="4def2-190">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4def2-190">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="4def2-191">Pour plus d’informations sur le tag Helper Component, y compris le passage de paramètres et de la <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="4def2-191">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="4def2-192">Ajoutez un itinéraire de priorité basse pour la `_Host.cshtml` page à la configuration de point de terminaison dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="4def2-192">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="4def2-193">Ajoutez des composants routables à l’application.</span><span class="sxs-lookup"><span data-stu-id="4def2-193">Add routable components to the app.</span></span> <span data-ttu-id="4def2-194">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4def2-194">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="4def2-195">Pour plus d’informations sur les espaces de noms, consultez la section [espaces de noms de composants](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="4def2-195">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="4def2-196">Utiliser des composants routables dans une application MVC</span><span class="sxs-lookup"><span data-stu-id="4def2-196">Use routable components in an MVC app</span></span>

<span data-ttu-id="4def2-197">*Cette section concerne l’ajout de composants qui sont directement routables à partir des demandes des utilisateurs.*</span><span class="sxs-lookup"><span data-stu-id="4def2-197">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="4def2-198">Pour prendre en charge les composants routables Razor dans les applications MVC :</span><span class="sxs-lookup"><span data-stu-id="4def2-198">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="4def2-199">Suivez les instructions de la section [configuration](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="4def2-199">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="4def2-200">Ajoutez un `App.razor` fichier à la racine du projet avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="4def2-200">Add an `App.razor` file to the root of the project with the following content:</span></span>

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

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="4def2-201">Ajoutez un `_Host.cshtml` fichier au `Views/Home` dossier avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="4def2-201">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="4def2-202">Les composants utilisent le `_Layout.cshtml` fichier partagé pour leur disposition.</span><span class="sxs-lookup"><span data-stu-id="4def2-202">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="4def2-203"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configure si le `App` composant :</span><span class="sxs-lookup"><span data-stu-id="4def2-203"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="4def2-204">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="4def2-204">Is prerendered into the page.</span></span>
   * <span data-ttu-id="4def2-205">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4def2-205">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="4def2-206">Pour plus d’informations sur le tag Helper Component, y compris le passage de paramètres et de la <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="4def2-206">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="4def2-207">Ajoutez une action au contrôleur d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="4def2-207">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="4def2-208">Ajoutez un itinéraire de faible priorité pour l’action de contrôleur qui retourne la `_Host.cshtml` vue à la configuration du point de terminaison dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="4def2-208">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="4def2-209">Créez un `Pages` dossier et ajoutez des composants routables à l’application.</span><span class="sxs-lookup"><span data-stu-id="4def2-209">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="4def2-210">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4def2-210">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="4def2-211">Pour plus d’informations sur les espaces de noms, consultez la section [espaces de noms de composants](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="4def2-211">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="4def2-212">Rendre les composants à partir d’une page ou d’une vue</span><span class="sxs-lookup"><span data-stu-id="4def2-212">Render components from a page or view</span></span>

<span data-ttu-id="4def2-213">*Cette section se rapporte à l’ajout de composants à des pages ou à des vues, où les composants ne sont pas directement routés à partir des demandes de l’utilisateur.*</span><span class="sxs-lookup"><span data-stu-id="4def2-213">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="4def2-214">Pour afficher un composant à partir d’une page ou d’une vue, utilisez le [tag Helper Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4def2-214">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="4def2-215">Rendu des composants interactifs avec état</span><span class="sxs-lookup"><span data-stu-id="4def2-215">Render stateful interactive components</span></span>

<span data-ttu-id="4def2-216">Les composants interactifs avec état peuvent être ajoutés à une Razor page ou à une vue.</span><span class="sxs-lookup"><span data-stu-id="4def2-216">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="4def2-217">Lors du rendu de la page ou de la vue :</span><span class="sxs-lookup"><span data-stu-id="4def2-217">When the page or view renders:</span></span>

* <span data-ttu-id="4def2-218">Le composant est prérendu avec la page ou la vue.</span><span class="sxs-lookup"><span data-stu-id="4def2-218">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="4def2-219">L’état initial du composant utilisé pour le prérendu est perdu.</span><span class="sxs-lookup"><span data-stu-id="4def2-219">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="4def2-220">Un nouvel état de composant est créé lorsque la SignalR connexion est établie.</span><span class="sxs-lookup"><span data-stu-id="4def2-220">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="4def2-221">La Razor page suivante affiche un `Counter` composant :</span><span class="sxs-lookup"><span data-stu-id="4def2-221">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="4def2-222">Pour plus d’informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="4def2-222">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="4def2-223">Rendre les composants non interactifs</span><span class="sxs-lookup"><span data-stu-id="4def2-223">Render noninteractive components</span></span>

<span data-ttu-id="4def2-224">Dans la Razor page suivante, le `Counter` composant est rendu statiquement avec une valeur initiale spécifiée à l’aide d’un formulaire.</span><span class="sxs-lookup"><span data-stu-id="4def2-224">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="4def2-225">Étant donné que le composant est rendu statiquement, le composant n’est pas interactif :</span><span class="sxs-lookup"><span data-stu-id="4def2-225">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="4def2-226">Pour plus d’informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="4def2-226">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="4def2-227">Espaces de noms de composants</span><span class="sxs-lookup"><span data-stu-id="4def2-227">Component namespaces</span></span>

<span data-ttu-id="4def2-228">Lorsque vous utilisez un dossier personnalisé pour stocker les composants de l’application, ajoutez l’espace de noms représentant le dossier à la page/la vue ou au `_ViewImports.cshtml` fichier.</span><span class="sxs-lookup"><span data-stu-id="4def2-228">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="4def2-229">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="4def2-229">In the following example:</span></span>

* <span data-ttu-id="4def2-230">Accédez `MyAppNamespace` à l’espace de noms de l’application.</span><span class="sxs-lookup"><span data-stu-id="4def2-230">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="4def2-231">Si un dossier nommé `Components` n’est pas utilisé pour contenir les composants, accédez `Components` au dossier dans lequel se trouvent les composants.</span><span class="sxs-lookup"><span data-stu-id="4def2-231">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="4def2-232">Le `_ViewImports.cshtml` fichier se trouve dans le `Pages` dossier d’une Razor application pages ou dans le `Views` dossier d’une application MVC.</span><span class="sxs-lookup"><span data-stu-id="4def2-232">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="4def2-233">Pour plus d’informations, consultez <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="4def2-233">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
