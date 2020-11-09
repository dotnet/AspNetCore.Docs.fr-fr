---
title: 'Modèles de ASP.NET Core Blazor'
author: guardrex
description: 'En savoir plus sur Blazor les modèles d’application ASP.net Core et la Blazor structure de projet.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
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
uid: blazor/templates
ms.openlocfilehash: eef381367d7aa59dcc430c529746088d4488e700
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054929"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="cfd8b-103">Modèles de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="cfd8b-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="cfd8b-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cfd8b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cfd8b-105">L' Blazor infrastructure fournit des modèles pour développer des applications pour chacun des Blazor modèles d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="cfd8b-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="cfd8b-106">Blazor WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="cfd8b-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="cfd8b-107">Blazor Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="cfd8b-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="cfd8b-108">Pour plus d’informations sur les Blazor modèles d’hébergement de, consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="cfd8b-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="cfd8b-109">Les options de modèle sont disponibles en passant l' `--help` option à la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande CLI :</span><span class="sxs-lookup"><span data-stu-id="cfd8b-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="cfd8b-110">Blazor structure de projet</span><span class="sxs-lookup"><span data-stu-id="cfd8b-110">Blazor project structure</span></span>

<span data-ttu-id="cfd8b-111">Les fichiers et dossiers suivants composent une Blazor application générée à partir d’un Blazor modèle de projet :</span><span class="sxs-lookup"><span data-stu-id="cfd8b-111">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

* <span data-ttu-id="cfd8b-112">`Program.cs`: Le point d’entrée de l’application qui installe les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="cfd8b-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="cfd8b-113">[Hôte](xref:fundamentals/host/generic-host) ASP.net Core ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="cfd8b-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="cfd8b-114">Hôte webassembly ( Blazor WebAssembly ) : le code de ce fichier est propre aux applications créées à partir du Blazor WebAssembly modèle ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="cfd8b-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="cfd8b-115">Le `App` composant est le composant racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="cfd8b-116">Le `App` composant est spécifié en tant qu' `app` élément DOM ( `<app>...</app>` ) à la collection de composants racine ( `builder.RootComponents.Add<App>("app")` ).</span><span class="sxs-lookup"><span data-stu-id="cfd8b-116">The `App` component is specified as the `app` DOM element (`<app>...</app>`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="cfd8b-117">Les [services](xref:blazor/fundamentals/dependency-injection) sont ajoutés et configurés (par exemple, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).</span><span class="sxs-lookup"><span data-stu-id="cfd8b-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

* <span data-ttu-id="cfd8b-118">`Startup.cs` ( Blazor Server ) : Contient la logique de démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-118">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="cfd8b-119">La `Startup` classe définit deux méthodes :</span><span class="sxs-lookup"><span data-stu-id="cfd8b-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="cfd8b-120">`ConfigureServices`: Configure les services d' [injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-120">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="cfd8b-121">Dans les Blazor Server applications, les services sont ajoutés en appelant <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> , et le `WeatherForecastService` est ajouté au conteneur de service pour une utilisation par l’exemple de `FetchData` composant.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="cfd8b-122">`Configure`: Configure le pipeline de traitement des demandes de l’application :</span><span class="sxs-lookup"><span data-stu-id="cfd8b-122">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="cfd8b-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> est appelé pour configurer un point de terminaison pour la connexion en temps réel avec le navigateur.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="cfd8b-124">La connexion est créée avec [SignalR](xref:signalr/introduction) , qui est une infrastructure permettant d’ajouter des fonctionnalités Web en temps réel aux applications.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="cfd8b-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) est appelé pour configurer la page racine de l’application ( `Pages/_Host.cshtml` ) et activer la navigation.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="cfd8b-126">`wwwroot/index.html` ( Blazor WebAssembly ) : Page racine de l’application implémentée en tant que page HTML :</span><span class="sxs-lookup"><span data-stu-id="cfd8b-126">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="cfd8b-127">Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="cfd8b-128">La page spécifie l’emplacement où le `App` composant racine est restitué.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="cfd8b-129">Le composant est rendu à l’emplacement de l' `app` élément DOM ( `<app>...</app>` ).</span><span class="sxs-lookup"><span data-stu-id="cfd8b-129">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="cfd8b-130">Le `_framework/blazor.webassembly.js` fichier JavaScript est chargé, ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="cfd8b-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="cfd8b-131">Télécharge le Runtime .NET, l’application et les dépendances de l’application.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="cfd8b-132">Initialise le runtime pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-132">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="cfd8b-133">`App.razor`: Composant racine de l’application qui configure le routage côté client à l’aide du <xref:Microsoft.AspNetCore.Components.Routing.Router> composant.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-133">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="cfd8b-134">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant intercepte la navigation dans le navigateur et affiche la page qui correspond à l’adresse demandée.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="cfd8b-135">`Pages` dossier : contient les composants/pages routables ( `.razor` ) qui composent l' Blazor application et la Razor page racine d’une Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-135">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="cfd8b-136">L’itinéraire de chaque page est spécifié à l’aide de la [`@page`](xref:mvc/views/razor#page) directive.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-136">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="cfd8b-137">Le modèle comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="cfd8b-137">The template includes the following:</span></span>
  * <span data-ttu-id="cfd8b-138">`_Host.cshtml` ( Blazor Server ) : Page racine de l’application implémentée en tant que Razor Pagination</span><span class="sxs-lookup"><span data-stu-id="cfd8b-138">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="cfd8b-139">Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-139">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="cfd8b-140">Le `_framework/blazor.server.js` fichier JavaScript est chargé, ce qui configure la connexion en temps réel SignalR entre le navigateur et le serveur.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-140">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="cfd8b-141">La page hôte spécifie où le `App` composant racine ( `App.razor` ) est affiché.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-141">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="cfd8b-142">`Counter` ( `Pages/Counter.razor` ) : Implémente la page de compteur.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-142">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="cfd8b-143">`Error` ( `Error.razor` , Blazor Server application uniquement) : rendu lorsqu’une exception non gérée se produit dans l’application.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-143">`Error` (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="cfd8b-144">`FetchData` ( `Pages/FetchData.razor` ) : Implémente la page FETCH Data.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-144">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="cfd8b-145">`Index` ( `Pages/Index.razor` ) : Implémente la page d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-145">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="cfd8b-146">`Properties/launchSettings.json`: Contient la configuration de l' [environnement de développement](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="cfd8b-146">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

* <span data-ttu-id="cfd8b-147">`Shared` dossier : contient d’autres composants d’interface utilisateur ( `.razor` ) utilisés par l’application :</span><span class="sxs-lookup"><span data-stu-id="cfd8b-147">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="cfd8b-148">`MainLayout` ( `MainLayout.razor` ) : [Composant de disposition](xref:blazor/layouts)de l’application.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-148">`MainLayout` (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="cfd8b-149">`NavMenu` ( `NavMenu.razor` ) : Implémente la navigation dans l’encadré.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-149">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="cfd8b-150">Comprend le [ `NavLink` composant](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), qui affiche des liens de navigation vers d’autres Razor composants.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-150">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="cfd8b-151">Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant indique automatiquement un état sélectionné quand son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="cfd8b-152">`_Imports.razor`: Inclut des Razor directives communes à inclure dans les composants de l’application ( `.razor` ), tels que les [`@using`](xref:mvc/views/razor#using) directives pour les espaces de noms.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-152">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="cfd8b-153">`Data` dossier ( Blazor Server ) : contient la `WeatherForecast` classe et l’implémentation du `WeatherForecastService` qui fournissent des exemples de données météorologiques au composant de l’application `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="cfd8b-153">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="cfd8b-154">`wwwroot`: Dossier [racine Web](xref:fundamentals/index#web-root) de l’application contenant les ressources statiques publiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-154">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="cfd8b-155">`appsettings.json`: Contient les [paramètres de configuration](xref:blazor/fundamentals/configuration) de l’application.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-155">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="cfd8b-156">Dans une Blazor WebAssembly application, le fichier de paramètres d’application se trouve dans le `wwwroot` dossier.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-156">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="cfd8b-157">Dans une Blazor Server application, le fichier de paramètres d’application se trouve à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="cfd8b-157">In a Blazor Server app, the app settings file is located at the project root.</span></span>
