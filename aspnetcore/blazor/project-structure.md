---
title: BlazorStructure de projet ASP.net Core
author: guardrex
description: En savoir plus sur ASP.NET Core Blazor structure de projet d’application.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2021
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
uid: blazor/project-structure
ms.openlocfilehash: ae41d096c50d350b7fcde52da59382614e62c109
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658623"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a>BlazorStructure de projet ASP.net Core

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

Cet article décrit les fichiers et les dossiers qui composent une Blazor application générée à partir des Blazor modèles de projet.

## Blazor WebAssembly

Le Blazor WebAssembly modèle ( `blazorwasm` ) crée les fichiers et la structure de répertoire initiaux pour une Blazor WebAssembly application. L’application est remplie avec un code de démonstration pour un `FetchData` composant qui charge des données à partir d’une ressource statique, et de l' `weather.json` interaction utilisateur avec un `Counter` composant.

* `Pages` dossier : contient les composants/pages routables ( `.razor` ) qui composent l' Blazor application. L’itinéraire de chaque page est spécifié à l’aide de la [`@page`](xref:mvc/views/razor#page) directive. Le modèle comprend les composants suivants :
  * `Counter` Component ( `Counter.razor` ) : implémente la page de compteur.
  * `FetchData` Component ( `FetchData.razor` ) : implémente la page FETCH Data.
  * `Index` Component ( `Index.razor` ) : implémente la page d’hébergement.
  
* `Properties/launchSettings.json`: Contient la configuration de l' [environnement de développement](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* `Shared` dossier : contient les composants et feuilles de style partagés suivants :
  * `MainLayout` Component ( `MainLayout.razor` ) : [composant de disposition](xref:blazor/layouts)de l’application.
  * `MainLayout.razor.css`: Feuille de style de la disposition principale de l’application.
  * `NavMenu` Component ( `NavMenu.razor` ) : implémente la navigation dans l’encadré. Comprend le [ `NavLink` composant](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), qui affiche des liens de navigation vers d’autres Razor composants. Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant indique automatiquement un état sélectionné quand son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.
  * `NavMenu.razor.css`: Feuille de style du menu de navigation de l’application.
  * `SurveyPrompt` composant ( `SurveyPrompt.razor` ) : Blazor composant d’enquête.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` dossier : contient les composants partagés suivants :
  * `MainLayout` Component ( `MainLayout.razor` ) : [composant de disposition](xref:blazor/layouts)de l’application.
  * `NavMenu` Component ( `NavMenu.razor` ) : implémente la navigation dans l’encadré. Comprend le [ `NavLink` composant](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), qui affiche des liens de navigation vers d’autres Razor composants. Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant indique automatiquement un état sélectionné quand son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.
  * `SurveyPrompt` composant ( `SurveyPrompt.razor` ) : Blazor composant d’enquête.
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `wwwroot`: Dossier [racine Web](xref:fundamentals/index#web-root) de l’application contenant les ressources statiques publiques de l’application, y compris les `appsettings.json` fichiers de paramètres de l’application environnementale et des [paramètres de configuration](xref:blazor/fundamentals/configuration). La `index.html` page Web est la page racine de l’application implémentée en tant que page HTML :
  * Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.
  * La page spécifie l’emplacement où le `App` composant racine est restitué. Le composant est rendu à l’emplacement de l' `div` élément DOM avec un `id` de `app` ( `<div id="app">Loading...</div>` ).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot`: Dossier [racine Web](xref:fundamentals/index#web-root) de l’application contenant les ressources statiques publiques de l’application, y compris les `appsettings.json` fichiers de paramètres de l’application environnementale et des [paramètres de configuration](xref:blazor/fundamentals/configuration). La `index.html` page Web est la page racine de l’application implémentée en tant que page HTML :
  * Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.
  * La page spécifie l’emplacement où le `App` composant racine est restitué. Le composant est rendu à l’emplacement de l' `app` élément DOM ( `<app>Loading...</app>` ).

::: moniker-end

* `_Imports.razor`: Inclut des Razor directives communes à inclure dans les composants de l’application ( `.razor` ), tels que les [`@using`](xref:mvc/views/razor#using) directives pour les espaces de noms.

* `App.razor`: Composant racine de l’application qui configure le routage côté client à l’aide du <xref:Microsoft.AspNetCore.Components.Routing.Router> composant. Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant intercepte la navigation dans le navigateur et affiche la page qui correspond à l’adresse demandée.

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`: Le point d’entrée de l’application qui installe l’hôte webassembly :
  
  * Le `App` composant est le composant racine de l’application. Le `App` composant est spécifié en tant qu' `div` élément DOM avec un `id` de `app` ( `<div id="app">Loading...</div>` dans `wwwroot/index.html` ) à la collection de composants racine ( `builder.RootComponents.Add<App>("#app")` ).
  * Les [services](xref:blazor/fundamentals/dependency-injection) sont ajoutés et configurés (par exemple, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`: Le point d’entrée de l’application qui installe l’hôte webassembly :

  * Le `App` composant est le composant racine de l’application. Le `App` composant est spécifié en tant qu' `app` élément DOM ( `<app>Loading...</app>` dans `wwwroot/index.html` ) à la collection de composants racine ( `builder.RootComponents.Add<App>("app")` ).
  * Les [services](xref:blazor/fundamentals/dependency-injection) sont ajoutés et configurés (par exemple, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

::: moniker-end

## Blazor Server

Le Blazor Server modèle ( `blazorserver` ) crée les fichiers et la structure de répertoire initiaux pour une Blazor Server application. L’application est remplie avec le code de démonstration pour un `FetchData` composant qui charge des données à partir d’un service inscrit, et de l' `WeatherForecastService` interaction utilisateur avec un `Counter` composant.

* `Data` dossier : contient la `WeatherForecast` classe et l’implémentation du `WeatherForecastService` qui fournit des exemples de données météorologiques au composant de l’application `FetchData` .

* `Pages` dossier : contient les composants/pages routables ( `.razor` ) qui composent l' Blazor application et la Razor page racine d’une Blazor Server application. L’itinéraire de chaque page est spécifié à l’aide de la [`@page`](xref:mvc/views/razor#page) directive. Le modèle comprend les éléments suivants :
  * `_Host.cshtml`: Page racine de l’application implémentée en tant que Razor page :
    * Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.
    * La page hôte spécifie où le `App` composant racine ( `App.razor` ) est affiché.
  * `Counter` Component ( `Counter.razor` ) : implémente la page de compteur.
  * `Error` Component ( `Error.razor` ) : rendu lorsqu’une exception non gérée se produit dans l’application.
  * `FetchData` Component ( `FetchData.razor` ) : implémente la page FETCH Data.
  * `Index` Component ( `Index.razor` ) : implémente la page d’hébergement.
  
* `Properties/launchSettings.json`: Contient la configuration de l' [environnement de développement](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* `Shared` dossier : contient les composants et feuilles de style partagés suivants :
  * `MainLayout` Component ( `MainLayout.razor` ) : [composant de disposition](xref:blazor/layouts)de l’application.
  * `MainLayout.razor.css`: Feuille de style de la disposition principale de l’application.
  * `NavMenu` Component ( `NavMenu.razor` ) : implémente la navigation dans l’encadré. Comprend le [ `NavLink` composant](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), qui affiche des liens de navigation vers d’autres Razor composants. Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant indique automatiquement un état sélectionné quand son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.
  * `NavMenu.razor.css`: Feuille de style du menu de navigation de l’application.
  * `SurveyPrompt` composant ( `SurveyPrompt.razor` ) : Blazor composant d’enquête.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` dossier : contient les composants partagés suivants :
  * `MainLayout` Component ( `MainLayout.razor` ) : [composant de disposition](xref:blazor/layouts)de l’application.
  * `NavMenu` Component ( `NavMenu.razor` ) : implémente la navigation dans l’encadré. Comprend le [ `NavLink` composant](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), qui affiche des liens de navigation vers d’autres Razor composants. Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant indique automatiquement un état sélectionné quand son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.
  * `SurveyPrompt` composant ( `SurveyPrompt.razor` ) : Blazor composant d’enquête.
  
::: moniker-end

* `wwwroot`: Dossier [racine Web](xref:fundamentals/index#web-root) de l’application contenant les ressources statiques publiques de l’application.

* `_Imports.razor`: Inclut des Razor directives communes à inclure dans les composants de l’application ( `.razor` ), tels que les [`@using`](xref:mvc/views/razor#using) directives pour les espaces de noms.

* `App.razor`: Composant racine de l’application qui configure le routage côté client à l’aide du <xref:Microsoft.AspNetCore.Components.Routing.Router> composant. Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant intercepte la navigation dans le navigateur et affiche la page qui correspond à l’adresse demandée.

* `appsettings.json` et les fichiers de paramètres d’application environnementale : fournissez des [paramètres de configuration](xref:blazor/fundamentals/configuration) pour l’application.

* `Program.cs`: Point d’entrée de l’application qui configure l' [hôte](xref:fundamentals/host/generic-host)ASP.net core.

* `Startup.cs`: Contient la logique de démarrage de l’application. La `Startup` classe définit deux méthodes :

  * `ConfigureServices`: Configure les services d' [injection de dépendances](xref:fundamentals/dependency-injection) de l’application. Les services sont ajoutés en appelant <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> , et `WeatherForecastService` sont ajoutés au conteneur de service pour être utilisés par l’exemple de `FetchData` composant.
  * `Configure`: Configure le pipeline de traitement des demandes de l’application :
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> est appelé pour configurer un point de terminaison pour la connexion en temps réel avec le navigateur. La connexion est créée avec [SignalR](xref:signalr/introduction) , qui est une infrastructure permettant d’ajouter des fonctionnalités Web en temps réel aux applications.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) est appelé pour configurer la page racine de l’application ( `Pages/_Host.cshtml` ) et activer la navigation.
