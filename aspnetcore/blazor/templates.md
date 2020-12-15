---
title: Modèles de ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur Blazor les modèles d’application ASP.net Core et la Blazor structure de projet.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
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
uid: blazor/templates
ms.openlocfilehash: 6d24e65acd44f98540575a3f3880fb137bfc822f
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485912"
---
# <a name="aspnet-core-no-locblazor-templates"></a>Modèles de ASP.NET Core Blazor

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

L' Blazor infrastructure fournit des modèles pour développer des applications pour chacun des Blazor modèles d’hébergement :

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Pour plus d’informations sur les Blazor modèles d’hébergement de, consultez <xref:blazor/hosting-models> .

Les options de modèle sont disponibles en passant l' `--help` option à la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande CLI :

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a>Blazor structure de projet

Les fichiers et dossiers suivants composent une Blazor application générée à partir d’un Blazor modèle de projet :

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`: Le point d’entrée de l’application qui installe les éléments suivants :

  * [Hôte](xref:fundamentals/host/generic-host) ASP.net Core ( Blazor Server )
  * Hôte webassembly ( Blazor WebAssembly ) : le code de ce fichier est propre aux applications créées à partir du Blazor WebAssembly modèle ( `blazorwasm` ).
    * Le `App` composant est le composant racine de l’application. Le `App` composant est spécifié en tant qu' `div` élément DOM avec un `id` de `app` ( `<div id="app">Loading...</div>` dans `wwwroot/index.html` ) à la collection de composants racine ( `builder.RootComponents.Add<App>("#app")` ).
    * Les [services](xref:blazor/fundamentals/dependency-injection) sont ajoutés et configurés (par exemple, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`: Le point d’entrée de l’application qui installe les éléments suivants :

  * [Hôte](xref:fundamentals/host/generic-host) ASP.net Core ( Blazor Server )
  * Hôte webassembly ( Blazor WebAssembly ) : le code de ce fichier est propre aux applications créées à partir du Blazor WebAssembly modèle ( `blazorwasm` ).
    * Le `App` composant est le composant racine de l’application. Le `App` composant est spécifié en tant qu' `app` élément DOM ( `<app>Loading...</app>` dans `wwwroot/index.html` ) à la collection de composants racine ( `builder.RootComponents.Add<App>("app")` ).
    * Les [services](xref:blazor/fundamentals/dependency-injection) sont ajoutés et configurés (par exemple, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

::: moniker-end

* `Startup.cs` ( Blazor Server ) : Contient la logique de démarrage de l’application. La `Startup` classe définit deux méthodes :

  * `ConfigureServices`: Configure les services d' [injection de dépendances](xref:fundamentals/dependency-injection) de l’application. Dans les Blazor Server applications, les services sont ajoutés en appelant <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> , et le `WeatherForecastService` est ajouté au conteneur de service pour une utilisation par l’exemple de `FetchData` composant.
  * `Configure`: Configure le pipeline de traitement des demandes de l’application :
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> est appelé pour configurer un point de terminaison pour la connexion en temps réel avec le navigateur. La connexion est créée avec [SignalR](xref:signalr/introduction) , qui est une infrastructure permettant d’ajouter des fonctionnalités Web en temps réel aux applications.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) est appelé pour configurer la page racine de l’application ( `Pages/_Host.cshtml` ) et activer la navigation.

::: moniker range=">= aspnetcore-5.0"

* `wwwroot/index.html` ( Blazor WebAssembly ) : Page racine de l’application implémentée en tant que page HTML :
  * Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.
  * La page spécifie l’emplacement où le `App` composant racine est restitué. Le composant est rendu à l’emplacement de l' `div` élément DOM avec un `id` de `app` ( `<div id="app">Loading...</div>` ).
  * Le `_framework/blazor.webassembly.js` fichier JavaScript est chargé, ce qui suit :
    * Télécharge le Runtime .NET, l’application et les dépendances de l’application.
    * Initialise le runtime pour exécuter l’application.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot/index.html` ( Blazor WebAssembly ) : Page racine de l’application implémentée en tant que page HTML :
  * Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.
  * La page spécifie l’emplacement où le `App` composant racine est restitué. Le composant est rendu à l’emplacement de l' `app` élément DOM ( `<app>Loading...</app>` ).
  * Le `_framework/blazor.webassembly.js` fichier JavaScript est chargé, ce qui suit :
    * Télécharge le Runtime .NET, l’application et les dépendances de l’application.
    * Initialise le runtime pour exécuter l’application.
    
::: moniker-end

* `App.razor`: Composant racine de l’application qui configure le routage côté client à l’aide du <xref:Microsoft.AspNetCore.Components.Routing.Router> composant. Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant intercepte la navigation dans le navigateur et affiche la page qui correspond à l’adresse demandée.

* `Pages` dossier : contient les composants/pages routables ( `.razor` ) qui composent l' Blazor application et la Razor page racine d’une Blazor Server application. L’itinéraire de chaque page est spécifié à l’aide de la [`@page`](xref:mvc/views/razor#page) directive. Le modèle comprend les éléments suivants :
  * `_Host.cshtml` ( Blazor Server ) : Page racine de l’application implémentée en tant que Razor Pagination
    * Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.
    * Le `_framework/blazor.server.js` fichier JavaScript est chargé, ce qui configure la connexion en temps réel SignalR entre le navigateur et le serveur.
    * La page hôte spécifie où le `App` composant racine ( `App.razor` ) est affiché.
  * `Counter` Component ( `Pages/Counter.razor` ) : implémente la page de compteur.
  * `Error` composant ( `Error.razor` , Blazor Server application uniquement) : rendu lorsqu’une exception non gérée se produit dans l’application.
  * `FetchData` Component ( `Pages/FetchData.razor` ) : implémente la page FETCH Data.
  * `Index` Component ( `Pages/Index.razor` ) : implémente la page d’hébergement.
  
* `Properties/launchSettings.json`: Contient la configuration de l' [environnement de développement](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* `Shared` dossier : contient d’autres composants d’interface utilisateur ( `.razor` ) utilisés par l’application :
  * `MainLayout` Component ( `MainLayout.razor` ) : [composant de disposition](xref:blazor/layouts)de l’application.
  * `MainLayout.razor.css`: Feuille de style de la disposition principale de l’application.
  * `NavMenu` Component ( `NavMenu.razor` ) : implémente la navigation dans l’encadré. Comprend le [ `NavLink` composant](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), qui affiche des liens de navigation vers d’autres Razor composants. Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant indique automatiquement un état sélectionné quand son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.
  * `NavMenu.razor.css`: Feuille de style du menu de navigation de l’application.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` dossier : contient d’autres composants d’interface utilisateur ( `.razor` ) utilisés par l’application :
  * `MainLayout` Component ( `MainLayout.razor` ) : [composant de disposition](xref:blazor/layouts)de l’application.
  * `NavMenu` Component ( `NavMenu.razor` ) : implémente la navigation dans l’encadré. Comprend le [ `NavLink` composant](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), qui affiche des liens de navigation vers d’autres Razor composants. Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant indique automatiquement un état sélectionné quand son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.
  
::: moniker-end

* `_Imports.razor`: Inclut des Razor directives communes à inclure dans les composants de l’application ( `.razor` ), tels que les [`@using`](xref:mvc/views/razor#using) directives pour les espaces de noms.

* `Data` dossier ( Blazor Server ) : contient la `WeatherForecast` classe et l’implémentation du `WeatherForecastService` qui fournissent des exemples de données météorologiques au composant de l’application `FetchData` .

* `wwwroot`: Dossier [racine Web](xref:fundamentals/index#web-root) de l’application contenant les ressources statiques publiques de l’application.

* `appsettings.json`: Contient les [paramètres de configuration](xref:blazor/fundamentals/configuration) de l’application. Dans une Blazor WebAssembly application, le fichier de paramètres d’application se trouve dans le `wwwroot` dossier. Dans une Blazor Server application, le fichier de paramètres d’application se trouve à la racine du projet.
