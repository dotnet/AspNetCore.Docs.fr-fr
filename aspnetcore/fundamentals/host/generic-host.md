---
title: Hôte générique .NET dans ASP.NET Core
author: rick-anderson
description: Utilisez l’hôte générique .NET Core dans les applications ASP.NET Core.  L’hôte générique est responsable du démarrage de l’application et de la gestion de la durée de vie.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: 3e44932c302713132a37534b97fffdd91acce2c7
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234554"
---
# <a name="net-generic-host-in-aspnet-core"></a>Hôte générique .NET dans ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

Les modèles ASP.NET Core créent un hôte générique .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).

Cette rubrique fournit des informations sur l’utilisation de l’hôte générique .NET dans ASP.NET Core. Pour plus d’informations sur l’utilisation de l’hôte générique .NET dans les applications de console, consultez [hôte générique .net](/dotnet/core/extensions/generic-host).

## <a name="host-definition"></a>Définition de l’hôte

Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :

* Injection de dépendances (DI)
* Journalisation
* Configuration
* Implémentations de `IHostedService`

Lorsqu’un hôte démarre, il appelle <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> inscrite dans la collection de services hébergés du conteneur de services. Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).

La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.

## <a name="set-up-a-host"></a>Configurer un hôte

L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`. La méthode `Main` :

* Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.
* Appelle les méthodes `Build` et `Run` sur l’objet builder.

Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte :

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

Le code suivant crée une charge de travail non-HTTP avec une `IHostedService` implémentation ajoutée au conteneur di.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`. Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application. Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).

## <a name="default-builder-settings"></a>Paramètres du générateur par défaut

La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :

* Définit la [racine du contenu](xref:fundamentals/index#content-root) sur le chemin d’accès retourné par <xref:System.IO.Directory.GetCurrentDirectory*> .
* Charge la configuration de l’hôte à partir de :
  * Les variables d’environnement précédées de `DOTNET_` .
  * Arguments de ligne de commande
* Charge la configuration de l’application à partir de :
  * *appsettings.json* .
  * *appsettings.{Environment}.json*
  * [Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.
  * Variables d'environnement.
  * Arguments de ligne de commande
* Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :
  * Console
  * Débogage
  * EventSource
  * EventLog (uniquement en cas d’exécution sur Windows)
* Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.

La méthode <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> :

* Charge la configuration de l’hôte à partir de variables d’environnement précédées de `ASPNETCORE_` .
* Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application. Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.
* Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).
* Ajoute l' [intergiciel d’en-têtes transférés](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est égal à `true` .
* Permet l’intégration d’IIS. Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.

Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.

## <a name="framework-provided-services"></a>Services fournis par le framework

Les services suivants sont inscrits automatiquement :

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Pour plus d’informations sur les services fournis par le Framework, consultez <xref:fundamentals/dependency-injection#framework-provided-services> .

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié. Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application. L’interface inclut également une méthode `StopApplication`.

L’exemple suivant est une `IHostedService` implémentation qui inscrit des `IHostApplicationLifetime` événements :

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête. La dernière implémentation inscrite est utilisée.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut. `ConsoleLifetime`:

* Écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.
* Débloque les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostEnvironment

Injectez le <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service dans une classe pour obtenir des informations sur les paramètres suivants :

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentroot)

Les applications Web implémentent l' `IWebHostEnvironment` interface, qui hérite `IHostEnvironment` et ajoute [WebRootPath](#webroot).

## <a name="host-configuration"></a>Configuration de l’hôte

La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.

La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>. Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.

Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> sur `IHostBuilder`. `ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs. L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.

Le fournisseur de variables d’environnement avec des arguments de préfixe `DOTNET_` et de ligne de commande est inclus par `CreateDefaultBuilder` . Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté. Ce préfixe est supprimé à la lecture des variables d’environnement. Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.

L’exemple suivant crée la configuration d’hôte :

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>la configuration d’une application ;

La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`. `ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs. L’application utilise l’option qui définit une valeur en dernier sur une clé donnée. 

La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances. La configuration d’hôte est également ajoutée à la configuration d’application.

Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Paramètres pour tous les types d’applications

Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP. Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`. Pour plus d’informations, consultez la section [paramètres par défaut du générateur](#default-builder-settings) .

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.

**Clé**  : `applicationName`  
**Type**  : `string`  
**Valeur par défaut** : nom de l’assembly qui contient le point d’entrée de l’application.  
**Variable d’environnement** : `<PREFIX_>APPLICATIONNAME`

Pour définir cette valeur, utilisez la variable d’environnement. 

### <a name="contentroot"></a>ContentRoot

La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu. Si le chemin est introuvable, l’hôte ne peut pas démarrer.

**Clé**  : `contentRoot`  
**Type**  : `string`  
**Par défaut** : le dossier dans lequel se trouve l’assembly de l’application.  
**Variable d’environnement** : `<PREFIX_>CONTENTROOT`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Pour plus d'informations, consultez les pages suivantes :

* [Notions de base : racine du contenu](xref:fundamentals/index#content-root)
* [WebRoot](#webroot)

### <a name="environmentname"></a>EnvironmentName

La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur. Les valeurs définies par le framework sont `Development`, `Staging` et `Production`. Les valeurs ne respectent pas la casse.

**Clé**  : `environment`  
**Type**  : `string`  
**Par défaut** : `Production`  
**Variable d’environnement** : `<PREFIX_>ENVIRONMENT`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. La valeur par défaut est de cinq secondes.  Pendant la période du délai d’expiration, l’hôte :

* Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).
* Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.

Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête. Les services s’arrêtent même s’ils n’ont pas terminé les traitements. Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.

**Clé**  : `shutdownTimeoutSeconds`  
**Type**  : `int`  
**Valeur par défaut** : 5 secondes  
**Variable d’environnement** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`. L’exemple suivant définit un délai d’expiration de 20 secondes :

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a>Désactiver le rechargement de la configuration d’application lors de la modification

Par [défaut](xref:fundamentals/configuration/index#default), *appsettings.json* et *appSettings. { Environment}. JSON* est rechargé lorsque le fichier change. Pour désactiver ce comportement de rechargement dans ASP.NET Core 5,0 ou version ultérieure, affectez la valeur `hostBuilder:reloadConfigOnChange` à la clé `false` .

**Clé**  : `hostBuilder:reloadConfigOnChange`  
**Type** : `bool` ( `true` ou `1` )  
**Par défaut** : `true`  
**Argument de ligne de commande** : `hostBuilder:reloadConfigOnChange`  
**Variable d’environnement** : `<PREFIX_>hostBuilder:reloadConfigOnChange`

> [!WARNING]
> Le séparateur deux `:` -points () ne fonctionne pas avec les clés hiérarchiques de variable d’environnement sur toutes les plateformes. Pour en savoir plus, voir [Variables d’environnement](xref:fundamentals/configuration/index#environment-variables).

## <a name="settings-for-web-apps"></a>Paramètres pour les applications web

Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP. Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.

Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres. Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>CaptureStartupErrors

Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte. Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.

**Clé**  : `captureStartupErrors`  
**Type** : `bool` ( `true` ou `1` )  
**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.  
**Variable d’environnement** : `<PREFIX_>CAPTURESTARTUPERRORS`

Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.

**Clé**  : `detailedErrors`  
**Type** : `bool` ( `true` ou `1` )  
**Par défaut** : `false`  
**Variable d’environnement** : `<PREFIX_>_DETAILEDERRORS`

Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage. La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application. Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.

**Clé**  : `hostingStartupAssemblies`  
**Type**  : `string`  
**Valeur par défaut** : chaîne vide  
**Variable d’environnement** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.

**Clé**  : `hostingStartupExcludeAssemblies`  
**Type**  : `string`  
**Valeur par défaut** : chaîne vide  
**Variable d’environnement** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

Port de redirection HTTPS. Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).

**Clé**  : `https_port`  
**Type**  : `string`  
Valeur **par défaut** : aucune valeur par défaut n’est définie.  
**Variable d’environnement** : `<PREFIX_>HTTPS_PORT`

Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

Indique si l’hôte doit écouter les URL configurées avec le `IWebHostBuilder` au lieu des URL configurées avec l' `IServer` implémentation.

**Clé**  : `preferHostingUrls`  
**Type** : `bool` ( `true` ou `1` )  
**Par défaut** : `true`  
**Variable d’environnement** : `<PREFIX_>_PREFERHOSTINGURLS`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application. Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.

**Clé**  : `preventHostingStartup`  
**Type** : `bool` ( `true` ou `1` )  
**Par défaut** : `false`  
**Variable d’environnement** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Assembly où rechercher la classe `Startup`.

**Clé**  : `startupAssembly`  
**Type**  : `string`  
**Valeur par défaut** : l’assembly de l’application  
**Variable d’environnement** : `<PREFIX_>STARTUPASSEMBLY`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`. `UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`). Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>URLs

Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes. Par exemple : `http://localhost:123`. Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`). Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL. Les formats pris en charge varient selon les serveurs.

**Clé**  : `urls`  
**Type**  : `string`  
**Par défaut** : `http://localhost:5000` et `https://localhost:5001`  
**Variable d’environnement** : `<PREFIX_>URLS`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel a sa propre API de configuration de points de terminaison. Pour plus d'informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>WebRoot

La propriété [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin d’accès relatif aux ressources statiques de l’application. Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.  

**Clé**  : `webroot`  
**Type**  : `string`  
**Valeur par défaut** : la valeur par défaut est `wwwroot` . Le chemin d’accès à *{root content}/wwwroot* doit exister.  
**Variable d’environnement** : `<PREFIX_>WEBROOT`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :

```csharp
webBuilder.UseWebRoot("public");
```

Pour plus d'informations, consultez les pages suivantes :

* [Notions de base : racine Web](xref:fundamentals/index#web-root)
* [ContentRoot](#contentroot)

## <a name="manage-the-host-lifetime"></a>Gérer la durée de vie de l’hôte

Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application. Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.

### <a name="run"></a>Exécuter

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.

### <a name="start"></a>Démarrer

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer. Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le thread appelant jusqu’à ce que l’arrêt soit déclenché par IHostLifetime, par exemple via <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

### <a name="external-control"></a>Contrôle externe

Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Les modèles ASP.NET Core créent un hôte générique .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).

Cette rubrique fournit des informations sur l’utilisation de l’hôte générique .NET dans ASP.NET Core. Pour plus d’informations sur l’utilisation de l’hôte générique .NET dans les applications de console, consultez [hôte générique .net](/dotnet/core/extensions/generic-host).

## <a name="host-definition"></a>Définition de l’hôte

Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :

* Injection de dépendances (DI)
* Journalisation
* Configuration
* Implémentations de `IHostedService`

Lorsqu’un hôte démarre, il appelle <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> inscrite dans la collection de services hébergés du conteneur de services. Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).

La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.

## <a name="set-up-a-host"></a>Configurer un hôte

L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`. La méthode `Main` :

* Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.
* Appelle les méthodes `Build` et `Run` sur l’objet builder.

Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte générique :

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

Le code suivant crée un hôte générique à l’aide d’une charge de travail non-HTTP. L' `IHostedService` implémentation est ajoutée au conteneur di :

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Le code précédent est généré par les modèles ASP.NET Core.

Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`. Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application. Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).

## <a name="default-builder-settings"></a>Paramètres du générateur par défaut

La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> :

* Définit la [racine du contenu](xref:fundamentals/index#content-root) sur le chemin d’accès retourné par <xref:System.IO.Directory.GetCurrentDirectory%2A> .
* Charge la configuration de l’hôte à partir de :
  * Les variables d’environnement précédées de `DOTNET_` .
  * Arguments de ligne de commande
* Charge la configuration de l’application à partir de :
  * *appsettings.json* .
  * *appsettings.{Environment}.json*
  * [Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.
  * Variables d'environnement.
  * Arguments de ligne de commande
* Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :
  * Console
  * Débogage
  * EventSource
  * EventLog (uniquement en cas d’exécution sur Windows)
* Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.

La méthode `ConfigureWebHostDefaults` :

* Charge la configuration de l’hôte à partir de variables d’environnement précédées de `ASPNETCORE_` .
* Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application. Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.
* Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).
* Ajoute l' [intergiciel d’en-têtes transférés](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est égal à `true` .
* Permet l’intégration d’IIS. Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.

Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.

## <a name="framework-provided-services"></a>Services fournis par le framework

Les services suivants sont inscrits automatiquement :

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Pour plus d’informations sur les services fournis par le Framework, consultez <xref:fundamentals/dependency-injection#framework-provided-services> .

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié. Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application. L’interface inclut également une méthode `StopApplication`.

L’exemple suivant est une `IHostedService` implémentation qui inscrit des `IHostApplicationLifetime` événements :

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête. La dernière implémentation inscrite est utilisée.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut. `ConsoleLifetime`:

* Écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> pour démarrer le processus d’arrêt.
* Débloque les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostEnvironment

Injectez le <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service dans une classe pour obtenir des informations sur les paramètres suivants :

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentroot)

Les applications Web implémentent l' `IWebHostEnvironment` interface, qui hérite `IHostEnvironment` et ajoute [WebRootPath](#webroot).

## <a name="host-configuration"></a>Configuration de l’hôte

La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.

La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>. Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.

Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> sur `IHostBuilder`. `ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs. L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.

Le fournisseur de variables d’environnement avec des arguments de préfixe `DOTNET_` et de ligne de commande est inclus par `CreateDefaultBuilder` . Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté. Ce préfixe est supprimé à la lecture des variables d’environnement. Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.

L’exemple suivant crée la configuration d’hôte :

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>la configuration d’une application ;

La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`. `ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs. L’application utilise l’option qui définit une valeur en dernier sur une clé donnée. 

La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances. La configuration d’hôte est également ajoutée à la configuration d’application.

Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Paramètres pour tous les types d’applications

Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP. Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.

**Clé**  : `applicationName`  
**Type**  : `string`  
**Valeur par défaut** : nom de l’assembly qui contient le point d’entrée de l’application.  
**Variable d’environnement** : `<PREFIX_>APPLICATIONNAME`

Pour définir cette valeur, utilisez la variable d’environnement. 

### <a name="contentroot"></a>ContentRoot

La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu. Si le chemin est introuvable, l’hôte ne peut pas démarrer.

**Clé**  : `contentRoot`  
**Type**  : `string`  
**Par défaut** : le dossier dans lequel se trouve l’assembly de l’application.  
**Variable d’environnement** : `<PREFIX_>CONTENTROOT`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Pour plus d'informations, consultez les pages suivantes :

* [Notions de base : racine du contenu](xref:fundamentals/index#content-root)
* [WebRoot](#webroot)

### <a name="environmentname"></a>EnvironmentName

La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur. Les valeurs définies par le framework sont `Development`, `Staging` et `Production`. Les valeurs ne respectent pas la casse.

**Clé**  : `environment`  
**Type**  : `string`  
**Par défaut** : `Production`  
**Variable d’environnement** : `<PREFIX_>ENVIRONMENT`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. La valeur par défaut est de cinq secondes.  Pendant la période du délai d’expiration, l’hôte :

* Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).
* Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.

Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête. Les services s’arrêtent même s’ils n’ont pas terminé les traitements. Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.

**Clé**  : `shutdownTimeoutSeconds`  
**Type**  : `int`  
**Valeur par défaut** : 5 secondes  
**Variable d’environnement** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`. L’exemple suivant définit un délai d’expiration de 20 secondes :

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a>Paramètres pour les applications web

Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP. Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.

Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres. Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>CaptureStartupErrors

Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte. Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.

**Clé**  : `captureStartupErrors`  
**Type** : `bool` ( `true` ou `1` )  
**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.  
**Variable d’environnement** : `<PREFIX_>CAPTURESTARTUPERRORS`

Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.

**Clé**  : `detailedErrors`  
**Type** : `bool` ( `true` ou `1` )  
**Par défaut** : `false`  
**Variable d’environnement** : `<PREFIX_>_DETAILEDERRORS`

Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage. La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application. Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.

**Clé**  : `hostingStartupAssemblies`  
**Type**  : `string`  
**Valeur par défaut** : chaîne vide  
**Variable d’environnement** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.

**Clé**  : `hostingStartupExcludeAssemblies`  
**Type**  : `string`  
**Valeur par défaut** : chaîne vide  
**Variable d’environnement** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

Port de redirection HTTPS. Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).

**Clé**  : `https_port`  
**Type**  : `string`  
Valeur **par défaut** : aucune valeur par défaut n’est définie.  
**Variable d’environnement** : `<PREFIX_>HTTPS_PORT`

Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

Indique si l’hôte doit écouter les URL configurées avec le `IWebHostBuilder` au lieu des URL configurées avec l' `IServer` implémentation.

**Clé**  : `preferHostingUrls`  
**Type** : `bool` ( `true` ou `1` )  
**Par défaut** : `true`  
**Variable d’environnement** : `<PREFIX_>_PREFERHOSTINGURLS`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application. Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.

**Clé**  : `preventHostingStartup`  
**Type** : `bool` ( `true` ou `1` )  
**Par défaut** : `false`  
**Variable d’environnement** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Assembly où rechercher la classe `Startup`.

**Clé**  : `startupAssembly`  
**Type**  : `string`  
**Valeur par défaut** : l’assembly de l’application  
**Variable d’environnement** : `<PREFIX_>STARTUPASSEMBLY`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`. `UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`). Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>URLs

Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes. Par exemple : `http://localhost:123`. Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`). Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL. Les formats pris en charge varient selon les serveurs.

**Clé**  : `urls`  
**Type**  : `string`  
**Par défaut** : `http://localhost:5000` et `https://localhost:5001`  
**Variable d’environnement** : `<PREFIX_>URLS`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel a sa propre API de configuration de points de terminaison. Pour plus d'informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>WebRoot

La propriété [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin d’accès relatif aux ressources statiques de l’application. Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.  

**Clé**  : `webroot`  
**Type**  : `string`  
**Valeur par défaut** : la valeur par défaut est `wwwroot` . Le chemin d’accès à *{root content}/wwwroot* doit exister.  
**Variable d’environnement** : `<PREFIX_>WEBROOT`

Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :

```csharp
webBuilder.UseWebRoot("public");
```

Pour plus d'informations, consultez les pages suivantes :

* [Notions de base : racine Web](xref:fundamentals/index#web-root)
* [ContentRoot](#contentroot)

## <a name="manage-the-host-lifetime"></a>Gérer la durée de vie de l’hôte

Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application. Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.

### <a name="run"></a>Exécuter

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.

### <a name="start"></a>Démarrer

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer. Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le thread appelant jusqu’à ce que l’arrêt soit déclenché par IHostLifetime, par exemple via <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

### <a name="external-control"></a>Contrôle externe

Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Les applications ASP.NET Core configurent et lancent un hôte. L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.

Cet article traite de l’hôte générique ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), qui est utilisé pour les applications qui ne traitent pas les requêtes HTTP.

L’objectif de l’hôte générique est de séparer le pipeline HTTP de l’API Hôte web pour permettre un plus large éventail de scénarios d’hôte. La messagerie, les tâches en arrière-plan et autres charges de travail non HTTP basées sur l’hôte générique bénéficient de fonctionnalités transversales, comme la configuration, l’injection de dépendances (DI) et la journalisation.

L’hôte générique est nouveau dans ASP.NET Core 2.1 et n’est pas adapté aux scénarios d’hébergement web. Pour les scénarios d’hébergement de web, utilisez l’[hôte web](xref:fundamentals/host/web-host). L’hôte générique est appelé à remplacer l’hôte web dans une version ultérieure et à servir d’API hôte principale dans les scénarios HTTP et non-HTTP.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Quand vous exécutez l’exemple d’application dans [Visual Studio Code](https://code.visualstudio.com/), utilisez un *terminal externe ou intégré* . N’exécutez pas l’exemple dans une `internalConsole`.

Pour définir la console dans Visual Studio Code :

1. Ouvrez le fichier *.vscode/launch.json* .
1. Dans la configuration **.NET Core Launch (console)** , recherchez l’entrée **console** . Définissez la valeur avec `externalTerminal` ou `integratedTerminal`.

## <a name="introduction"></a>Introduction

La bibliothèque de l’hôte générique est disponible dans l’espace de noms <xref:Microsoft.Extensions.Hosting> et est fournie par le package [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/). Le package `Microsoft.Extensions.Hosting` est inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou ultérieur).

<xref:Microsoft.Extensions.Hosting.IHostedService> est le point d’entrée pour exécuter le code. Chaque implémentation <xref:Microsoft.Extensions.Hosting.IHostedService> est exécutée dans l’ordre d’[inscription des services dans ConfigureServices](#configureservices). <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> est appelé sur chaque <xref:Microsoft.Extensions.Hosting.IHostedService> au démarrage de l’hôte, tandis que <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> est appelé dans l’ordre d’inscription inverse quand l’hôte s’arrête normalement.

## <a name="set-up-a-host"></a>Configurer un hôte

<xref:Microsoft.Extensions.Hosting.IHostBuilder> est le principal composant que les applications et les bibliothèques utilisent pour initialiser, générer et exécuter l’hôte :

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>Options

<xref:Microsoft.Extensions.Hosting.HostOptions> configure les options pour <xref:Microsoft.Extensions.Hosting.IHost>.

### <a name="shutdown-timeout"></a>Délai d’expiration

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. La valeur par défaut est de cinq secondes.

La configuration d’option suivante dans `Program.Main` augmente le délai d’attente d’arrêt de cinq secondes par défaut à 20 secondes :

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a>Services par défaut

Les services suivants sont inscrits au moment de l’initialisation de l’hôte :

* [Environnement](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* [Configuration](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )
* <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHost>
* [Options](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )
* [Journalisation](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )

## <a name="host-configuration"></a>Configuration de l’hôte

La création de la configuration d’hôte fait appel aux opérations suivantes :

* Appel de méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder> pour définir la [racine du contenu](#content-root) et [l’environnement](#environment).
* Lecture de la configuration à partir des fournisseurs de configuration dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.

### <a name="extension-methods"></a>Méthodes d’extension

### <a name="application-key-name"></a>Clé d’application (nom)

La propriété [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) est définie à partir de la configuration de l’hôte pendant la construction de l’hôte. Pour définir la valeur explicitement, utilisez [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey) :

**Clé**  : `applicationName`  
**Type**  : `string`  
**Par défaut**  : nom de l’assembly contenant le point d’entrée de l’application.  
**Définir à l’aide** de : `HostBuilderContext.HostingEnvironment.ApplicationName`  
**Variable d’environnement** : `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))

### <a name="content-root"></a>Racine de contenu

Ce paramètre détermine où l’hôte commence la recherche des fichiers de contenu.

**Clé**  : `contentRoot`  
**Type**  : `string`  
**Valeur par défaut** : dossier où réside l’assembly de l’application.  
**Définir à l’aide** de : `UseContentRoot`  
**Variable d’environnement** : `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))

Si le chemin est introuvable, l’hôte ne peut pas démarrer.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

Pour plus d’informations, consultez [principes de base : racine du contenu](xref:fundamentals/index#content-root).

### <a name="environment"></a>Environnement

Définit l' [environnement](xref:fundamentals/environments)de l’application.

**Clé**  : `environment`  
**Type**  : `string`  
**Par défaut** : `Production`  
**Définir à l’aide** de : `UseEnvironment`  
**Variable d’environnement** : `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))

L’environnement peut être défini à n’importe quelle valeur. Les valeurs définies par le framework sont `Development`, `Staging` et `Production`. Les valeurs ne respectent pas la casse.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>ConfigureHostConfiguration

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’hôte. La configuration d’hôte permet d’initialiser le <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> en vue de son utilisation dans le processus de génération de l’application.

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> peut être appelé plusieurs fois avec des résultats additifs. L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.

Aucun fournisseur n’est inclus par défaut. Vous devez spécifier explicitement les fournisseurs de configuration dont l’application a besoin dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, notamment :

* Configuration du fichier (par exemple, à partir d’un fichier *hostsettings.json* ).
* Configuration de la variable d’environnement.
* Configuration de l’argument de ligne de commande.
* Tout autre fournisseur de configuration obligatoire.

Pour activer la configuration de fichier de l’hôte, spécifiez le chemin de base de l’application avec `SetBasePath`, suivi d’un appel à l’un des [fournisseurs de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider). L’exemple d’application utilise un fichier JSON, *hostsettings.json* , et appelle <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> pour utiliser les paramètres de configuration d’hôte du fichier.

Pour ajouter la [configuration de variable d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider) de l’hôte, appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur le générateur d’hôte. `AddEnvironmentVariables` accepte un préfixe facultatif défini par l’utilisateur. L’exemple d’application utilise un préfixe `PREFIX_`. Ce préfixe est supprimé à la lecture des variables d’environnement. Lorsque l’hôte de l’exemple d’application est configuré, la valeur de variable d’environnement de `PREFIX_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.

Pendant le développement, lorsque vous utilisez [Visual Studio](https://visualstudio.microsoft.com) ou que vous lancez une application avec `dotnet run`, vous pouvez définir les variables d’environnement dans le fichier *Properties/launchSettings.json* . Dans [Visual Studio Code](https://code.visualstudio.com/), elles peuvent être définies dans le fichier *.vscode/launch.json* au cours du développement. Pour plus d'informations, consultez <xref:fundamentals/environments>.

Pour ajouter la [configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider), appelez <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>. La configuration de ligne de commande est ajoutée en dernier pour permettre aux arguments de ligne de commande de substituer la configuration fournie par les fournisseurs de configuration antérieurs.

*hostsettings.json*  :

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

Une configuration supplémentaire peut être fournie à l’aide des clés [applicationName](#application-key-name) et [contentRoot](#content-root).

Exemple de configuration `HostBuilder` avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Pour créer la configuration d’application, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur l’implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder>. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’application. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> peut être appelé plusieurs fois avec des résultats additifs. L’application utilise l’option qui définit une valeur en dernier sur une clé donnée. La configuration créée par <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et dans <xref:Microsoft.Extensions.Hosting.IHost.Services*>.

La configuration d’application reçoit automatiquement la configuration d’hôte fournie par [ConfigureHostConfiguration](#configurehostconfiguration).

Exemple de configuration d’application avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appsettings.json* :

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*appsettings.Development.json*  :

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*appsettings.Production.json*  :

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

Pour déplacer des fichiers de paramètres vers le répertoire de sortie, spécifiez-les en tant qu’[éléments de projet MSBuild](/visualstudio/msbuild/common-msbuild-project-items) dans le fichier projet. L’exemple d’application déplace ses fichiers de paramètres d’application JSON et *hostsettings.json* avec l’élément `<Content>` suivant :

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> Les méthodes d’extension de configuration, telles que <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> et <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, nécessitent des packages NuGet supplémentaires, tels que [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) et [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables). Si l’application n’utilise pas le [métapaquet Microsoft.AspNetCore.App ](xref:fundamentals/metapackage-app), ces packages doivent être ajoutés au projet en plus du noyau [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration). Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.

## <a name="configureservices"></a>ConfigureServices

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> ajoute des services au conteneur [d’injection de dépendances](xref:fundamentals/dependency-injection) de l’application. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> peut être appelé plusieurs fois avec des résultats additifs.

Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>. Pour plus d'informations, consultez <xref:fundamentals/host/hosted-services>.

L’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise la méthode d’extension `AddHostedService` afin d’ajouter un service pour les événements de durée de vie, `LifetimeEventsHostedService`, et une tâche en arrière-plan chronométrée, `TimedHostedService`, à l’application :

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>ConfigureLogging

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> ajoute un délégué pour configurer le <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fourni. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> peut être appelé plusieurs fois avec des résultats additifs.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleLifetime

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> déverrouille les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync). `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est préinscrit comme implémentation de durée de vie par défaut. La dernière durée de vie inscrite est utilisée.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>Configuration du conteneur

Pour prendre en charge le branchement dans d’autres conteneurs, l’hôte peut accepter un <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>. L’ajout d’une fabrique ne fait pas partie de l’inscription de conteneur DI mais est plutôt une tâche un intrinsèque à l’hôte utilisée pour créer le conteneur DI concret. [UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) remplace la fabrique par défaut utilisée pour créer le fournisseur de services de l’application.

La configuration de conteneur personnalisée est gérée par la méthode <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre une expérience fortement typée pour configurer le conteneur sur l’API hôte sous-jacente. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> peut être appelé plusieurs fois avec des résultats additifs.

Créer un conteneur de service pour l’application :

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

Fournir une fabrique de conteneur de service :

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

Utiliser la fabrique et configurer le conteneur de service personnalisé pour l’application :

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>Extensibilité

L’extensibilité de l’hôte est effectuée avec les méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder>. L’exemple suivant montre comment une méthode d’extension étend une implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder> avec l’exemple [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) démontré dans <xref:fundamentals/host/hosted-services>.

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

Une application établit la méthode d'extension `UseHostedService` pour inscrire le service hébergé passé dans `T` :

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a>Gérer l’hôte

L’implémentation <xref:Microsoft.Extensions.Hosting.IHost> est chargée de démarrer et d’arrêter les implémentations <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.

### <a name="run"></a>Exécuter

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte :

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand le jeton d’annulation ou l’arrêt est déclenché :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a>Start et StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a>StartAsync et StopAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’application.

<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arrête l’application.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>est déclenché via le <xref:Microsoft.Extensions.Hosting.IHostLifetime> , par exemple `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (écoute la <kbd>touche Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM). <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a>Contrôle externe

Le contrôle externe de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, lequel attend qu’il soit fini avant de continuer. Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.

## <a name="ihostingenvironment-interface"></a>Interface IHostingEnvironment

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fournit des informations sur l’environnement d’hébergement de l’application. Utilisez [l’injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir l’interface <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> afin d’utiliser ses propriétés et méthodes d’extension :

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

Pour plus d'informations, consultez <xref:fundamentals/environments>.

## <a name="iapplicationlifetime-interface"></a>Interface IApplicationLifetime

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime> s’utilise pour les activités de post-démarrage et d’arrêt, notamment pour les demandes d’arrêt normal. Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes <xref:System.Action> qui définissent les événements de démarrage et d’arrêt.

| Jeton d’annulation | Événement déclencheur |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | L’hôte a complètement démarré. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | L’hôte effectue un arrêt approprié. Toutes les requêtes doivent être traitées. L’arrêt est bloqué tant que cet événement n’est pas terminé. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | L’hôte effectue un arrêt approprié. Des requêtes peuvent encore être en cours de traitement. L’arrêt est bloqué tant que cet événement n’est pas terminé. |

Le constructeur injecte le service <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> dans une classe. [L’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise l’injection de constructeur dans une classe `LifetimeEventsHostedService` (une implémentation <xref:Microsoft.Extensions.Hosting.IHostedService>) pour inscrire les événements.

*LifetimeEventsHostedService.cs* :

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> demande l’arrêt de l’application. La classe suivante utilise <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour arrêter normalement une application lors de l’appel de la méthode `Shutdown` de la classe :

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/host/hosted-services>
