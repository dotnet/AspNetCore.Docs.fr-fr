---
title: Hôte générique .NET dans ASP.NET Core
author: rick-anderson
description: Utilisez l’hôte générique .NET Core dans les applications ASP.NET Core.  L’hôte générique est responsable du démarrage de l’application et de la gestion de la durée de vie.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: 3e44932c302713132a37534b97fffdd91acce2c7
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234554"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="2adda-104">Hôte générique .NET dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2adda-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2adda-105">Les modèles ASP.NET Core créent un hôte générique .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="2adda-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="2adda-106">Cette rubrique fournit des informations sur l’utilisation de l’hôte générique .NET dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2adda-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="2adda-107">Pour plus d’informations sur l’utilisation de l’hôte générique .NET dans les applications de console, consultez [hôte générique .net](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="2adda-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="2adda-108">Définition de l’hôte</span><span class="sxs-lookup"><span data-stu-id="2adda-108">Host definition</span></span>

<span data-ttu-id="2adda-109">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="2adda-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="2adda-110">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="2adda-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="2adda-111">Journalisation</span><span class="sxs-lookup"><span data-stu-id="2adda-111">Logging</span></span>
* <span data-ttu-id="2adda-112">Configuration</span><span class="sxs-lookup"><span data-stu-id="2adda-112">Configuration</span></span>
* <span data-ttu-id="2adda-113">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="2adda-113">`IHostedService` implementations</span></span>

<span data-ttu-id="2adda-114">Lorsqu’un hôte démarre, il appelle <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> inscrite dans la collection de services hébergés du conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="2adda-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="2adda-115">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="2adda-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="2adda-116">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="2adda-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="2adda-117">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="2adda-117">Set up a host</span></span>

<span data-ttu-id="2adda-118">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="2adda-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="2adda-119">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="2adda-119">The `Main` method:</span></span>

* <span data-ttu-id="2adda-120">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="2adda-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="2adda-121">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="2adda-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="2adda-122">Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte :</span><span class="sxs-lookup"><span data-stu-id="2adda-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="2adda-123">Le code suivant crée une charge de travail non-HTTP avec une `IHostedService` implémentation ajoutée au conteneur di.</span><span class="sxs-lookup"><span data-stu-id="2adda-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="2adda-124">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="2adda-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="2adda-125">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2adda-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="2adda-126">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="2adda-127">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="2adda-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="2adda-128">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="2adda-128">Default builder settings</span></span>

<span data-ttu-id="2adda-129">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="2adda-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="2adda-130">Définit la [racine du contenu](xref:fundamentals/index#content-root) sur le chemin d’accès retourné par <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="2adda-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="2adda-131">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="2adda-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="2adda-132">Les variables d’environnement précédées de `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="2adda-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="2adda-133">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2adda-133">Command-line arguments.</span></span>
* <span data-ttu-id="2adda-134">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="2adda-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="2adda-135">*appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="2adda-135">*appsettings.json* .</span></span>
  * <span data-ttu-id="2adda-136">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="2adda-136">*appsettings.{Environment}.json* .</span></span>
  * <span data-ttu-id="2adda-137">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="2adda-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="2adda-138">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="2adda-138">Environment variables.</span></span>
  * <span data-ttu-id="2adda-139">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2adda-139">Command-line arguments.</span></span>
* <span data-ttu-id="2adda-140">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="2adda-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="2adda-141">Console</span><span class="sxs-lookup"><span data-stu-id="2adda-141">Console</span></span>
  * <span data-ttu-id="2adda-142">Débogage</span><span class="sxs-lookup"><span data-stu-id="2adda-142">Debug</span></span>
  * <span data-ttu-id="2adda-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="2adda-143">EventSource</span></span>
  * <span data-ttu-id="2adda-144">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="2adda-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="2adda-145">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="2adda-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="2adda-146">La méthode <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> :</span><span class="sxs-lookup"><span data-stu-id="2adda-146">The <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method:</span></span>

* <span data-ttu-id="2adda-147">Charge la configuration de l’hôte à partir de variables d’environnement précédées de `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="2adda-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="2adda-148">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="2adda-149">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="2adda-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="2adda-150">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2adda-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="2adda-151">Ajoute l' [intergiciel d’en-têtes transférés](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est égal à `true` .</span><span class="sxs-lookup"><span data-stu-id="2adda-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="2adda-152">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="2adda-152">Enables IIS integration.</span></span> <span data-ttu-id="2adda-153">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="2adda-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="2adda-154">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="2adda-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="2adda-155">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="2adda-155">Framework-provided services</span></span>

<span data-ttu-id="2adda-156">Les services suivants sont inscrits automatiquement :</span><span class="sxs-lookup"><span data-stu-id="2adda-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="2adda-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="2adda-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="2adda-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="2adda-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="2adda-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="2adda-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="2adda-160">Pour plus d’informations sur les services fournis par le Framework, consultez <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="2adda-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="2adda-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="2adda-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="2adda-162">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="2adda-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="2adda-163">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="2adda-164">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="2adda-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="2adda-165">L’exemple suivant est une `IHostedService` implémentation qui inscrit des `IHostApplicationLifetime` événements :</span><span class="sxs-lookup"><span data-stu-id="2adda-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="2adda-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="2adda-166">IHostLifetime</span></span>

<span data-ttu-id="2adda-167">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="2adda-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="2adda-168">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="2adda-168">The last implementation registered is used.</span></span>

<span data-ttu-id="2adda-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="2adda-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="2adda-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="2adda-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="2adda-171">Écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="2adda-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="2adda-172">Débloque les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="2adda-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="2adda-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="2adda-173">IHostEnvironment</span></span>

<span data-ttu-id="2adda-174">Injectez le <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="2adda-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="2adda-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="2adda-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="2adda-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="2adda-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="2adda-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="2adda-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="2adda-178">Les applications Web implémentent l' `IWebHostEnvironment` interface, qui hérite `IHostEnvironment` et ajoute [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="2adda-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="2adda-179">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="2adda-179">Host configuration</span></span>

<span data-ttu-id="2adda-180">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="2adda-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="2adda-181">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="2adda-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="2adda-182">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="2adda-183">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2adda-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="2adda-184">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="2adda-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="2adda-185">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="2adda-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="2adda-186">Le fournisseur de variables d’environnement avec des arguments de préfixe `DOTNET_` et de ligne de commande est inclus par `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2adda-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2adda-187">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="2adda-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="2adda-188">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="2adda-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="2adda-189">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="2adda-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="2adda-190">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="2adda-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="2adda-191">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="2adda-191">App configuration</span></span>

<span data-ttu-id="2adda-192">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2adda-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="2adda-193">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="2adda-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="2adda-194">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="2adda-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="2adda-195">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2adda-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="2adda-196">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="2adda-197">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="2adda-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="2adda-198">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="2adda-198">Settings for all app types</span></span>

<span data-ttu-id="2adda-199">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="2adda-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="2adda-200">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="2adda-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="2adda-201">Pour plus d’informations, consultez la section [paramètres par défaut du générateur](#default-builder-settings) .</span><span class="sxs-lookup"><span data-stu-id="2adda-201">For more information, see the [Default builder settings](#default-builder-settings) section.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="2adda-202">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="2adda-202">ApplicationName</span></span>

<span data-ttu-id="2adda-203">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2adda-203">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="2adda-204">**Clé**  : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="2adda-204">**Key** : `applicationName`</span></span>  
<span data-ttu-id="2adda-205">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-205">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-206">**Valeur par défaut** : nom de l’assembly qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-206">**Default** : The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="2adda-207">**Variable d’environnement** : `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="2adda-207">**Environment variable** : `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="2adda-208">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="2adda-208">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="2adda-209">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="2adda-209">ContentRoot</span></span>

<span data-ttu-id="2adda-210">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="2adda-210">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="2adda-211">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="2adda-211">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="2adda-212">**Clé**  : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="2adda-212">**Key** : `contentRoot`</span></span>  
<span data-ttu-id="2adda-213">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-213">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-214">**Par défaut** : le dossier dans lequel se trouve l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-214">**Default** : The folder where the app assembly resides.</span></span>  
<span data-ttu-id="2adda-215">**Variable d’environnement** : `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="2adda-215">**Environment variable** : `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="2adda-216">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2adda-216">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="2adda-217">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="2adda-217">For more information, see:</span></span>

* [<span data-ttu-id="2adda-218">Notions de base : racine du contenu</span><span class="sxs-lookup"><span data-stu-id="2adda-218">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="2adda-219">WebRoot</span><span class="sxs-lookup"><span data-stu-id="2adda-219">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="2adda-220">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="2adda-220">EnvironmentName</span></span>

<span data-ttu-id="2adda-221">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="2adda-221">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="2adda-222">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="2adda-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="2adda-223">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="2adda-223">Values aren't case-sensitive.</span></span>

<span data-ttu-id="2adda-224">**Clé**  : `environment`</span><span class="sxs-lookup"><span data-stu-id="2adda-224">**Key** : `environment`</span></span>  
<span data-ttu-id="2adda-225">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-225">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-226">**Par défaut** : `Production`</span><span class="sxs-lookup"><span data-stu-id="2adda-226">**Default** : `Production`</span></span>  
<span data-ttu-id="2adda-227">**Variable d’environnement** : `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="2adda-227">**Environment variable** : `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="2adda-228">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2adda-228">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="2adda-229">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="2adda-229">ShutdownTimeout</span></span>

<span data-ttu-id="2adda-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2adda-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="2adda-231">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="2adda-231">The default value is five seconds.</span></span>  <span data-ttu-id="2adda-232">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="2adda-232">During the timeout period, the host:</span></span>

* <span data-ttu-id="2adda-233">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="2adda-233">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="2adda-234">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="2adda-234">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="2adda-235">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="2adda-235">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="2adda-236">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="2adda-236">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="2adda-237">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="2adda-237">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="2adda-238">**Clé**  : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="2adda-238">**Key** : `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="2adda-239">**Type**  : `int`</span><span class="sxs-lookup"><span data-stu-id="2adda-239">**Type** : `int`</span></span>  
<span data-ttu-id="2adda-240">**Valeur par défaut** : 5 secondes</span><span class="sxs-lookup"><span data-stu-id="2adda-240">**Default** : 5 seconds</span></span>  
<span data-ttu-id="2adda-241">**Variable d’environnement** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="2adda-241">**Environment variable** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="2adda-242">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="2adda-242">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="2adda-243">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="2adda-243">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="2adda-244">Désactiver le rechargement de la configuration d’application lors de la modification</span><span class="sxs-lookup"><span data-stu-id="2adda-244">Disable app configuration reload on change</span></span>

<span data-ttu-id="2adda-245">Par [défaut](xref:fundamentals/configuration/index#default), *appsettings.json* et *appSettings. { Environment}. JSON* est rechargé lorsque le fichier change.</span><span class="sxs-lookup"><span data-stu-id="2adda-245">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="2adda-246">Pour désactiver ce comportement de rechargement dans ASP.NET Core 5,0 ou version ultérieure, affectez la valeur `hostBuilder:reloadConfigOnChange` à la clé `false` .</span><span class="sxs-lookup"><span data-stu-id="2adda-246">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="2adda-247">**Clé**  : `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="2adda-247">**Key** : `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="2adda-248">**Type** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="2adda-248">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="2adda-249">**Par défaut** : `true`</span><span class="sxs-lookup"><span data-stu-id="2adda-249">**Default** : `true`</span></span>  
<span data-ttu-id="2adda-250">**Argument de ligne de commande** : `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="2adda-250">**Command-line argument** : `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="2adda-251">**Variable d’environnement** : `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="2adda-251">**Environment variable** : `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="2adda-252">Le séparateur deux `:` -points () ne fonctionne pas avec les clés hiérarchiques de variable d’environnement sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="2adda-252">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="2adda-253">Pour en savoir plus, voir [Variables d’environnement](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="2adda-253">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="2adda-254">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="2adda-254">Settings for web apps</span></span>

<span data-ttu-id="2adda-255">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="2adda-255">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="2adda-256">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="2adda-256">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="2adda-257">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="2adda-257">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="2adda-258">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="2adda-258">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="2adda-259">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="2adda-259">CaptureStartupErrors</span></span>

<span data-ttu-id="2adda-260">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2adda-260">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="2adda-261">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="2adda-261">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="2adda-262">**Clé**  : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="2adda-262">**Key** : `captureStartupErrors`</span></span>  
<span data-ttu-id="2adda-263">**Type** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="2adda-263">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="2adda-264">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="2adda-264">**Default** : Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="2adda-265">**Variable d’environnement** : `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="2adda-265">**Environment variable** : `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="2adda-266">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="2adda-266">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="2adda-267">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="2adda-267">DetailedErrors</span></span>

<span data-ttu-id="2adda-268">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="2adda-268">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="2adda-269">**Clé**  : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="2adda-269">**Key** : `detailedErrors`</span></span>  
<span data-ttu-id="2adda-270">**Type** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="2adda-270">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="2adda-271">**Par défaut** : `false`</span><span class="sxs-lookup"><span data-stu-id="2adda-271">**Default** : `false`</span></span>  
<span data-ttu-id="2adda-272">**Variable d’environnement** : `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="2adda-272">**Environment variable** : `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="2adda-273">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="2adda-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="2adda-274">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="2adda-274">HostingStartupAssemblies</span></span>

<span data-ttu-id="2adda-275">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="2adda-275">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="2adda-276">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-276">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="2adda-277">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="2adda-277">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="2adda-278">**Clé**  : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="2adda-278">**Key** : `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="2adda-279">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-279">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-280">**Valeur par défaut** : chaîne vide</span><span class="sxs-lookup"><span data-stu-id="2adda-280">**Default** : Empty string</span></span>  
<span data-ttu-id="2adda-281">**Variable d’environnement** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="2adda-281">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="2adda-282">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="2adda-282">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="2adda-283">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="2adda-283">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="2adda-284">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="2adda-284">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="2adda-285">**Clé**  : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="2adda-285">**Key** : `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="2adda-286">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-286">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-287">**Valeur par défaut** : chaîne vide</span><span class="sxs-lookup"><span data-stu-id="2adda-287">**Default** : Empty string</span></span>  
<span data-ttu-id="2adda-288">**Variable d’environnement** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="2adda-288">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="2adda-289">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="2adda-289">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="2adda-290">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="2adda-290">HTTPS_Port</span></span>

<span data-ttu-id="2adda-291">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2adda-291">The HTTPS redirect port.</span></span> <span data-ttu-id="2adda-292">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="2adda-292">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2adda-293">**Clé**  : `https_port`</span><span class="sxs-lookup"><span data-stu-id="2adda-293">**Key** : `https_port`</span></span>  
<span data-ttu-id="2adda-294">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-294">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-295">Valeur **par défaut** : aucune valeur par défaut n’est définie.</span><span class="sxs-lookup"><span data-stu-id="2adda-295">**Default** : A default value isn't set.</span></span>  
<span data-ttu-id="2adda-296">**Variable d’environnement** : `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="2adda-296">**Environment variable** : `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="2adda-297">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="2adda-297">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="2adda-298">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="2adda-298">PreferHostingUrls</span></span>

<span data-ttu-id="2adda-299">Indique si l’hôte doit écouter les URL configurées avec le `IWebHostBuilder` au lieu des URL configurées avec l' `IServer` implémentation.</span><span class="sxs-lookup"><span data-stu-id="2adda-299">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="2adda-300">**Clé**  : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="2adda-300">**Key** : `preferHostingUrls`</span></span>  
<span data-ttu-id="2adda-301">**Type** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="2adda-301">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="2adda-302">**Par défaut** : `true`</span><span class="sxs-lookup"><span data-stu-id="2adda-302">**Default** : `true`</span></span>  
<span data-ttu-id="2adda-303">**Variable d’environnement** : `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="2adda-303">**Environment variable** : `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="2adda-304">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="2adda-304">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="2adda-305">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="2adda-305">PreventHostingStartup</span></span>

<span data-ttu-id="2adda-306">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-306">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="2adda-307">Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2adda-307">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="2adda-308">**Clé**  : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="2adda-308">**Key** : `preventHostingStartup`</span></span>  
<span data-ttu-id="2adda-309">**Type** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="2adda-309">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="2adda-310">**Par défaut** : `false`</span><span class="sxs-lookup"><span data-stu-id="2adda-310">**Default** : `false`</span></span>  
<span data-ttu-id="2adda-311">**Variable d’environnement** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="2adda-311">**Environment variable** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="2adda-312">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="2adda-312">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="2adda-313">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="2adda-313">StartupAssembly</span></span>

<span data-ttu-id="2adda-314">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="2adda-314">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="2adda-315">**Clé**  : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="2adda-315">**Key** : `startupAssembly`</span></span>  
<span data-ttu-id="2adda-316">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-316">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-317">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="2adda-317">**Default** : The app's assembly</span></span>  
<span data-ttu-id="2adda-318">**Variable d’environnement** : `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="2adda-318">**Environment variable** : `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="2adda-319">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="2adda-319">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="2adda-320">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="2adda-320">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="2adda-321">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="2adda-321">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="2adda-322">URLs</span><span class="sxs-lookup"><span data-stu-id="2adda-322">URLs</span></span>

<span data-ttu-id="2adda-323">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="2adda-323">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="2adda-324">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="2adda-324">For example, `http://localhost:123`.</span></span> <span data-ttu-id="2adda-325">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="2adda-325">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="2adda-326">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="2adda-326">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="2adda-327">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="2adda-327">Supported formats vary among servers.</span></span>

<span data-ttu-id="2adda-328">**Clé**  : `urls`</span><span class="sxs-lookup"><span data-stu-id="2adda-328">**Key** : `urls`</span></span>  
<span data-ttu-id="2adda-329">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-329">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-330">**Par défaut** : `http://localhost:5000` et `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="2adda-330">**Default** : `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="2adda-331">**Variable d’environnement** : `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="2adda-331">**Environment variable** : `<PREFIX_>URLS`</span></span>

<span data-ttu-id="2adda-332">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="2adda-332">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="2adda-333">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="2adda-333">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="2adda-334">Pour plus d'informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2adda-334">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="2adda-335">WebRoot</span><span class="sxs-lookup"><span data-stu-id="2adda-335">WebRoot</span></span>

<span data-ttu-id="2adda-336">La propriété [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin d’accès relatif aux ressources statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-336">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="2adda-337">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="2adda-337">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="2adda-338">**Clé**  : `webroot`</span><span class="sxs-lookup"><span data-stu-id="2adda-338">**Key** : `webroot`</span></span>  
<span data-ttu-id="2adda-339">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-339">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-340">**Valeur par défaut** : la valeur par défaut est `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="2adda-340">**Default** : The default is `wwwroot`.</span></span> <span data-ttu-id="2adda-341">Le chemin d’accès à *{root content}/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="2adda-341">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="2adda-342">**Variable d’environnement** : `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="2adda-342">**Environment variable** : `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="2adda-343">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2adda-343">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="2adda-344">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="2adda-344">For more information, see:</span></span>

* [<span data-ttu-id="2adda-345">Notions de base : racine Web</span><span class="sxs-lookup"><span data-stu-id="2adda-345">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="2adda-346">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="2adda-346">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="2adda-347">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="2adda-347">Manage the host lifetime</span></span>

<span data-ttu-id="2adda-348">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-348">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="2adda-349">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="2adda-349">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="2adda-350">Exécuter</span><span class="sxs-lookup"><span data-stu-id="2adda-350">Run</span></span>

<span data-ttu-id="2adda-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2adda-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="2adda-352">RunAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-352">RunAsync</span></span>

<span data-ttu-id="2adda-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="2adda-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="2adda-354">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-354">RunConsoleAsync</span></span>

<span data-ttu-id="2adda-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="2adda-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="2adda-356">Démarrer</span><span class="sxs-lookup"><span data-stu-id="2adda-356">Start</span></span>

<span data-ttu-id="2adda-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="2adda-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="2adda-358">StartAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-358">StartAsync</span></span>

<span data-ttu-id="2adda-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="2adda-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="2adda-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="2adda-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="2adda-361">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="2adda-361">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="2adda-362">StopAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-362">StopAsync</span></span>

<span data-ttu-id="2adda-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="2adda-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="2adda-364">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="2adda-364">WaitForShutdown</span></span>

<span data-ttu-id="2adda-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le thread appelant jusqu’à ce que l’arrêt soit déclenché par IHostLifetime, par exemple via <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="2adda-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="2adda-366">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-366">WaitForShutdownAsync</span></span>

<span data-ttu-id="2adda-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2adda-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="2adda-368">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="2adda-368">External control</span></span>

<span data-ttu-id="2adda-369">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="2adda-369">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="2adda-370">Les modèles ASP.NET Core créent un hôte générique .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="2adda-370">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="2adda-371">Cette rubrique fournit des informations sur l’utilisation de l’hôte générique .NET dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2adda-371">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="2adda-372">Pour plus d’informations sur l’utilisation de l’hôte générique .NET dans les applications de console, consultez [hôte générique .net](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="2adda-372">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="2adda-373">Définition de l’hôte</span><span class="sxs-lookup"><span data-stu-id="2adda-373">Host definition</span></span>

<span data-ttu-id="2adda-374">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="2adda-374">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="2adda-375">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="2adda-375">Dependency injection (DI)</span></span>
* <span data-ttu-id="2adda-376">Journalisation</span><span class="sxs-lookup"><span data-stu-id="2adda-376">Logging</span></span>
* <span data-ttu-id="2adda-377">Configuration</span><span class="sxs-lookup"><span data-stu-id="2adda-377">Configuration</span></span>
* <span data-ttu-id="2adda-378">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="2adda-378">`IHostedService` implementations</span></span>

<span data-ttu-id="2adda-379">Lorsqu’un hôte démarre, il appelle <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> inscrite dans la collection de services hébergés du conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="2adda-379">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="2adda-380">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="2adda-380">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="2adda-381">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="2adda-381">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="2adda-382">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="2adda-382">Set up a host</span></span>

<span data-ttu-id="2adda-383">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="2adda-383">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="2adda-384">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="2adda-384">The `Main` method:</span></span>

* <span data-ttu-id="2adda-385">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="2adda-385">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="2adda-386">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="2adda-386">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="2adda-387">Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte générique :</span><span class="sxs-lookup"><span data-stu-id="2adda-387">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="2adda-388">Le code suivant crée un hôte générique à l’aide d’une charge de travail non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="2adda-388">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="2adda-389">L' `IHostedService` implémentation est ajoutée au conteneur di :</span><span class="sxs-lookup"><span data-stu-id="2adda-389">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="2adda-390">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="2adda-390">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="2adda-391">Le code précédent est généré par les modèles ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2adda-391">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="2adda-392">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2adda-392">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="2adda-393">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-393">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="2adda-394">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="2adda-394">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="2adda-395">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="2adda-395">Default builder settings</span></span>

<span data-ttu-id="2adda-396">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> :</span><span class="sxs-lookup"><span data-stu-id="2adda-396">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="2adda-397">Définit la [racine du contenu](xref:fundamentals/index#content-root) sur le chemin d’accès retourné par <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="2adda-397">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="2adda-398">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="2adda-398">Loads host configuration from:</span></span>
  * <span data-ttu-id="2adda-399">Les variables d’environnement précédées de `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="2adda-399">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="2adda-400">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2adda-400">Command-line arguments.</span></span>
* <span data-ttu-id="2adda-401">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="2adda-401">Loads app configuration from:</span></span>
  * <span data-ttu-id="2adda-402">*appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="2adda-402">*appsettings.json* .</span></span>
  * <span data-ttu-id="2adda-403">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="2adda-403">*appsettings.{Environment}.json* .</span></span>
  * <span data-ttu-id="2adda-404">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="2adda-404">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="2adda-405">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="2adda-405">Environment variables.</span></span>
  * <span data-ttu-id="2adda-406">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2adda-406">Command-line arguments.</span></span>
* <span data-ttu-id="2adda-407">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="2adda-407">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="2adda-408">Console</span><span class="sxs-lookup"><span data-stu-id="2adda-408">Console</span></span>
  * <span data-ttu-id="2adda-409">Débogage</span><span class="sxs-lookup"><span data-stu-id="2adda-409">Debug</span></span>
  * <span data-ttu-id="2adda-410">EventSource</span><span class="sxs-lookup"><span data-stu-id="2adda-410">EventSource</span></span>
  * <span data-ttu-id="2adda-411">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="2adda-411">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="2adda-412">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="2adda-412">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="2adda-413">La méthode `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="2adda-413">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="2adda-414">Charge la configuration de l’hôte à partir de variables d’environnement précédées de `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="2adda-414">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="2adda-415">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-415">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="2adda-416">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="2adda-416">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="2adda-417">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2adda-417">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="2adda-418">Ajoute l' [intergiciel d’en-têtes transférés](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est égal à `true` .</span><span class="sxs-lookup"><span data-stu-id="2adda-418">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="2adda-419">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="2adda-419">Enables IIS integration.</span></span> <span data-ttu-id="2adda-420">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="2adda-420">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="2adda-421">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="2adda-421">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="2adda-422">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="2adda-422">Framework-provided services</span></span>

<span data-ttu-id="2adda-423">Les services suivants sont inscrits automatiquement :</span><span class="sxs-lookup"><span data-stu-id="2adda-423">The following services are registered automatically:</span></span>

* [<span data-ttu-id="2adda-424">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="2adda-424">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="2adda-425">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="2adda-425">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="2adda-426">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="2adda-426">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="2adda-427">Pour plus d’informations sur les services fournis par le Framework, consultez <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="2adda-427">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="2adda-428">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="2adda-428">IHostApplicationLifetime</span></span>

<span data-ttu-id="2adda-429">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="2adda-429">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="2adda-430">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-430">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="2adda-431">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="2adda-431">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="2adda-432">L’exemple suivant est une `IHostedService` implémentation qui inscrit des `IHostApplicationLifetime` événements :</span><span class="sxs-lookup"><span data-stu-id="2adda-432">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="2adda-433">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="2adda-433">IHostLifetime</span></span>

<span data-ttu-id="2adda-434">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="2adda-434">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="2adda-435">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="2adda-435">The last implementation registered is used.</span></span>

<span data-ttu-id="2adda-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="2adda-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="2adda-437">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="2adda-437">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="2adda-438">Écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="2adda-438">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="2adda-439">Débloque les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="2adda-439">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="2adda-440">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="2adda-440">IHostEnvironment</span></span>

<span data-ttu-id="2adda-441">Injectez le <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="2adda-441">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="2adda-442">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="2adda-442">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="2adda-443">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="2adda-443">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="2adda-444">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="2adda-444">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="2adda-445">Les applications Web implémentent l' `IWebHostEnvironment` interface, qui hérite `IHostEnvironment` et ajoute [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="2adda-445">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="2adda-446">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="2adda-446">Host configuration</span></span>

<span data-ttu-id="2adda-447">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="2adda-447">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="2adda-448">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span><span class="sxs-lookup"><span data-stu-id="2adda-448">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="2adda-449">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-449">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="2adda-450">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2adda-450">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="2adda-451">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="2adda-451">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="2adda-452">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="2adda-452">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="2adda-453">Le fournisseur de variables d’environnement avec des arguments de préfixe `DOTNET_` et de ligne de commande est inclus par `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2adda-453">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2adda-454">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="2adda-454">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="2adda-455">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="2adda-455">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="2adda-456">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="2adda-456">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="2adda-457">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="2adda-457">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="2adda-458">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="2adda-458">App configuration</span></span>

<span data-ttu-id="2adda-459">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2adda-459">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="2adda-460">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="2adda-460">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="2adda-461">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="2adda-461">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="2adda-462">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2adda-462">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="2adda-463">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-463">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="2adda-464">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="2adda-464">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="2adda-465">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="2adda-465">Settings for all app types</span></span>

<span data-ttu-id="2adda-466">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="2adda-466">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="2adda-467">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="2adda-467">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="2adda-468">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="2adda-468">ApplicationName</span></span>

<span data-ttu-id="2adda-469">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2adda-469">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="2adda-470">**Clé**  : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="2adda-470">**Key** : `applicationName`</span></span>  
<span data-ttu-id="2adda-471">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-471">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-472">**Valeur par défaut** : nom de l’assembly qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-472">**Default** : The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="2adda-473">**Variable d’environnement** : `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="2adda-473">**Environment variable** : `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="2adda-474">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="2adda-474">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="2adda-475">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="2adda-475">ContentRoot</span></span>

<span data-ttu-id="2adda-476">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="2adda-476">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="2adda-477">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="2adda-477">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="2adda-478">**Clé**  : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="2adda-478">**Key** : `contentRoot`</span></span>  
<span data-ttu-id="2adda-479">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-479">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-480">**Par défaut** : le dossier dans lequel se trouve l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-480">**Default** : The folder where the app assembly resides.</span></span>  
<span data-ttu-id="2adda-481">**Variable d’environnement** : `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="2adda-481">**Environment variable** : `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="2adda-482">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2adda-482">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="2adda-483">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="2adda-483">For more information, see:</span></span>

* [<span data-ttu-id="2adda-484">Notions de base : racine du contenu</span><span class="sxs-lookup"><span data-stu-id="2adda-484">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="2adda-485">WebRoot</span><span class="sxs-lookup"><span data-stu-id="2adda-485">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="2adda-486">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="2adda-486">EnvironmentName</span></span>

<span data-ttu-id="2adda-487">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="2adda-487">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="2adda-488">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="2adda-488">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="2adda-489">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="2adda-489">Values aren't case-sensitive.</span></span>

<span data-ttu-id="2adda-490">**Clé**  : `environment`</span><span class="sxs-lookup"><span data-stu-id="2adda-490">**Key** : `environment`</span></span>  
<span data-ttu-id="2adda-491">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-491">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-492">**Par défaut** : `Production`</span><span class="sxs-lookup"><span data-stu-id="2adda-492">**Default** : `Production`</span></span>  
<span data-ttu-id="2adda-493">**Variable d’environnement** : `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="2adda-493">**Environment variable** : `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="2adda-494">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2adda-494">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="2adda-495">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="2adda-495">ShutdownTimeout</span></span>

<span data-ttu-id="2adda-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2adda-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="2adda-497">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="2adda-497">The default value is five seconds.</span></span>  <span data-ttu-id="2adda-498">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="2adda-498">During the timeout period, the host:</span></span>

* <span data-ttu-id="2adda-499">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="2adda-499">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="2adda-500">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="2adda-500">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="2adda-501">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="2adda-501">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="2adda-502">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="2adda-502">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="2adda-503">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="2adda-503">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="2adda-504">**Clé**  : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="2adda-504">**Key** : `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="2adda-505">**Type**  : `int`</span><span class="sxs-lookup"><span data-stu-id="2adda-505">**Type** : `int`</span></span>  
<span data-ttu-id="2adda-506">**Valeur par défaut** : 5 secondes</span><span class="sxs-lookup"><span data-stu-id="2adda-506">**Default** : 5 seconds</span></span>  
<span data-ttu-id="2adda-507">**Variable d’environnement** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="2adda-507">**Environment variable** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="2adda-508">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="2adda-508">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="2adda-509">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="2adda-509">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="2adda-510">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="2adda-510">Settings for web apps</span></span>

<span data-ttu-id="2adda-511">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="2adda-511">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="2adda-512">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="2adda-512">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="2adda-513">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="2adda-513">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="2adda-514">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="2adda-514">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="2adda-515">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="2adda-515">CaptureStartupErrors</span></span>

<span data-ttu-id="2adda-516">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2adda-516">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="2adda-517">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="2adda-517">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="2adda-518">**Clé**  : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="2adda-518">**Key** : `captureStartupErrors`</span></span>  
<span data-ttu-id="2adda-519">**Type** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="2adda-519">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="2adda-520">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="2adda-520">**Default** : Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="2adda-521">**Variable d’environnement** : `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="2adda-521">**Environment variable** : `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="2adda-522">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="2adda-522">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="2adda-523">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="2adda-523">DetailedErrors</span></span>

<span data-ttu-id="2adda-524">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="2adda-524">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="2adda-525">**Clé**  : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="2adda-525">**Key** : `detailedErrors`</span></span>  
<span data-ttu-id="2adda-526">**Type** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="2adda-526">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="2adda-527">**Par défaut** : `false`</span><span class="sxs-lookup"><span data-stu-id="2adda-527">**Default** : `false`</span></span>  
<span data-ttu-id="2adda-528">**Variable d’environnement** : `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="2adda-528">**Environment variable** : `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="2adda-529">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="2adda-529">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="2adda-530">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="2adda-530">HostingStartupAssemblies</span></span>

<span data-ttu-id="2adda-531">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="2adda-531">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="2adda-532">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-532">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="2adda-533">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="2adda-533">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="2adda-534">**Clé**  : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="2adda-534">**Key** : `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="2adda-535">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-535">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-536">**Valeur par défaut** : chaîne vide</span><span class="sxs-lookup"><span data-stu-id="2adda-536">**Default** : Empty string</span></span>  
<span data-ttu-id="2adda-537">**Variable d’environnement** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="2adda-537">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="2adda-538">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="2adda-538">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="2adda-539">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="2adda-539">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="2adda-540">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="2adda-540">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="2adda-541">**Clé**  : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="2adda-541">**Key** : `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="2adda-542">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-542">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-543">**Valeur par défaut** : chaîne vide</span><span class="sxs-lookup"><span data-stu-id="2adda-543">**Default** : Empty string</span></span>  
<span data-ttu-id="2adda-544">**Variable d’environnement** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="2adda-544">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="2adda-545">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="2adda-545">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="2adda-546">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="2adda-546">HTTPS_Port</span></span>

<span data-ttu-id="2adda-547">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2adda-547">The HTTPS redirect port.</span></span> <span data-ttu-id="2adda-548">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="2adda-548">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2adda-549">**Clé**  : `https_port`</span><span class="sxs-lookup"><span data-stu-id="2adda-549">**Key** : `https_port`</span></span>  
<span data-ttu-id="2adda-550">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-550">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-551">Valeur **par défaut** : aucune valeur par défaut n’est définie.</span><span class="sxs-lookup"><span data-stu-id="2adda-551">**Default** : A default value isn't set.</span></span>  
<span data-ttu-id="2adda-552">**Variable d’environnement** : `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="2adda-552">**Environment variable** : `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="2adda-553">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="2adda-553">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="2adda-554">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="2adda-554">PreferHostingUrls</span></span>

<span data-ttu-id="2adda-555">Indique si l’hôte doit écouter les URL configurées avec le `IWebHostBuilder` au lieu des URL configurées avec l' `IServer` implémentation.</span><span class="sxs-lookup"><span data-stu-id="2adda-555">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="2adda-556">**Clé**  : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="2adda-556">**Key** : `preferHostingUrls`</span></span>  
<span data-ttu-id="2adda-557">**Type** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="2adda-557">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="2adda-558">**Par défaut** : `true`</span><span class="sxs-lookup"><span data-stu-id="2adda-558">**Default** : `true`</span></span>  
<span data-ttu-id="2adda-559">**Variable d’environnement** : `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="2adda-559">**Environment variable** : `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="2adda-560">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="2adda-560">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="2adda-561">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="2adda-561">PreventHostingStartup</span></span>

<span data-ttu-id="2adda-562">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-562">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="2adda-563">Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2adda-563">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="2adda-564">**Clé**  : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="2adda-564">**Key** : `preventHostingStartup`</span></span>  
<span data-ttu-id="2adda-565">**Type** : `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="2adda-565">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="2adda-566">**Par défaut** : `false`</span><span class="sxs-lookup"><span data-stu-id="2adda-566">**Default** : `false`</span></span>  
<span data-ttu-id="2adda-567">**Variable d’environnement** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="2adda-567">**Environment variable** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="2adda-568">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="2adda-568">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="2adda-569">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="2adda-569">StartupAssembly</span></span>

<span data-ttu-id="2adda-570">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="2adda-570">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="2adda-571">**Clé**  : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="2adda-571">**Key** : `startupAssembly`</span></span>  
<span data-ttu-id="2adda-572">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-572">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-573">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="2adda-573">**Default** : The app's assembly</span></span>  
<span data-ttu-id="2adda-574">**Variable d’environnement** : `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="2adda-574">**Environment variable** : `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="2adda-575">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="2adda-575">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="2adda-576">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="2adda-576">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="2adda-577">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="2adda-577">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="2adda-578">URLs</span><span class="sxs-lookup"><span data-stu-id="2adda-578">URLs</span></span>

<span data-ttu-id="2adda-579">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="2adda-579">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="2adda-580">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="2adda-580">For example, `http://localhost:123`.</span></span> <span data-ttu-id="2adda-581">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="2adda-581">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="2adda-582">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="2adda-582">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="2adda-583">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="2adda-583">Supported formats vary among servers.</span></span>

<span data-ttu-id="2adda-584">**Clé**  : `urls`</span><span class="sxs-lookup"><span data-stu-id="2adda-584">**Key** : `urls`</span></span>  
<span data-ttu-id="2adda-585">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-585">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-586">**Par défaut** : `http://localhost:5000` et `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="2adda-586">**Default** : `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="2adda-587">**Variable d’environnement** : `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="2adda-587">**Environment variable** : `<PREFIX_>URLS`</span></span>

<span data-ttu-id="2adda-588">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="2adda-588">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="2adda-589">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="2adda-589">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="2adda-590">Pour plus d'informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2adda-590">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="2adda-591">WebRoot</span><span class="sxs-lookup"><span data-stu-id="2adda-591">WebRoot</span></span>

<span data-ttu-id="2adda-592">La propriété [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin d’accès relatif aux ressources statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-592">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="2adda-593">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="2adda-593">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="2adda-594">**Clé**  : `webroot`</span><span class="sxs-lookup"><span data-stu-id="2adda-594">**Key** : `webroot`</span></span>  
<span data-ttu-id="2adda-595">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-595">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-596">**Valeur par défaut** : la valeur par défaut est `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="2adda-596">**Default** : The default is `wwwroot`.</span></span> <span data-ttu-id="2adda-597">Le chemin d’accès à *{root content}/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="2adda-597">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="2adda-598">**Variable d’environnement** : `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="2adda-598">**Environment variable** : `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="2adda-599">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2adda-599">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="2adda-600">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="2adda-600">For more information, see:</span></span>

* [<span data-ttu-id="2adda-601">Notions de base : racine Web</span><span class="sxs-lookup"><span data-stu-id="2adda-601">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="2adda-602">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="2adda-602">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="2adda-603">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="2adda-603">Manage the host lifetime</span></span>

<span data-ttu-id="2adda-604">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-604">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="2adda-605">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="2adda-605">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="2adda-606">Exécuter</span><span class="sxs-lookup"><span data-stu-id="2adda-606">Run</span></span>

<span data-ttu-id="2adda-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2adda-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="2adda-608">RunAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-608">RunAsync</span></span>

<span data-ttu-id="2adda-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="2adda-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="2adda-610">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-610">RunConsoleAsync</span></span>

<span data-ttu-id="2adda-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="2adda-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="2adda-612">Démarrer</span><span class="sxs-lookup"><span data-stu-id="2adda-612">Start</span></span>

<span data-ttu-id="2adda-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="2adda-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="2adda-614">StartAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-614">StartAsync</span></span>

<span data-ttu-id="2adda-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="2adda-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="2adda-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="2adda-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="2adda-617">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="2adda-617">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="2adda-618">StopAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-618">StopAsync</span></span>

<span data-ttu-id="2adda-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="2adda-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="2adda-620">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="2adda-620">WaitForShutdown</span></span>

<span data-ttu-id="2adda-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le thread appelant jusqu’à ce que l’arrêt soit déclenché par IHostLifetime, par exemple via <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="2adda-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="2adda-622">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-622">WaitForShutdownAsync</span></span>

<span data-ttu-id="2adda-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2adda-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="2adda-624">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="2adda-624">External control</span></span>

<span data-ttu-id="2adda-625">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="2adda-625">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="2adda-626">Les applications ASP.NET Core configurent et lancent un hôte.</span><span class="sxs-lookup"><span data-stu-id="2adda-626">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="2adda-627">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="2adda-627">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="2adda-628">Cet article traite de l’hôte générique ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), qui est utilisé pour les applications qui ne traitent pas les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="2adda-628">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="2adda-629">L’objectif de l’hôte générique est de séparer le pipeline HTTP de l’API Hôte web pour permettre un plus large éventail de scénarios d’hôte.</span><span class="sxs-lookup"><span data-stu-id="2adda-629">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="2adda-630">La messagerie, les tâches en arrière-plan et autres charges de travail non HTTP basées sur l’hôte générique bénéficient de fonctionnalités transversales, comme la configuration, l’injection de dépendances (DI) et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="2adda-630">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="2adda-631">L’hôte générique est nouveau dans ASP.NET Core 2.1 et n’est pas adapté aux scénarios d’hébergement web.</span><span class="sxs-lookup"><span data-stu-id="2adda-631">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="2adda-632">Pour les scénarios d’hébergement de web, utilisez l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="2adda-632">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="2adda-633">L’hôte générique est appelé à remplacer l’hôte web dans une version ultérieure et à servir d’API hôte principale dans les scénarios HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="2adda-633">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="2adda-634">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2adda-634">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2adda-635">Quand vous exécutez l’exemple d’application dans [Visual Studio Code](https://code.visualstudio.com/), utilisez un *terminal externe ou intégré* .</span><span class="sxs-lookup"><span data-stu-id="2adda-635">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal* .</span></span> <span data-ttu-id="2adda-636">N’exécutez pas l’exemple dans une `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="2adda-636">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="2adda-637">Pour définir la console dans Visual Studio Code :</span><span class="sxs-lookup"><span data-stu-id="2adda-637">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="2adda-638">Ouvrez le fichier *.vscode/launch.json* .</span><span class="sxs-lookup"><span data-stu-id="2adda-638">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="2adda-639">Dans la configuration **.NET Core Launch (console)** , recherchez l’entrée **console** .</span><span class="sxs-lookup"><span data-stu-id="2adda-639">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="2adda-640">Définissez la valeur avec `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="2adda-640">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="2adda-641">Introduction</span><span class="sxs-lookup"><span data-stu-id="2adda-641">Introduction</span></span>

<span data-ttu-id="2adda-642">La bibliothèque de l’hôte générique est disponible dans l’espace de noms <xref:Microsoft.Extensions.Hosting> et est fournie par le package [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="2adda-642">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="2adda-643">Le package `Microsoft.Extensions.Hosting` est inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou ultérieur).</span><span class="sxs-lookup"><span data-stu-id="2adda-643">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="2adda-644"><xref:Microsoft.Extensions.Hosting.IHostedService> est le point d’entrée pour exécuter le code.</span><span class="sxs-lookup"><span data-stu-id="2adda-644"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="2adda-645">Chaque implémentation <xref:Microsoft.Extensions.Hosting.IHostedService> est exécutée dans l’ordre d’[inscription des services dans ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="2adda-645">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="2adda-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> est appelé sur chaque <xref:Microsoft.Extensions.Hosting.IHostedService> au démarrage de l’hôte, tandis que <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> est appelé dans l’ordre d’inscription inverse quand l’hôte s’arrête normalement.</span><span class="sxs-lookup"><span data-stu-id="2adda-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="2adda-647">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="2adda-647">Set up a host</span></span>

<span data-ttu-id="2adda-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> est le principal composant que les applications et les bibliothèques utilisent pour initialiser, générer et exécuter l’hôte :</span><span class="sxs-lookup"><span data-stu-id="2adda-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="2adda-649">Options</span><span class="sxs-lookup"><span data-stu-id="2adda-649">Options</span></span>

<span data-ttu-id="2adda-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configure les options pour <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="2adda-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="2adda-651">Délai d’expiration</span><span class="sxs-lookup"><span data-stu-id="2adda-651">Shutdown timeout</span></span>

<span data-ttu-id="2adda-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2adda-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="2adda-653">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="2adda-653">The default value is five seconds.</span></span>

<span data-ttu-id="2adda-654">La configuration d’option suivante dans `Program.Main` augmente le délai d’attente d’arrêt de cinq secondes par défaut à 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="2adda-654">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="2adda-655">Services par défaut</span><span class="sxs-lookup"><span data-stu-id="2adda-655">Default services</span></span>

<span data-ttu-id="2adda-656">Les services suivants sont inscrits au moment de l’initialisation de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="2adda-656">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="2adda-657">[Environnement](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="2adda-657">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="2adda-658">[Configuration](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="2adda-658">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="2adda-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="2adda-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="2adda-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="2adda-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="2adda-661">[Options](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="2adda-661">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="2adda-662">[Journalisation](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="2adda-662">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="2adda-663">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="2adda-663">Host configuration</span></span>

<span data-ttu-id="2adda-664">La création de la configuration d’hôte fait appel aux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="2adda-664">Host configuration is created by:</span></span>

* <span data-ttu-id="2adda-665">Appel de méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder> pour définir la [racine du contenu](#content-root) et [l’environnement](#environment).</span><span class="sxs-lookup"><span data-stu-id="2adda-665">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="2adda-666">Lecture de la configuration à partir des fournisseurs de configuration dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="2adda-666">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="2adda-667">Méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="2adda-667">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="2adda-668">Clé d’application (nom)</span><span class="sxs-lookup"><span data-stu-id="2adda-668">Application key (name)</span></span>

<span data-ttu-id="2adda-669">La propriété [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) est définie à partir de la configuration de l’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2adda-669">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="2adda-670">Pour définir la valeur explicitement, utilisez [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey) :</span><span class="sxs-lookup"><span data-stu-id="2adda-670">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="2adda-671">**Clé**  : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="2adda-671">**Key** : `applicationName`</span></span>  
<span data-ttu-id="2adda-672">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-672">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-673">**Par défaut**  : nom de l’assembly contenant le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-673">**Default** : The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="2adda-674">**Définir à l’aide** de : `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="2adda-674">**Set using** : `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="2adda-675">**Variable d’environnement** : `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="2adda-675">**Environment variable** : `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="2adda-676">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="2adda-676">Content root</span></span>

<span data-ttu-id="2adda-677">Ce paramètre détermine où l’hôte commence la recherche des fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="2adda-677">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="2adda-678">**Clé**  : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="2adda-678">**Key** : `contentRoot`</span></span>  
<span data-ttu-id="2adda-679">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-679">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-680">**Valeur par défaut** : dossier où réside l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-680">**Default** : Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="2adda-681">**Définir à l’aide** de : `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="2adda-681">**Set using** : `UseContentRoot`</span></span>  
<span data-ttu-id="2adda-682">**Variable d’environnement** : `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="2adda-682">**Environment variable** : `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="2adda-683">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="2adda-683">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="2adda-684">Pour plus d’informations, consultez [principes de base : racine du contenu](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="2adda-684">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="2adda-685">Environnement</span><span class="sxs-lookup"><span data-stu-id="2adda-685">Environment</span></span>

<span data-ttu-id="2adda-686">Définit l' [environnement](xref:fundamentals/environments)de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-686">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="2adda-687">**Clé**  : `environment`</span><span class="sxs-lookup"><span data-stu-id="2adda-687">**Key** : `environment`</span></span>  
<span data-ttu-id="2adda-688">**Type**  : `string`</span><span class="sxs-lookup"><span data-stu-id="2adda-688">**Type** : `string`</span></span>  
<span data-ttu-id="2adda-689">**Par défaut** : `Production`</span><span class="sxs-lookup"><span data-stu-id="2adda-689">**Default** : `Production`</span></span>  
<span data-ttu-id="2adda-690">**Définir à l’aide** de : `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="2adda-690">**Set using** : `UseEnvironment`</span></span>  
<span data-ttu-id="2adda-691">**Variable d’environnement** : `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="2adda-691">**Environment variable** : `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="2adda-692">L’environnement peut être défini à n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="2adda-692">The environment can be set to any value.</span></span> <span data-ttu-id="2adda-693">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="2adda-693">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="2adda-694">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="2adda-694">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="2adda-695">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="2adda-695">ConfigureHostConfiguration</span></span>

<span data-ttu-id="2adda-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2adda-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="2adda-697">La configuration d’hôte permet d’initialiser le <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> en vue de son utilisation dans le processus de génération de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-697">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="2adda-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="2adda-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="2adda-699">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="2adda-699">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="2adda-700">Aucun fournisseur n’est inclus par défaut.</span><span class="sxs-lookup"><span data-stu-id="2adda-700">No providers are included by default.</span></span> <span data-ttu-id="2adda-701">Vous devez spécifier explicitement les fournisseurs de configuration dont l’application a besoin dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, notamment :</span><span class="sxs-lookup"><span data-stu-id="2adda-701">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="2adda-702">Configuration du fichier (par exemple, à partir d’un fichier *hostsettings.json* ).</span><span class="sxs-lookup"><span data-stu-id="2adda-702">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="2adda-703">Configuration de la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="2adda-703">Environment variable configuration.</span></span>
* <span data-ttu-id="2adda-704">Configuration de l’argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="2adda-704">Command-line argument configuration.</span></span>
* <span data-ttu-id="2adda-705">Tout autre fournisseur de configuration obligatoire.</span><span class="sxs-lookup"><span data-stu-id="2adda-705">Any other required configuration providers.</span></span>

<span data-ttu-id="2adda-706">Pour activer la configuration de fichier de l’hôte, spécifiez le chemin de base de l’application avec `SetBasePath`, suivi d’un appel à l’un des [fournisseurs de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2adda-706">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="2adda-707">L’exemple d’application utilise un fichier JSON, *hostsettings.json* , et appelle <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> pour utiliser les paramètres de configuration d’hôte du fichier.</span><span class="sxs-lookup"><span data-stu-id="2adda-707">The sample app uses a JSON file, *hostsettings.json* , and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="2adda-708">Pour ajouter la [configuration de variable d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider) de l’hôte, appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur le générateur d’hôte.</span><span class="sxs-lookup"><span data-stu-id="2adda-708">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="2adda-709">`AddEnvironmentVariables` accepte un préfixe facultatif défini par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="2adda-709">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="2adda-710">L’exemple d’application utilise un préfixe `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="2adda-710">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="2adda-711">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="2adda-711">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="2adda-712">Lorsque l’hôte de l’exemple d’application est configuré, la valeur de variable d’environnement de `PREFIX_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="2adda-712">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="2adda-713">Pendant le développement, lorsque vous utilisez [Visual Studio](https://visualstudio.microsoft.com) ou que vous lancez une application avec `dotnet run`, vous pouvez définir les variables d’environnement dans le fichier *Properties/launchSettings.json* .</span><span class="sxs-lookup"><span data-stu-id="2adda-713">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="2adda-714">Dans [Visual Studio Code](https://code.visualstudio.com/), elles peuvent être définies dans le fichier *.vscode/launch.json* au cours du développement.</span><span class="sxs-lookup"><span data-stu-id="2adda-714">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="2adda-715">Pour plus d'informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2adda-715">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="2adda-716">Pour ajouter la [configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider), appelez <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="2adda-716">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="2adda-717">La configuration de ligne de commande est ajoutée en dernier pour permettre aux arguments de ligne de commande de substituer la configuration fournie par les fournisseurs de configuration antérieurs.</span><span class="sxs-lookup"><span data-stu-id="2adda-717">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="2adda-718">*hostsettings.json*  :</span><span class="sxs-lookup"><span data-stu-id="2adda-718">*hostsettings.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="2adda-719">Une configuration supplémentaire peut être fournie à l’aide des clés [applicationName](#application-key-name) et [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="2adda-719">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="2adda-720">Exemple de configuration `HostBuilder` avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="2adda-720">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="2adda-721">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="2adda-721">ConfigureAppConfiguration</span></span>

<span data-ttu-id="2adda-722">Pour créer la configuration d’application, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur l’implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2adda-722">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="2adda-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="2adda-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="2adda-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="2adda-725">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="2adda-725">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="2adda-726">La configuration créée par <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et dans <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="2adda-726">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="2adda-727">La configuration d’application reçoit automatiquement la configuration d’hôte fournie par [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="2adda-727">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="2adda-728">Exemple de configuration d’application avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="2adda-728">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="2adda-729">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="2adda-729">*appsettings.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="2adda-730">*appsettings.Development.json*  :</span><span class="sxs-lookup"><span data-stu-id="2adda-730">*appsettings.Development.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="2adda-731">*appsettings.Production.json*  :</span><span class="sxs-lookup"><span data-stu-id="2adda-731">*appsettings.Production.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="2adda-732">Pour déplacer des fichiers de paramètres vers le répertoire de sortie, spécifiez-les en tant qu’[éléments de projet MSBuild](/visualstudio/msbuild/common-msbuild-project-items) dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="2adda-732">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="2adda-733">L’exemple d’application déplace ses fichiers de paramètres d’application JSON et *hostsettings.json* avec l’élément `<Content>` suivant :</span><span class="sxs-lookup"><span data-stu-id="2adda-733">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="2adda-734">Les méthodes d’extension de configuration, telles que <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> et <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, nécessitent des packages NuGet supplémentaires, tels que [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) et [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="2adda-734">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="2adda-735">Si l’application n’utilise pas le [métapaquet Microsoft.AspNetCore.App ](xref:fundamentals/metapackage-app), ces packages doivent être ajoutés au projet en plus du noyau [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="2adda-735">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="2adda-736">Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="2adda-736">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="2adda-737">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="2adda-737">ConfigureServices</span></span>

<span data-ttu-id="2adda-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> ajoute des services au conteneur [d’injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2adda-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="2adda-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="2adda-740">Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="2adda-740">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="2adda-741">Pour plus d'informations, consultez <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="2adda-741">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="2adda-742">L’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise la méthode d’extension `AddHostedService` afin d’ajouter un service pour les événements de durée de vie, `LifetimeEventsHostedService`, et une tâche en arrière-plan chronométrée, `TimedHostedService`, à l’application :</span><span class="sxs-lookup"><span data-stu-id="2adda-742">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="2adda-743">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="2adda-743">ConfigureLogging</span></span>

<span data-ttu-id="2adda-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> ajoute un délégué pour configurer le <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fourni.</span><span class="sxs-lookup"><span data-stu-id="2adda-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="2adda-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="2adda-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="2adda-746">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="2adda-746">UseConsoleLifetime</span></span>

<span data-ttu-id="2adda-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="2adda-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="2adda-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> déverrouille les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="2adda-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="2adda-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est préinscrit comme implémentation de durée de vie par défaut.</span><span class="sxs-lookup"><span data-stu-id="2adda-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="2adda-750">La dernière durée de vie inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="2adda-750">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="2adda-751">Configuration du conteneur</span><span class="sxs-lookup"><span data-stu-id="2adda-751">Container configuration</span></span>

<span data-ttu-id="2adda-752">Pour prendre en charge le branchement dans d’autres conteneurs, l’hôte peut accepter un <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="2adda-752">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="2adda-753">L’ajout d’une fabrique ne fait pas partie de l’inscription de conteneur DI mais est plutôt une tâche un intrinsèque à l’hôte utilisée pour créer le conteneur DI concret.</span><span class="sxs-lookup"><span data-stu-id="2adda-753">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="2adda-754">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) remplace la fabrique par défaut utilisée pour créer le fournisseur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="2adda-755">La configuration de conteneur personnalisée est gérée par la méthode <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="2adda-755">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="2adda-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre une expérience fortement typée pour configurer le conteneur sur l’API hôte sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2adda-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="2adda-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="2adda-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="2adda-758">Créer un conteneur de service pour l’application :</span><span class="sxs-lookup"><span data-stu-id="2adda-758">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="2adda-759">Fournir une fabrique de conteneur de service :</span><span class="sxs-lookup"><span data-stu-id="2adda-759">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="2adda-760">Utiliser la fabrique et configurer le conteneur de service personnalisé pour l’application :</span><span class="sxs-lookup"><span data-stu-id="2adda-760">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="2adda-761">Extensibilité</span><span class="sxs-lookup"><span data-stu-id="2adda-761">Extensibility</span></span>

<span data-ttu-id="2adda-762">L’extensibilité de l’hôte est effectuée avec les méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2adda-762">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="2adda-763">L’exemple suivant montre comment une méthode d’extension étend une implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder> avec l’exemple [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) démontré dans <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="2adda-763">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="2adda-764">Une application établit la méthode d'extension `UseHostedService` pour inscrire le service hébergé passé dans `T` :</span><span class="sxs-lookup"><span data-stu-id="2adda-764">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="2adda-765">Gérer l’hôte</span><span class="sxs-lookup"><span data-stu-id="2adda-765">Manage the host</span></span>

<span data-ttu-id="2adda-766">L’implémentation <xref:Microsoft.Extensions.Hosting.IHost> est chargée de démarrer et d’arrêter les implémentations <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="2adda-766">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="2adda-767">Exécuter</span><span class="sxs-lookup"><span data-stu-id="2adda-767">Run</span></span>

<span data-ttu-id="2adda-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="2adda-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="2adda-769">RunAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-769">RunAsync</span></span>

<span data-ttu-id="2adda-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand le jeton d’annulation ou l’arrêt est déclenché :</span><span class="sxs-lookup"><span data-stu-id="2adda-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="2adda-771">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-771">RunConsoleAsync</span></span>

<span data-ttu-id="2adda-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="2adda-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="2adda-773">Start et StopAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-773">Start and StopAsync</span></span>

<span data-ttu-id="2adda-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="2adda-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="2adda-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="2adda-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="2adda-776">StartAsync et StopAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-776">StartAsync and StopAsync</span></span>

<span data-ttu-id="2adda-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="2adda-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arrête l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="2adda-779">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="2adda-779">WaitForShutdown</span></span>

<span data-ttu-id="2adda-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>est déclenché via le <xref:Microsoft.Extensions.Hosting.IHostLifetime> , par exemple `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (écoute la <kbd>touche Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="2adda-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="2adda-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2adda-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="2adda-782">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="2adda-782">WaitForShutdownAsync</span></span>

<span data-ttu-id="2adda-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2adda-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="2adda-784">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="2adda-784">External control</span></span>

<span data-ttu-id="2adda-785">Le contrôle externe de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="2adda-785">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="2adda-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="2adda-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="2adda-787">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="2adda-787">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="2adda-788">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="2adda-788">IHostingEnvironment interface</span></span>

<span data-ttu-id="2adda-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fournit des informations sur l’environnement d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="2adda-790">Utilisez [l’injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir l’interface <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> afin d’utiliser ses propriétés et méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="2adda-790">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="2adda-791">Pour plus d'informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2adda-791">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="2adda-792">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="2adda-792">IApplicationLifetime interface</span></span>

<span data-ttu-id="2adda-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> s’utilise pour les activités de post-démarrage et d’arrêt, notamment pour les demandes d’arrêt normal.</span><span class="sxs-lookup"><span data-stu-id="2adda-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="2adda-794">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes <xref:System.Action> qui définissent les événements de démarrage et d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="2adda-794">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="2adda-795">Jeton d’annulation</span><span class="sxs-lookup"><span data-stu-id="2adda-795">Cancellation Token</span></span> | <span data-ttu-id="2adda-796">Événement déclencheur</span><span class="sxs-lookup"><span data-stu-id="2adda-796">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="2adda-797">L’hôte a complètement démarré.</span><span class="sxs-lookup"><span data-stu-id="2adda-797">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="2adda-798">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="2adda-798">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="2adda-799">Toutes les requêtes doivent être traitées.</span><span class="sxs-lookup"><span data-stu-id="2adda-799">All requests should be processed.</span></span> <span data-ttu-id="2adda-800">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="2adda-800">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="2adda-801">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="2adda-801">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="2adda-802">Des requêtes peuvent encore être en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="2adda-802">Requests may still be processing.</span></span> <span data-ttu-id="2adda-803">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="2adda-803">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="2adda-804">Le constructeur injecte le service <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> dans une classe.</span><span class="sxs-lookup"><span data-stu-id="2adda-804">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="2adda-805">[L’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise l’injection de constructeur dans une classe `LifetimeEventsHostedService` (une implémentation <xref:Microsoft.Extensions.Hosting.IHostedService>) pour inscrire les événements.</span><span class="sxs-lookup"><span data-stu-id="2adda-805">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="2adda-806">*LifetimeEventsHostedService.cs* :</span><span class="sxs-lookup"><span data-stu-id="2adda-806">*LifetimeEventsHostedService.cs* :</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="2adda-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> demande l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="2adda-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="2adda-808">La classe suivante utilise <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour arrêter normalement une application lors de l’appel de la méthode `Shutdown` de la classe :</span><span class="sxs-lookup"><span data-stu-id="2adda-808">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="2adda-809">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2adda-809">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
