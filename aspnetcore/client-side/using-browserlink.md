---
title: Lien du navigateur dans ASP.NET Core
author: ncarandini
description: Explique comment le lien du navigateur est une fonctionnalité de Visual Studio qui lie l’environnement de développement à un ou plusieurs navigateurs Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: client-side/using-browserlink
ms.openlocfilehash: 80f05acab55af973faf08b5db79ea4cbaf896b14
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054487"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="cf9d9-103">Lien du navigateur dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf9d9-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="cf9d9-104">Par [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)et [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="cf9d9-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="cf9d9-105">Le lien du navigateur est une fonctionnalité de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="cf9d9-106">Il crée un canal de communication entre l’environnement de développement et un ou plusieurs navigateurs Web.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="cf9d9-107">Vous pouvez utiliser le lien du navigateur pour actualiser votre application Web dans plusieurs navigateurs à la fois, ce qui est utile pour les tests entre navigateurs.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="cf9d9-108">Configuration des liens du navigateur</span><span class="sxs-lookup"><span data-stu-id="cf9d9-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cf9d9-109">Ajoutez le package [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) à votre projet.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="cf9d9-110">Pour les Razor Pages ASP.net Core ou les projets MVC, activez également la compilation du runtime des Razor fichiers ( *. cshtml* ) comme décrit dans <xref:mvc/views/view-compilation> .</span><span class="sxs-lookup"><span data-stu-id="cf9d9-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor ( *.cshtml* ) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="cf9d9-111">Razor les modifications de syntaxe sont appliquées uniquement lorsque la compilation du runtime a été activée.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="cf9d9-112">Lors de la conversion d’un projet ASP.NET Core 2,0 en ASP.NET Core 2,1 et de la transition vers le [AspNetCore Microsoft.. app](xref:fundamentals/metapackage-app), installez le package [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) pour la fonctionnalité de lien du navigateur.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="cf9d9-113">Les modèles de projet ASP.NET Core 2,1 utilisent le `Microsoft.AspNetCore.App` package par défaut.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="cf9d9-114">Les modèles de projet **application web** ASP.net Core 2,0, **vide** et **API Web** utilisent le sous- [package Microsoft. AspNetCore. All](xref:fundamentals/metapackage), qui contient une référence de package pour [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="cf9d9-114">The ASP.NET Core 2.0 **Web Application** , **Empty** , and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="cf9d9-115">Par conséquent, l’utilisation du `Microsoft.AspNetCore.All` package ne nécessite aucune action supplémentaire pour rendre le lien de navigateur disponible.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="cf9d9-116">Le modèle de projet d' **application Web** ASP.net Core 1. x a une référence de package pour le package [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) .</span><span class="sxs-lookup"><span data-stu-id="cf9d9-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="cf9d9-117">D’autres types de projets nécessitent l’ajout d’une référence de package à `Microsoft.VisualStudio.Web.BrowserLink` .</span><span class="sxs-lookup"><span data-stu-id="cf9d9-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="cf9d9-118">Configuration</span><span class="sxs-lookup"><span data-stu-id="cf9d9-118">Configuration</span></span>

<span data-ttu-id="cf9d9-119">Appelez `UseBrowserLink` dans la méthode `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="cf9d9-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="cf9d9-120">L' `UseBrowserLink` appel est généralement placé à l’intérieur d’un `if` bloc qui active uniquement le lien de navigateur dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="cf9d9-121">Exemple :</span><span class="sxs-lookup"><span data-stu-id="cf9d9-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="cf9d9-122">Pour plus d'informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="cf9d9-123">Procédure d’utilisation d’un lien de navigateur</span><span class="sxs-lookup"><span data-stu-id="cf9d9-123">How to use Browser Link</span></span>

<span data-ttu-id="cf9d9-124">Quand un projet de ASP.NET Core est ouvert, Visual Studio affiche le contrôle de barre d’outils lien de navigateur en regard du contrôle de barre d’outils **cible de débogage** :</span><span class="sxs-lookup"><span data-stu-id="cf9d9-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menu déroulant lien du navigateur](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="cf9d9-126">Dans le contrôle de barre d’outils lien de navigateur, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="cf9d9-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="cf9d9-127">Actualisez l’application Web dans plusieurs navigateurs à la fois.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="cf9d9-128">Ouvrez le **tableau de bord du lien de navigateur** .</span><span class="sxs-lookup"><span data-stu-id="cf9d9-128">Open the **Browser Link Dashboard** .</span></span>
* <span data-ttu-id="cf9d9-129">Activez ou désactivez le **lien du navigateur** .</span><span class="sxs-lookup"><span data-stu-id="cf9d9-129">Enable or disable **Browser Link** .</span></span> <span data-ttu-id="cf9d9-130">Remarque : le lien du navigateur est désactivé par défaut dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="cf9d9-131">Activez ou désactivez la [synchronisation automatique CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="cf9d9-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="cf9d9-132">Actualiser l’application Web dans plusieurs navigateurs à la fois</span><span class="sxs-lookup"><span data-stu-id="cf9d9-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="cf9d9-133">Pour choisir un seul navigateur Web à lancer au démarrage du projet, utilisez le menu déroulant du contrôle de barre d’outils de la **cible de débogage** :</span><span class="sxs-lookup"><span data-stu-id="cf9d9-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Menu déroulant F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="cf9d9-135">Pour ouvrir plusieurs navigateurs à la fois, choisissez **Parcourir avec...** dans la même liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="cf9d9-136">Maintenez la touche <kbd>CTRL</kbd> enfoncée pour sélectionner les navigateurs de votre choix, puis cliquez sur **Parcourir** :</span><span class="sxs-lookup"><span data-stu-id="cf9d9-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse** :</span></span>

![Ouvrir plusieurs navigateurs à la fois](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="cf9d9-138">La capture d’écran suivante montre Visual Studio avec la vue d’index ouverte et deux navigateurs ouverts :</span><span class="sxs-lookup"><span data-stu-id="cf9d9-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Exemple de synchronisation avec deux navigateurs](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="cf9d9-140">Pointez sur le contrôle de barre d’outils lien du navigateur pour afficher les navigateurs qui sont connectés au projet :</span><span class="sxs-lookup"><span data-stu-id="cf9d9-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Pointe pointage](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="cf9d9-142">Modifiez la vue index et tous les navigateurs connectés sont mis à jour lorsque vous cliquez sur le bouton actualiser le lien du navigateur :</span><span class="sxs-lookup"><span data-stu-id="cf9d9-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![navigateurs-synchronisation-à-modification](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="cf9d9-144">Le lien du navigateur fonctionne également avec les navigateurs que vous lancez en dehors de Visual Studio et accédez à l’URL de l’application.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="cf9d9-145">Tableau de bord du lien de navigateur</span><span class="sxs-lookup"><span data-stu-id="cf9d9-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="cf9d9-146">Ouvrez la fenêtre **tableau de bord du lien de navigateur** dans le menu déroulant de lien du navigateur pour gérer la connexion avec les navigateurs ouverts :</span><span class="sxs-lookup"><span data-stu-id="cf9d9-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![ouvrir-browserslink-tableau de bord](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="cf9d9-148">Si aucun navigateur n’est connecté, vous pouvez démarrer une session de non-débogage en sélectionnant le lien **afficher dans le navigateur** :</span><span class="sxs-lookup"><span data-stu-id="cf9d9-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-tableau de bord-sans-connexions](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="cf9d9-150">Dans le cas contraire, les navigateurs connectés affichent le chemin d’accès à la page affichée par chaque navigateur :</span><span class="sxs-lookup"><span data-stu-id="cf9d9-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-tableau de bord-deux-connexions](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="cf9d9-152">Vous pouvez également cliquer sur un nom de navigateur individuel pour actualiser uniquement ce navigateur.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="cf9d9-153">Activer ou désactiver le lien du navigateur</span><span class="sxs-lookup"><span data-stu-id="cf9d9-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="cf9d9-154">Lorsque vous réactivez le lien de navigateur après l’avoir désactivé, vous devez actualiser les navigateurs pour les reconnecter.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="cf9d9-155">Activer ou désactiver la synchronisation automatique CSS</span><span class="sxs-lookup"><span data-stu-id="cf9d9-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="cf9d9-156">Lorsque la synchronisation automatique CSS est activée, les navigateurs connectés sont actualisés automatiquement lorsque vous apportez des modifications aux fichiers CSS.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="cf9d9-157">Fonctionnement</span><span class="sxs-lookup"><span data-stu-id="cf9d9-157">How it works</span></span>

<span data-ttu-id="cf9d9-158">Le lien du navigateur utilise [SignalR](xref:signalr/introduction) pour créer un canal de communication entre Visual Studio et le navigateur.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="cf9d9-159">Lorsque le lien du navigateur est activé, Visual Studio agit comme un SignalR serveur auquel plusieurs clients (navigateurs) peuvent se connecter.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="cf9d9-160">Le lien du navigateur inscrit également un composant d’intergiciel dans le pipeline de demande ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="cf9d9-161">Ce composant injecte `<script>` des références spéciales dans chaque demande de page à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="cf9d9-162">Vous pouvez voir les références de script en sélectionnant **afficher la source** dans le navigateur et en faisant défiler jusqu’à la fin du contenu de la `<body>` balise :</span><span class="sxs-lookup"><span data-stu-id="cf9d9-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="cf9d9-163">Vos fichiers sources ne sont pas modifiés.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-163">Your source files aren't modified.</span></span> <span data-ttu-id="cf9d9-164">Le composant intergiciel (middleware) injecte les références de script de manière dynamique.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="cf9d9-165">Étant donné que le code côté navigateur est tout JavaScript, il fonctionne sur tous les navigateurs qui SignalR prennent en charge sans nécessiter de plug-in de navigateur.</span><span class="sxs-lookup"><span data-stu-id="cf9d9-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
