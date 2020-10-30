---
title: Démarrage d’une application dans ASP.NET Core
author: rick-anderson
description: Découvrez comment la classe Startup en ASP.NET Core configure les services et le pipeline de requête de l’application.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/startup
ms.openlocfilehash: 747b13abb0ce3fed2d1dc018c6dbf82db1ae7130
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052238"
---
# <a name="app-startup-in-aspnet-core"></a>Démarrage d’une application dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) et [Steve Smith](https://ardalis.com)

La classe `Startup` configure des services et le pipeline de requête de l’application.

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a>Classe Startup

Les applications ASP.NET Core utilisent une classe `Startup`, appelée `Startup` par convention. La classe `Startup` :

* Inclut éventuellement une méthode <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> pour configurer les *services* de l’application. Un service est un composant réutilisable qui fournit une fonctionnalité d’application. Les services sont *enregistrés* dans `ConfigureServices` et utilisés dans l’ensemble de l’application via l' [injection de dépendances (di)](xref:fundamentals/dependency-injection) ou <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .
* Inclut une méthode <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> pour créer le pipeline de traitement des requêtes de l’application.

`ConfigureServices` et `Configure` sont appelés par le runtime ASP.NET Core au lancement de l’application :

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

L’exemple précédent concerne les [ Razor pages](xref:razor-pages/index); la version MVC est similaire.


La classe `Startup` est spécifiée quand l’[hôte](xref:fundamentals/index#host) de l’application est créé. La `Startup` classe est généralement spécifiée en appelant la méthode [WebHostBuilderExtensions. \<TStartup> UseStartup](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) sur le générateur d’ordinateur hôte :

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

L’hôte fournit des services accessibles au constructeur de classe `Startup`. L’application ajoute des services supplémentaires à l’aide de `ConfigureServices`. Les services de l’hôte ainsi que ceux de l’application sont disponibles dans `Configure` et dans l’ensemble de l’application.

Seuls les types de service suivants peuvent être injectés dans le `Startup` constructeur lors de l’utilisation de l' [hôte générique](xref:fundamentals/host/generic-host) ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ) :

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

La plupart des services ne sont pas disponibles tant que la méthode `Configure` n’est pas appelée.

### <a name="multiple-startup"></a>Démarrage multiple

Quand l’application définit différentes classes `Startup` pour différents environnements (par exemple, `StartupDevelopment`), la classe `Startup` appropriée est sélectionnée au moment de l’exécution. La classe dont le suffixe du nom correspond à l'environnement actuel est prioritaire. Si l’application est exécutée dans l’environnement de développement et qu’elle inclut à la fois une classe `Startup` et une classe `StartupDevelopment`, la classe `StartupDevelopment` est utilisée. Pour plus d’informations, consultez [Utiliser plusieurs environnements](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Pour plus d’informations sur l’hôte, consultez [L’hôte](xref:fundamentals/index#host). Pour plus d’informations sur la gestion des erreurs lors du démarrage, consultez [Gestion des exceptions de démarrage](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Méthode ConfigureServices

La méthode <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> est :

* Optionnel.
* Appelée par l’hôte avant la méthode `Configure` pour configurer les services de l’application
* L’emplacement où les [options de configuration](xref:fundamentals/configuration/index) sont définies par convention.

L’hôte peut configurer certains services avant l’appel des méthodes `Startup`. Pour plus d’informations, consultez [L’hôte](xref:fundamentals/index#host).

Pour les fonctionnalités qui nécessitent une configuration importante, il existe des méthodes d’extension `Add{Service}` pour <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. Par exemple, **Add** DbContext, **Add** default Identity , **Add** EntityFrameworkStores et **Add** Razor pages :

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

L’ajout de services au conteneur de service les rend disponibles dans l’application et dans la méthode `Configure`. Les services sont résolus via l’[injection de dépendances](xref:fundamentals/dependency-injection) ou à partir de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

## <a name="the-configure-method"></a>Méthode Configure

La méthode <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> permet de spécifier le mode de réponse de l’application aux requêtes HTTP. Vous configurez le pipeline de requête en ajoutant des composants d’[intergiciel (middleware)](xref:fundamentals/middleware/index) à une instance de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` est disponible pour la méthode `Configure`, mais elle n’est pas inscrite dans le conteneur de service. L’hébergement crée un `IApplicationBuilder` et le passe directement à `Configure`.

Les [modèles ASP.NET Core](/dotnet/core/tools/dotnet-new) configurent le pipeline pour permettre la prise en charge des éléments suivants :

* [Page exception du développeur](xref:fundamentals/error-handling#developer-exception-page)
* [Gestionnaire d’exceptions](xref:fundamentals/error-handling#exception-handler-page)
* [HSTS (HTTP Strict Transport Security)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Redirection HTTPS](xref:security/enforcing-ssl)
* [Fichiers statiques](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) et [ Razor pages](xref:razor-pages/index)


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

L’exemple précédent concerne les [ Razor pages](xref:razor-pages/index); la version MVC est similaire.

Chaque méthode d’extension `Use` ajoute un ou plusieurs composants de middleware au pipeline de requête. Par exemple, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configure le [middleware](xref:fundamentals/middleware/index) pour qu’il traite des [fichiers statiques](xref:fundamentals/static-files).

Chaque composant de middleware du pipeline des requêtes est chargé d’appeler le composant suivant du pipeline ou de court-circuiter la chaîne, si c’est approprié.

Vous pouvez spécifier des services supplémentaires (comme `IWebHostEnvironment`, `ILoggerFactory` et tout service défini dans `ConfigureServices`) dans la signature de la méthode `Configure`. Ces services sont injectés s’ils sont disponibles.

Pour plus d’informations sur l’utilisation de `IApplicationBuilder` et sur l’ordre de traitement des middlewares, consultez <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Configurer des services sans démarrage

Pour configurer les services et le pipeline de traitement de requête sans utiliser de classe `Startup`, appelez les méthodes pratiques `ConfigureServices` et `Configure` sur le générateur de l’hôte. Les appels multiples à `ConfigureServices` s’ajoutent les uns aux autres. S’il existe plusieurs appels de méthode `Configure`, le dernier appel de `Configure` est utilisé.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a>Étendre le démarrage avec les filtres de démarrage

Utilisez <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> :

* Pour configurer l’intergiciel (middleware) au début ou à la fin du pipeline de [configuration](#the-configure-method) d’une application sans appel explicite à `Use{Middleware}` . `IStartupFilter` est utilisé par ASP.NET Core pour ajouter des valeurs par défaut au début du pipeline sans avoir à faire en sorte que l’auteur de l’application enregistre explicitement l’intergiciel par défaut. `IStartupFilter` autorise un autre appel de composant `Use{Middleware}` pour le compte de l’auteur de l’application.
* Pour créer un pipeline de `Configure` méthodes. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) peut définir un middleware à exécuter avant ou après les middlewares qui sont ajoutés par les bibliothèques.

`IStartupFilter` implémente <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, qui reçoit et retourne un `Action<IApplicationBuilder>`. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> définit une classe pour configurer le pipeline de requête d’une application. Pour plus d’informations, consultez [Créer un pipeline de middlewares avec IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Chaque `IStartupFilter` peut ajouter un ou plusieurs middlewares au pipeline de requête. Les filtres sont appelés dans l’ordre dans lequel ils ont été ajoutés au conteneur de service. Les filtres peuvent ajouter l’intergiciel avant ou après la transmission du contrôle au filtre suivant. Par conséquent, ils s’ajoutent au début ou à la fin du pipeline de l’application.

L’exemple suivant montre comment inscrire un middleware auprès de `IStartupFilter`. Le middleware `RequestSetOptionsMiddleware` définit une valeur d’options à partir d’un paramètre de chaîne de requête :

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

`RequestSetOptionsMiddleware` est configuré dans la classe `RequestSetOptionsStartupFilter` :

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter` est inscrit dans le conteneur de service de <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

Quand un paramètre de chaîne de requête pour `option` est fourni, le middleware traite l’affectation de valeur avant que le middleware ASP.NET Core n’affiche la réponse.

L’ordre d’exécution de l’intergiciel est défini par l’ordre des inscriptions d’`IStartupFilter` :

* Plusieurs implémentations d’`IStartupFilter` peuvent interagir avec les mêmes objets. Si l’ordre est important, définissez l’ordre des inscriptions de leurs services `IStartupFilter` pour qu’il corresponde à l’ordre dans lequel leurs intergiciels doivent s’exécuter.
* Les bibliothèques peuvent ajouter des intergiciels avec une ou plusieurs implémentations `IStartupFilter` qui s’exécutent avant ou après l’autre intergiciel d’application inscrit avec `IStartupFilter`. Pour appeler un middleware `IStartupFilter` avant un middleware ajouté par le `IStartupFilter` d’une bibliothèque :

  * Placez l’inscription du service avant l’ajout de la bibliothèque au conteneur de service.
  * Pour les appels suivants, placez l’inscription du service après l’ajout de la bibliothèque.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Ajouter la configuration au démarrage à partir d’un assembly externe

Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application. Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Ressources supplémentaires

* [L’hôte](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a>Classe Startup

Les applications ASP.NET Core utilisent une classe `Startup`, appelée `Startup` par convention. La classe `Startup` :

* Inclut éventuellement une méthode <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> pour configurer les *services* de l’application. Un service est un composant réutilisable qui fournit une fonctionnalité d’application. Les services sont *enregistrés* dans `ConfigureServices` et utilisés dans l’ensemble de l’application via l' [injection de dépendances (di)](xref:fundamentals/dependency-injection) ou <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .
* Inclut une méthode <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> pour créer le pipeline de traitement des requêtes de l’application.

`ConfigureServices` et `Configure` sont appelés par le runtime ASP.NET Core au lancement de l’application :

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

La classe `Startup` est spécifiée quand l’[hôte](xref:fundamentals/index#host) de l’application est créé. La `Startup` classe est généralement spécifiée en appelant la méthode [WebHostBuilderExtensions. \<TStartup> UseStartup](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) sur le générateur d’ordinateur hôte :

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

L’hôte fournit des services accessibles au constructeur de classe `Startup`. L’application ajoute des services supplémentaires à l’aide de `ConfigureServices`. Les services de l’hôte ainsi que ceux de l’application sont alors disponibles dans `Configure` et dans l’ensemble de l’application.

Une utilisation courante de [l’injection de dépendances](xref:fundamentals/dependency-injection) dans la classe `Startup` consiste à injecter :

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> pour configurer les services en fonction de l’environnement.
* <xref:Microsoft.Extensions.Configuration.IConfiguration> pour lire la configuration.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory> pour créer un enregistreur d’événements dans `Startup.ConfigureServices`.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

La plupart des services ne sont pas disponibles tant que la méthode `Configure` n’est pas appelée.

### <a name="multiple-startup"></a>Démarrage multiple

Quand l’application définit différentes classes `Startup` pour différents environnements (par exemple, `StartupDevelopment`), la classe `Startup` appropriée est sélectionnée au moment de l’exécution. La classe dont le suffixe du nom correspond à l'environnement actuel est prioritaire. Si l’application est exécutée dans l’environnement de développement et qu’elle inclut à la fois une classe `Startup` et une classe `StartupDevelopment`, la classe `StartupDevelopment` est utilisée. Pour plus d’informations, consultez [Utiliser plusieurs environnements](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Pour plus d’informations sur l’hôte, consultez [L’hôte](xref:fundamentals/index#host). Pour plus d’informations sur la gestion des erreurs lors du démarrage, consultez [Gestion des exceptions de démarrage](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Méthode ConfigureServices

La méthode <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> est :

* Optionnel.
* Appelée par l’hôte avant la méthode `Configure` pour configurer les services de l’application
* L’emplacement où les [options de configuration](xref:fundamentals/configuration/index) sont définies par convention.

L’hôte peut configurer certains services avant l’appel des méthodes `Startup`. Pour plus d’informations, consultez [L’hôte](xref:fundamentals/index#host).

Pour les fonctionnalités qui nécessitent une configuration importante, il existe des méthodes d’extension `Add{Service}` pour <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. Par exemple, **Add** DbContext, **Add** default Identity , **Add** EntityFrameworkStores et **Add** Razor pages :

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

L’ajout de services au conteneur de service les rend disponibles dans l’application et dans la méthode `Configure`. Les services sont résolus via l’[injection de dépendances](xref:fundamentals/dependency-injection) ou à partir de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

Pour plus d’informations sur `SetCompatibilityVersion`, voir [SetCompatibilityVersion](xref:mvc/compatibility-version).

## <a name="the-configure-method"></a>Méthode Configure

La méthode <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> permet de spécifier le mode de réponse de l’application aux requêtes HTTP. Vous configurez le pipeline de requête en ajoutant des composants d’[intergiciel (middleware)](xref:fundamentals/middleware/index) à une instance de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` est disponible pour la méthode `Configure`, mais elle n’est pas inscrite dans le conteneur de service. L’hébergement crée un `IApplicationBuilder` et le passe directement à `Configure`.

Les [modèles ASP.NET Core](/dotnet/core/tools/dotnet-new) configurent le pipeline pour permettre la prise en charge des éléments suivants :

* [Page exception du développeur](xref:fundamentals/error-handling#developer-exception-page)
* [Gestionnaire d’exceptions](xref:fundamentals/error-handling#exception-handler-page)
* [HSTS (HTTP Strict Transport Security)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Redirection HTTPS](xref:security/enforcing-ssl)
* [Fichiers statiques](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) et [ Razor pages](xref:razor-pages/index)
* [Règlement Général sur la Protection des Données (RGPD)](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

Chaque méthode d’extension `Use` ajoute un ou plusieurs composants de middleware au pipeline de requête. Par exemple, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configure le [middleware](xref:fundamentals/middleware/index) pour qu’il traite des [fichiers statiques](xref:fundamentals/static-files).

Chaque composant de middleware du pipeline des requêtes est chargé d’appeler le composant suivant du pipeline ou de court-circuiter la chaîne, si c’est approprié.

Vous pouvez spécifier des services supplémentaires (comme `IHostingEnvironment`, `ILoggerFactory` et tout service défini dans `ConfigureServices`) dans la signature de la méthode `Configure`. Ces services sont injectés s’ils sont disponibles.

Pour plus d’informations sur l’utilisation de `IApplicationBuilder` et sur l’ordre de traitement des middlewares, consultez <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Configurer des services sans démarrage

Pour configurer les services et le pipeline de traitement de requête sans utiliser de classe `Startup`, appelez les méthodes pratiques `ConfigureServices` et `Configure` sur le générateur de l’hôte. Les appels multiples à `ConfigureServices` s’ajoutent les uns aux autres. S’il existe plusieurs appels de méthode `Configure`, le dernier appel de `Configure` est utilisé.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>Étendre le démarrage avec les filtres de démarrage

Utilisez <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> :

* Pour configurer l’intergiciel (middleware) au début ou à la fin du pipeline de [configuration](#the-configure-method) d’une application sans appel explicite à `Use{Middleware}` . `IStartupFilter` est utilisé par ASP.NET Core pour ajouter des valeurs par défaut au début du pipeline sans avoir à faire en sorte que l’auteur de l’application enregistre explicitement l’intergiciel par défaut. `IStartupFilter` autorise un autre appel de composant `Use{Middleware}` pour le compte de l’auteur de l’application.
* Pour créer un pipeline de `Configure` méthodes. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) peut définir un middleware à exécuter avant ou après les middlewares qui sont ajoutés par les bibliothèques.

`IStartupFilter` implémente <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, qui reçoit et retourne un `Action<IApplicationBuilder>`. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> définit une classe pour configurer le pipeline de requête d’une application. Pour plus d’informations, consultez [Créer un pipeline de middlewares avec IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Chaque `IStartupFilter` peut ajouter un ou plusieurs middlewares au pipeline de requête. Les filtres sont appelés dans l’ordre dans lequel ils ont été ajoutés au conteneur de service. Les filtres peuvent ajouter l’intergiciel avant ou après la transmission du contrôle au filtre suivant. Par conséquent, ils s’ajoutent au début ou à la fin du pipeline de l’application.

L’exemple suivant montre comment inscrire un middleware auprès de `IStartupFilter`. Le middleware `RequestSetOptionsMiddleware` définit une valeur d’options à partir d’un paramètre de chaîne de requête :

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

`RequestSetOptionsMiddleware` est configuré dans la classe `RequestSetOptionsStartupFilter` :

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter` est inscrit dans le conteneur de service de <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

Quand un paramètre de chaîne de requête pour `option` est fourni, le middleware traite l’affectation de valeur avant que le middleware ASP.NET Core n’affiche la réponse.

L’ordre d’exécution de l’intergiciel est défini par l’ordre des inscriptions d’`IStartupFilter` :

* Plusieurs implémentations d’`IStartupFilter` peuvent interagir avec les mêmes objets. Si l’ordre est important, définissez l’ordre des inscriptions de leurs services `IStartupFilter` pour qu’il corresponde à l’ordre dans lequel leurs intergiciels doivent s’exécuter.
* Les bibliothèques peuvent ajouter des intergiciels avec une ou plusieurs implémentations `IStartupFilter` qui s’exécutent avant ou après l’autre intergiciel d’application inscrit avec `IStartupFilter`. Pour appeler un middleware `IStartupFilter` avant un middleware ajouté par le `IStartupFilter` d’une bibliothèque :

  * Placez l’inscription du service avant l’ajout de la bibliothèque au conteneur de service.
  * Pour les appels suivants, placez l’inscription du service après l’ajout de la bibliothèque.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Ajouter la configuration au démarrage à partir d’un assembly externe

Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application. Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Ressources supplémentaires

* [L’hôte](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end