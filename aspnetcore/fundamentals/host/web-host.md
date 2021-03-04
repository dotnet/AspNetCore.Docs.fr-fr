---
title: Hôte web ASP.NET Core
author: rick-anderson
description: Découvrez l’hôte web dans ASP.NET Core, qui est responsable de la gestion du démarrage et de la durée de vie des applications.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: fa9b1941d6dcda30855a4729dfa1cd78f897d9b6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109973"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="bd459-103">Hôte web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bd459-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="bd459-104">ASP.NET Core les applications configurent et lancent un *hôte*.</span><span class="sxs-lookup"><span data-stu-id="bd459-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="bd459-105">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="bd459-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="bd459-106">Au minimum, l’hôte configure un serveur ainsi qu’un pipeline de traitement des requêtes.</span><span class="sxs-lookup"><span data-stu-id="bd459-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="bd459-107">L’hôte peut aussi configurer la journalisation, l’injection de dépendances et la configuration.</span><span class="sxs-lookup"><span data-stu-id="bd459-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bd459-108">Cet article traite de l’hôte Web, qui reste disponible uniquement pour la compatibilité descendante.</span><span class="sxs-lookup"><span data-stu-id="bd459-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="bd459-109">Les modèles ASP.NET Core créent un [hôte générique .net](<xref:fundamentals/host/generic-host>), ce qui est recommandé pour tous les types d’applications.</span><span class="sxs-lookup"><span data-stu-id="bd459-109">The ASP.NET Core templates create a [.NET Generic Host](<xref:fundamentals/host/generic-host>), which is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bd459-110">Cet article traite de l’hôte web qui est responsable de l’hébergement des applications web.</span><span class="sxs-lookup"><span data-stu-id="bd459-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="bd459-111">Pour d’autres types d’applications, utilisez l’[Hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="bd459-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="bd459-112">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="bd459-112">Set up a host</span></span>

<span data-ttu-id="bd459-113">Créez un hôte en utilisant une instance de [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="bd459-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="bd459-114">Cette opération est généralement effectuée au point d’entrée de l’application, à savoir la méthode `Main`.</span><span class="sxs-lookup"><span data-stu-id="bd459-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="bd459-115">Dans les modèles de projet, `Main` se trouve dans *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bd459-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="bd459-116">Une application standard appelle [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) pour lancer la configuration d’un hôte :</span><span class="sxs-lookup"><span data-stu-id="bd459-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="bd459-117">Le code qui appelle `CreateDefaultBuilder` est dans une méthode nommée `CreateWebHostBuilder`, qui le sépare du code dans `Main` qui appelle `Run` sur l’objet du générateur.</span><span class="sxs-lookup"><span data-stu-id="bd459-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="bd459-118">Cette séparation est requise si vous utilisez [les outils Entity Framework Core](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="bd459-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="bd459-119">Les outils s’attendent à trouver une méthode `CreateWebHostBuilder` qu’ils peuvent appeler au moment du design pour configurer l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="bd459-120">Une alternative consiste à implémenter `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="bd459-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="bd459-121">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="bd459-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="bd459-122">`CreateDefaultBuilder` effectue les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="bd459-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="bd459-123">Configure le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="bd459-124">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel/options>.</span><span class="sxs-lookup"><span data-stu-id="bd459-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel/options>.</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* <span data-ttu-id="bd459-125">Configure le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-125">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="bd459-126">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="bd459-126">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
::: moniker-end
* <span data-ttu-id="bd459-127">Définit la [racine du contenu](xref:fundamentals/index#content-root) sur le chemin d’accès retourné par [Directory. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="bd459-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="bd459-128">Charge la [configuration de l’hôte](#host-configuration-values) à partir de :</span><span class="sxs-lookup"><span data-stu-id="bd459-128">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="bd459-129">Variables d’environnement comportant le préfixe `ASPNETCORE_` (par exemple, `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="bd459-129">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="bd459-130">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="bd459-130">Command-line arguments.</span></span>
* <span data-ttu-id="bd459-131">Charge la configuration de l’application dans l’ordre suivant à partir des éléments ci-après :</span><span class="sxs-lookup"><span data-stu-id="bd459-131">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="bd459-132">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="bd459-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="bd459-133">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="bd459-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="bd459-134">Les [secrets utilisateur](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development` à l’aide de l’assembly d’entrée</span><span class="sxs-lookup"><span data-stu-id="bd459-134">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="bd459-135">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="bd459-135">Environment variables.</span></span>
  * <span data-ttu-id="bd459-136">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="bd459-136">Command-line arguments.</span></span>
* <span data-ttu-id="bd459-137">Configure la [journalisation](xref:fundamentals/logging/index) des sorties de la console et du débogage.</span><span class="sxs-lookup"><span data-stu-id="bd459-137">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="bd459-138">La journalisation comprend les règles de [filtrage de journal](xref:fundamentals/logging/index#log-filtering) spécifiées dans une section de configuration de journalisation d’un *appsettings.json* ou *appSettings. { Fichier Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="bd459-138">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="bd459-139">Lors de l’exécution derrière IIS avec le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` active l' [intégration IIS](xref:host-and-deploy/iis/index), qui configure l’adresse et le port de base de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-139">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="bd459-140">L’intégration IIS configure également l’application pour la [capture des erreurs de démarrage](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="bd459-140">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="bd459-141">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="bd459-141">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="bd459-142">Définissez [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) sur `true` si l’environnement de l’application est Développement.</span><span class="sxs-lookup"><span data-stu-id="bd459-142">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="bd459-143">Pour plus d’informations, consultez [Validation de l’étendue](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="bd459-143">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="bd459-144">La configuration définie par `CreateDefaultBuilder` peut être remplacée et enrichie par [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) et les autres méthodes et les méthodes d’extension de [ IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="bd459-144">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="bd459-145">En voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="bd459-145">A few examples follow:</span></span>

* <span data-ttu-id="bd459-146">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) permet de spécifier une configuration `IConfiguration` supplémentaire pour l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-146">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="bd459-147">L’appel `ConfigureAppConfiguration` suivant ajoute un délégué pour inclure la configuration de l’application dans le fichier *appsettings.xml*.</span><span class="sxs-lookup"><span data-stu-id="bd459-147">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="bd459-148">`ConfigureAppConfiguration` peut être appelé plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="bd459-148">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="bd459-149">Notez que cette configuration ne s’applique pas à l’hôte (par exemple, les URL de serveur ou l’environnement).</span><span class="sxs-lookup"><span data-stu-id="bd459-149">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="bd459-150">Voir la section [Valeurs de configuration de l’hôte](#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="bd459-150">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="bd459-151">L’appel `ConfigureLogging` suivant ajoute un délégué pour configurer le niveau de journalisation minimal ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) sur [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="bd459-151">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="bd459-152">Ce paramètre remplace les paramètres dans *appsettings.Development.jssur* ( `LogLevel.Debug` ) et *appsettings.Production.jssur* ( `LogLevel.Error` ) configuré par `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="bd459-152">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="bd459-153">`ConfigureLogging` peut être appelé plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="bd459-153">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="bd459-154">L’appel suivant à `ConfigureKestrel` remplace la valeur par défaut de [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) (30 000 000 octets) établie lors de la configuration de Kestrel par `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="bd459-154">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="bd459-155">L’appel suivant à [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) remplace la valeur par défaut de [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) (30 000 000 octets) établie lors de la configuration de Kestrel par `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="bd459-155">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="bd459-156">La [racine de contenu](xref:fundamentals/index#content-root) détermine l’emplacement où l’hôte recherche les fichiers de contenu, tels que les fichiers de vue MVC.</span><span class="sxs-lookup"><span data-stu-id="bd459-156">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="bd459-157">Quand l’application est démarrée à partir du dossier racine du projet, ce dossier est utilisé comme racine de contenu.</span><span class="sxs-lookup"><span data-stu-id="bd459-157">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="bd459-158">Il s’agit du dossier par défaut utilisé dans [Visual Studio](https://visualstudio.microsoft.com) et les [modèles dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="bd459-158">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="bd459-159">Pour plus d’informations sur la configuration d’application, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="bd459-159">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="bd459-160">Au lieu d’utiliser la méthode statique `CreateDefaultBuilder`, vous pouvez aussi créer un hôte à partir de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder). Cette approche est prise en charge dans ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="bd459-160">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="bd459-161">Lors de la configuration d’un hôte, les méthodes [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) et [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) peuvent être fournies.</span><span class="sxs-lookup"><span data-stu-id="bd459-161">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="bd459-162">Si une classe `Startup` est spécifiée, elle doit définir une méthode `Configure`.</span><span class="sxs-lookup"><span data-stu-id="bd459-162">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="bd459-163">Pour plus d’informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="bd459-163">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="bd459-164">Les appels multiples à `ConfigureServices` s’ajoutent les uns aux autres.</span><span class="sxs-lookup"><span data-stu-id="bd459-164">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="bd459-165">Les appels multiples à `Configure` ou `UseStartup` sur `WebHostBuilder` remplacent les paramètres précédents.</span><span class="sxs-lookup"><span data-stu-id="bd459-165">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="bd459-166">Valeurs de configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="bd459-166">Host configuration values</span></span>

<span data-ttu-id="bd459-167">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) s’appuie sur les approches suivantes pour définir les valeurs de configuration de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="bd459-167">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="bd459-168">Configuration du générateur de l’hôte, qui inclut des variables d’environnement au format `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="bd459-168">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="bd459-169">Par exemple : `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="bd459-169">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="bd459-170">Des extensions comme [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) et [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (voir la section [Remplacer la configuration](#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="bd459-170">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="bd459-171">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) et la clé associée.</span><span class="sxs-lookup"><span data-stu-id="bd459-171">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="bd459-172">Quand une valeur est définie avec `UseSetting`, elle est définie au format chaîne indépendamment du type.</span><span class="sxs-lookup"><span data-stu-id="bd459-172">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="bd459-173">L’hôte utilise l’option qui définit une valeur en dernier.</span><span class="sxs-lookup"><span data-stu-id="bd459-173">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="bd459-174">Pour plus d’informations, consultez [Remplacer une configuration](#override-configuration) dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="bd459-174">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="bd459-175">Clé d’application (Nom)</span><span class="sxs-lookup"><span data-stu-id="bd459-175">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bd459-176">La `IWebHostEnvironment.ApplicationName` propriété est définie automatiquement quand [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) ou [configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) est appelé lors de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="bd459-176">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="bd459-177">La valeur affectée correspond au nom de l’assembly contenant le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-177">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="bd459-178">Pour définir la valeur explicitement, utilisez [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey) :</span><span class="sxs-lookup"><span data-stu-id="bd459-178">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bd459-179">La propriété [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) est définie automatiquement quand [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) ou [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) est appelé pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="bd459-179">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="bd459-180">La valeur affectée correspond au nom de l’assembly contenant le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-180">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="bd459-181">Pour définir la valeur explicitement, utilisez [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey) :</span><span class="sxs-lookup"><span data-stu-id="bd459-181">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="bd459-182">**Clé** : applicationName</span><span class="sxs-lookup"><span data-stu-id="bd459-182">**Key**: applicationName</span></span>  
<span data-ttu-id="bd459-183">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="bd459-183">**Type**: *string*</span></span>  
<span data-ttu-id="bd459-184">**Par défaut** : nom de l’assembly contenant le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-184">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="bd459-185">**Définir à l’aide** de : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="bd459-185">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="bd459-186">**Variable d’environnement**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="bd459-186">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="bd459-187">Capture des erreurs de démarrage</span><span class="sxs-lookup"><span data-stu-id="bd459-187">Capture Startup Errors</span></span>

<span data-ttu-id="bd459-188">Ce paramètre contrôle la capture des erreurs de démarrage.</span><span class="sxs-lookup"><span data-stu-id="bd459-188">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="bd459-189">**Clé** : captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="bd459-189">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="bd459-190">**Type** : *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="bd459-190">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="bd459-191">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="bd459-191">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="bd459-192">**Définir à l’aide** de : `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="bd459-192">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="bd459-193">**Variable d’environnement**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="bd459-193">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="bd459-194">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="bd459-194">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="bd459-195">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="bd459-195">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="bd459-196">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="bd459-196">Content root</span></span>

<span data-ttu-id="bd459-197">Ce paramètre détermine où ASP.NET Core commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="bd459-197">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="bd459-198">**Clé** : contentRoot</span><span class="sxs-lookup"><span data-stu-id="bd459-198">**Key**: contentRoot</span></span>  
<span data-ttu-id="bd459-199">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="bd459-199">**Type**: *string*</span></span>  
<span data-ttu-id="bd459-200">**Valeur par défaut** : dossier où réside l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-200">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="bd459-201">**Définir à l’aide** de : `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="bd459-201">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="bd459-202">**Variable d’environnement**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="bd459-202">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="bd459-203">La racine du contenu est également utilisée comme chemin de base pour la [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="bd459-203">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="bd459-204">Si le chemin d’accès racine du contenu n’existe pas, le démarrage de l’hôte échoue.</span><span class="sxs-lookup"><span data-stu-id="bd459-204">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="bd459-205">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="bd459-205">For more information, see:</span></span>

* [<span data-ttu-id="bd459-206">Notions de base : racine du contenu</span><span class="sxs-lookup"><span data-stu-id="bd459-206">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="bd459-207">Racine web</span><span class="sxs-lookup"><span data-stu-id="bd459-207">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="bd459-208">Erreurs détaillées</span><span class="sxs-lookup"><span data-stu-id="bd459-208">Detailed Errors</span></span>

<span data-ttu-id="bd459-209">Détermine si les erreurs détaillées doivent être capturées.</span><span class="sxs-lookup"><span data-stu-id="bd459-209">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="bd459-210">**Clé** : detailedErrors</span><span class="sxs-lookup"><span data-stu-id="bd459-210">**Key**: detailedErrors</span></span>  
<span data-ttu-id="bd459-211">**Type** : *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="bd459-211">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="bd459-212">**Valeur par défaut**: false</span><span class="sxs-lookup"><span data-stu-id="bd459-212">**Default**: false</span></span>  
<span data-ttu-id="bd459-213">**Définir à l’aide** de : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="bd459-213">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="bd459-214">**Variable d’environnement**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="bd459-214">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="bd459-215">Quand cette fonctionnalité est activée (ou que <a href="#environment">l’environnement</a> est défini sur `Development`), l’application capture les exceptions détaillées.</span><span class="sxs-lookup"><span data-stu-id="bd459-215">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="bd459-216">Environnement</span><span class="sxs-lookup"><span data-stu-id="bd459-216">Environment</span></span>

<span data-ttu-id="bd459-217">Définit l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-217">Sets the app's environment.</span></span>

<span data-ttu-id="bd459-218">**Clé** : environment</span><span class="sxs-lookup"><span data-stu-id="bd459-218">**Key**: environment</span></span>  
<span data-ttu-id="bd459-219">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="bd459-219">**Type**: *string*</span></span>  
<span data-ttu-id="bd459-220">**Valeur par défaut** : Production</span><span class="sxs-lookup"><span data-stu-id="bd459-220">**Default**: Production</span></span>  
<span data-ttu-id="bd459-221">**Définir à l’aide** de : `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="bd459-221">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="bd459-222">**Variable d’environnement**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="bd459-222">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="bd459-223">L’environnement peut être défini à n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="bd459-223">The environment can be set to any value.</span></span> <span data-ttu-id="bd459-224">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="bd459-224">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="bd459-225">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="bd459-225">Values aren't case sensitive.</span></span> <span data-ttu-id="bd459-226">Par défaut, *l’environnement* est fourni par la variable d’environnement `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="bd459-226">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="bd459-227">Si vous utilisez [Visual Studio](https://visualstudio.microsoft.com), les variables d’environnement peuvent être définies dans le fichier *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="bd459-227">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="bd459-228">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="bd459-228">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="bd459-229">Assemblys d’hébergement au démarrage</span><span class="sxs-lookup"><span data-stu-id="bd459-229">Hosting Startup Assemblies</span></span>

<span data-ttu-id="bd459-230">Définit les assemblys d’hébergement au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-230">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="bd459-231">**Clé** : hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="bd459-231">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="bd459-232">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="bd459-232">**Type**: *string*</span></span>  
<span data-ttu-id="bd459-233">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="bd459-233">**Default**: Empty string</span></span>  
<span data-ttu-id="bd459-234">**Définir à l’aide** de : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="bd459-234">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="bd459-235">**Variable d’environnement**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="bd459-235">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="bd459-236">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="bd459-236">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="bd459-237">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-237">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="bd459-238">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="bd459-238">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="bd459-239">Port HTTPS</span><span class="sxs-lookup"><span data-stu-id="bd459-239">HTTPS Port</span></span>

<span data-ttu-id="bd459-240">Définissez le port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="bd459-240">Set the HTTPS redirect port.</span></span> <span data-ttu-id="bd459-241">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="bd459-241">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="bd459-242">**Clé** : https_port **Type** : *chaîne*
**Valeur par défaut** : une valeur par défaut n’est pas définie.</span><span class="sxs-lookup"><span data-stu-id="bd459-242">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="bd459-243">**Définir à l’aide** de : `UseSetting` 
 **variable d’environnement**:`ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="bd459-243">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="bd459-244">Assemblys d’hébergement à exclure au démarrage</span><span class="sxs-lookup"><span data-stu-id="bd459-244">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="bd459-245">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="bd459-245">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="bd459-246">**Clé** : hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="bd459-246">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="bd459-247">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="bd459-247">**Type**: *string*</span></span>  
<span data-ttu-id="bd459-248">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="bd459-248">**Default**: Empty string</span></span>  
<span data-ttu-id="bd459-249">**Définir à l’aide** de : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="bd459-249">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="bd459-250">**Variable d’environnement**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="bd459-250">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="bd459-251">URL d’hébergement préférées</span><span class="sxs-lookup"><span data-stu-id="bd459-251">Prefer Hosting URLs</span></span>

<span data-ttu-id="bd459-252">Indique si l’hôte doit écouter les URL configurées avec `WebHostBuilder` au lieu d’écouter celles configurées avec l’implémentation `IServer`.</span><span class="sxs-lookup"><span data-stu-id="bd459-252">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="bd459-253">**Clé** : preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="bd459-253">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="bd459-254">**Type** : *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="bd459-254">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="bd459-255">**Valeur par défaut**: true</span><span class="sxs-lookup"><span data-stu-id="bd459-255">**Default**: true</span></span>  
<span data-ttu-id="bd459-256">**Définir à l’aide** de : `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="bd459-256">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="bd459-257">**Variable d’environnement**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="bd459-257">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="bd459-258">Bloquer les assemblys d’hébergement au démarrage</span><span class="sxs-lookup"><span data-stu-id="bd459-258">Prevent Hosting Startup</span></span>

<span data-ttu-id="bd459-259">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-259">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="bd459-260">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="bd459-260">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="bd459-261">**Clé** : preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="bd459-261">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="bd459-262">**Type** : *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="bd459-262">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="bd459-263">**Valeur par défaut**: false</span><span class="sxs-lookup"><span data-stu-id="bd459-263">**Default**: false</span></span>  
<span data-ttu-id="bd459-264">**Définir à l’aide** de : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="bd459-264">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="bd459-265">**Variable d’environnement**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="bd459-265">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="bd459-266">URL du serveur</span><span class="sxs-lookup"><span data-stu-id="bd459-266">Server URLs</span></span>

<span data-ttu-id="bd459-267">Indique les adresses IP ou les adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="bd459-267">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="bd459-268">**Clé** : urls</span><span class="sxs-lookup"><span data-stu-id="bd459-268">**Key**: urls</span></span>  
<span data-ttu-id="bd459-269">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="bd459-269">**Type**: *string*</span></span>  
<span data-ttu-id="bd459-270">**Par défaut**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="bd459-270">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="bd459-271">**Définir à l’aide** de : `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="bd459-271">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="bd459-272">**Variable d’environnement**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="bd459-272">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="bd459-273">Liste de préfixes d’URL séparés par des points-virgules (;) correspondant aux URL auxquelles le serveur doit répondre.</span><span class="sxs-lookup"><span data-stu-id="bd459-273">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="bd459-274">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="bd459-274">For example, `http://localhost:123`.</span></span> <span data-ttu-id="bd459-275">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="bd459-275">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="bd459-276">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="bd459-276">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="bd459-277">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="bd459-277">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="bd459-278">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="bd459-278">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="bd459-279">Pour plus d’informations, consultez <xref:fundamentals/servers/kestrel/endpoints>.</span><span class="sxs-lookup"><span data-stu-id="bd459-279">For more information, see <xref:fundamentals/servers/kestrel/endpoints>.</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="bd459-280">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="bd459-280">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="bd459-281">Pour plus d’informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="bd459-281">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>
::: moniker-end

### <a name="shutdown-timeout"></a><span data-ttu-id="bd459-282">Délai d’arrêt</span><span class="sxs-lookup"><span data-stu-id="bd459-282">Shutdown Timeout</span></span>

<span data-ttu-id="bd459-283">Spécifie le délai d’attente avant l’arrêt de l’hôte web.</span><span class="sxs-lookup"><span data-stu-id="bd459-283">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="bd459-284">**Clé** : shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="bd459-284">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="bd459-285">**Type**: *int*</span><span class="sxs-lookup"><span data-stu-id="bd459-285">**Type**: *int*</span></span>  
<span data-ttu-id="bd459-286">**Valeur par défaut**: 5</span><span class="sxs-lookup"><span data-stu-id="bd459-286">**Default**: 5</span></span>  
<span data-ttu-id="bd459-287">**Définir à l’aide** de : `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="bd459-287">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="bd459-288">**Variable d’environnement**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="bd459-288">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="bd459-289">La clé accepte une valeur *int* avec `UseSetting` (par exemple, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), mais la méthode d’extension [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) prend une valeur [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="bd459-289">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="bd459-290">Pendant la période du délai d’attente, l’hébergement :</span><span class="sxs-lookup"><span data-stu-id="bd459-290">During the timeout period, hosting:</span></span>

* <span data-ttu-id="bd459-291">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="bd459-291">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="bd459-292">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="bd459-292">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="bd459-293">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="bd459-293">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="bd459-294">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="bd459-294">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="bd459-295">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="bd459-295">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="bd459-296">Assembly de démarrage</span><span class="sxs-lookup"><span data-stu-id="bd459-296">Startup Assembly</span></span>

<span data-ttu-id="bd459-297">Détermine l’assembly à rechercher pour la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="bd459-297">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="bd459-298">**Clé** : startupAssembly</span><span class="sxs-lookup"><span data-stu-id="bd459-298">**Key**: startupAssembly</span></span>  
<span data-ttu-id="bd459-299">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="bd459-299">**Type**: *string*</span></span>  
<span data-ttu-id="bd459-300">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="bd459-300">**Default**: The app's assembly</span></span>  
<span data-ttu-id="bd459-301">**Définir à l’aide** de : `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="bd459-301">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="bd459-302">**Variable d’environnement**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="bd459-302">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="bd459-303">L’assembly peut être référencé par nom (`string`) ou type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="bd459-303">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="bd459-304">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="bd459-304">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="bd459-305">Racine web</span><span class="sxs-lookup"><span data-stu-id="bd459-305">Web root</span></span>

<span data-ttu-id="bd459-306">Définit le chemin relatif des ressources statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-306">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="bd459-307">**Clé** : webroot</span><span class="sxs-lookup"><span data-stu-id="bd459-307">**Key**: webroot</span></span>  
<span data-ttu-id="bd459-308">**Type**: *chaîne*</span><span class="sxs-lookup"><span data-stu-id="bd459-308">**Type**: *string*</span></span>  
<span data-ttu-id="bd459-309">**Valeur par défaut**: la valeur par défaut est `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="bd459-309">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="bd459-310">Le chemin d’accès à *{root content}/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="bd459-310">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="bd459-311">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="bd459-311">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="bd459-312">**Définir à l’aide** de : `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="bd459-312">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="bd459-313">**Variable d’environnement**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="bd459-313">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="bd459-314">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="bd459-314">For more information, see:</span></span>

* [<span data-ttu-id="bd459-315">Notions de base : racine Web</span><span class="sxs-lookup"><span data-stu-id="bd459-315">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="bd459-316">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="bd459-316">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="bd459-317">Remplacer la configuration</span><span class="sxs-lookup"><span data-stu-id="bd459-317">Override configuration</span></span>

<span data-ttu-id="bd459-318">Utilisez [Configuration](xref:fundamentals/configuration/index) pour configurer l’hôte web.</span><span class="sxs-lookup"><span data-stu-id="bd459-318">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="bd459-319">Dans l’exemple suivant, la configuration de l’hôte est spécifiée de façon facultative dans un fichier *hostsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="bd459-319">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="bd459-320">Toute configuration chargée à partir du fichier *hostsettings.json* est remplaçable par des arguments de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="bd459-320">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="bd459-321">La configuration définie (dans `config`) est utilisée pour configurer l’hôte avec [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="bd459-321">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="bd459-322">La configuration `IWebHostBuilder` est ajoutée à la configuration de l’application, mais l’inverse n’est pas vrai &mdash;`ConfigureAppConfiguration` n’a pas d’incidence sur la configuration `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="bd459-322">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="bd459-323">La configuration fournie par `UseUrls` est d’abord remplacée par la configuration *hostsettings.json*, puis par la configuration des arguments de ligne de commande :</span><span class="sxs-lookup"><span data-stu-id="bd459-323">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="bd459-324">*hostsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="bd459-324">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="bd459-325">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) copie uniquement les clés du fourni `IConfiguration` dans la configuration du générateur de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="bd459-325">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="bd459-326">Par conséquent, le fait de définir `reloadOnChange: true` pour les fichiers de paramètres XML, JSON et INI n’a aucun effet.</span><span class="sxs-lookup"><span data-stu-id="bd459-326">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="bd459-327">Pour spécifier l’exécution de l’hôte sur une URL particulière, vous pouvez passer la valeur souhaitée à partir d’une invite de commandes lors de l’exécution de [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="bd459-327">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="bd459-328">L’argument de ligne de commande remplace la valeur `urls` du fichier *hostsettings.json*, et le serveur écoute le port 8080 :</span><span class="sxs-lookup"><span data-stu-id="bd459-328">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="bd459-329">Gérer l’hôte</span><span class="sxs-lookup"><span data-stu-id="bd459-329">Manage the host</span></span>

<span data-ttu-id="bd459-330">**Exécuter**</span><span class="sxs-lookup"><span data-stu-id="bd459-330">**Run**</span></span>

<span data-ttu-id="bd459-331">La méthode `Run` démarre l’application web et bloque le thread appelant jusqu’à l’arrêt de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="bd459-331">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="bd459-332">**Start**</span><span class="sxs-lookup"><span data-stu-id="bd459-332">**Start**</span></span>

<span data-ttu-id="bd459-333">Appelez la méthode `Start` pour exécuter l’hôte en mode non bloquant :</span><span class="sxs-lookup"><span data-stu-id="bd459-333">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="bd459-334">Si une liste d’URL est passée à la méthode `Start`, l’hôte écoute les URL spécifiées :</span><span class="sxs-lookup"><span data-stu-id="bd459-334">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="bd459-335">L’application peut initialiser et démarrer un nouvel hôte ayant les valeurs par défaut préconfigurées de `CreateDefaultBuilder` en utilisant une méthode d’usage statique.</span><span class="sxs-lookup"><span data-stu-id="bd459-335">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="bd459-336">Ces méthodes démarrent le serveur sans sortie de console et, avec [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown), elles attendent un arrêt (Ctrl-C/SIGINT ou SIGTERM) :</span><span class="sxs-lookup"><span data-stu-id="bd459-336">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="bd459-337">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="bd459-337">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="bd459-338">Commencez par un `RequestDelegate` :</span><span class="sxs-lookup"><span data-stu-id="bd459-338">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="bd459-339">Envoyez une requête à `http://localhost:5000` dans le navigateur pour recevoir la réponse « Hello World! »</span><span class="sxs-lookup"><span data-stu-id="bd459-339">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="bd459-340">`WaitForShutdown` bloque la requête jusqu’à l’émission d’une commande d’arrêt (Ctrl-C/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="bd459-340">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="bd459-341">L’application affiche le message `Console.WriteLine` et attend que l’utilisateur appuie sur une touche pour s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="bd459-341">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="bd459-342">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="bd459-342">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="bd459-343">Commencez par une URL et `RequestDelegate` :</span><span class="sxs-lookup"><span data-stu-id="bd459-343">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="bd459-344">Produit le même résultat que **Start(RequestDelegate app)**, sauf que l’application répond sur `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="bd459-344">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="bd459-345">**Start(Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="bd459-345">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="bd459-346">Utilisez une instance de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) pour utiliser le middleware de routage :</span><span class="sxs-lookup"><span data-stu-id="bd459-346">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="bd459-347">Utilisez les requêtes de navigateur suivantes avec l’exemple :</span><span class="sxs-lookup"><span data-stu-id="bd459-347">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="bd459-348">Requête</span><span class="sxs-lookup"><span data-stu-id="bd459-348">Request</span></span>                                    | <span data-ttu-id="bd459-349">response</span><span class="sxs-lookup"><span data-stu-id="bd459-349">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="bd459-350">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="bd459-350">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="bd459-351">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="bd459-351">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="bd459-352">Lève une exception avec la chaîne « ooops! »</span><span class="sxs-lookup"><span data-stu-id="bd459-352">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="bd459-353">Lève une exception avec la chaîne « Uh oh! »</span><span class="sxs-lookup"><span data-stu-id="bd459-353">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="bd459-354">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="bd459-354">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="bd459-355">Hello World!</span><span class="sxs-lookup"><span data-stu-id="bd459-355">Hello World!</span></span>                             |

<span data-ttu-id="bd459-356">`WaitForShutdown` bloque la requête jusqu’à l’émission d’une commande d’arrêt (Ctrl-C/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="bd459-356">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="bd459-357">L’application affiche le message `Console.WriteLine` et attend que l’utilisateur appuie sur une touche pour s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="bd459-357">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="bd459-358">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="bd459-358">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="bd459-359">Utilisez une URL et une instance de `IRouteBuilder` :</span><span class="sxs-lookup"><span data-stu-id="bd459-359">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="bd459-360">Produit le même résultat que **Start(Action\<IRouteBuilder> routeBuilder)**, sauf que l’application répond sur `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="bd459-360">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="bd459-361">**StartWith(Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="bd459-361">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="bd459-362">Fournissez un délégué pour configurer un `IApplicationBuilder` :</span><span class="sxs-lookup"><span data-stu-id="bd459-362">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="bd459-363">Envoyez une requête à `http://localhost:5000` dans le navigateur pour recevoir la réponse « Hello World! »</span><span class="sxs-lookup"><span data-stu-id="bd459-363">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="bd459-364">`WaitForShutdown` bloque la requête jusqu’à l’émission d’une commande d’arrêt (Ctrl-C/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="bd459-364">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="bd459-365">L’application affiche le message `Console.WriteLine` et attend que l’utilisateur appuie sur une touche pour s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="bd459-365">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="bd459-366">**StartWith(string url, Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="bd459-366">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="bd459-367">Fournissez une URL et un délégué pour configurer un `IApplicationBuilder` :</span><span class="sxs-lookup"><span data-stu-id="bd459-367">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="bd459-368">Produit le même résultat que **StartWith(Action\<IApplicationBuilder> app)**, sauf que l’application répond sur `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="bd459-368">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="bd459-369">Interface IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="bd459-369">IWebHostEnvironment interface</span></span>

<span data-ttu-id="bd459-370">L' `IWebHostEnvironment` interface fournit des informations sur l’environnement d’hébergement Web de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-370">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="bd459-371">Utilisez [l’injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir l’interface `IWebHostEnvironment` afin d’utiliser ses propriétés et méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="bd459-371">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="bd459-372">Vous pouvez utiliser une [approche basée sur une convention](xref:fundamentals/environments#environment-based-startup-class-and-methods) pour configurer l’application au démarrage en fonction de l’environnement.</span><span class="sxs-lookup"><span data-stu-id="bd459-372">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="bd459-373">Vous pouvez également injecter `IWebHostEnvironment` dans le constructeur `Startup` pour l’utiliser dans `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="bd459-373">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="bd459-374">En plus de la méthode d’extension `IsDevelopment`, `IWebHostEnvironment` fournit les méthodes `IsStaging`, `IsProduction` et `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="bd459-374">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="bd459-375">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="bd459-375">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="bd459-376">Le service `IWebHostEnvironment` peut également être injecté directement dans la méthode `Configure` pour configurer le pipeline de traitement :</span><span class="sxs-lookup"><span data-stu-id="bd459-376">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="bd459-377">`IWebHostEnvironment` peut être injecté dans la méthode `Invoke` lors de la création d’un [intergiciel (middleware)](xref:fundamentals/middleware/write) personnalisé :</span><span class="sxs-lookup"><span data-stu-id="bd459-377">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="bd459-378">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="bd459-378">IHostingEnvironment interface</span></span>

<span data-ttu-id="bd459-379">[L’interface IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) fournit des informations sur l’environnement d’hébergement web de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-379">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="bd459-380">Utilisez [l’injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir l’interface `IHostingEnvironment` afin d’utiliser ses propriétés et méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="bd459-380">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="bd459-381">Vous pouvez utiliser une [approche basée sur une convention](xref:fundamentals/environments#environment-based-startup-class-and-methods) pour configurer l’application au démarrage en fonction de l’environnement.</span><span class="sxs-lookup"><span data-stu-id="bd459-381">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="bd459-382">Vous pouvez également injecter `IHostingEnvironment` dans le constructeur `Startup` pour l’utiliser dans `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="bd459-382">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="bd459-383">En plus de la méthode d’extension `IsDevelopment`, `IHostingEnvironment` fournit les méthodes `IsStaging`, `IsProduction` et `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="bd459-383">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="bd459-384">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="bd459-384">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="bd459-385">Le service `IHostingEnvironment` peut également être injecté directement dans la méthode `Configure` pour configurer le pipeline de traitement :</span><span class="sxs-lookup"><span data-stu-id="bd459-385">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="bd459-386">`IHostingEnvironment` peut être injecté dans la méthode `Invoke` lors de la création d’un [intergiciel (middleware)](xref:fundamentals/middleware/write) personnalisé :</span><span class="sxs-lookup"><span data-stu-id="bd459-386">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="bd459-387">Interface IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="bd459-387">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="bd459-388">`IHostApplicationLifetime` permet les activités postérieures au démarrage et à l’arrêt.</span><span class="sxs-lookup"><span data-stu-id="bd459-388">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="bd459-389">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes `Action` qui définissent les événements de démarrage et d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="bd459-389">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="bd459-390">Jeton d’annulation</span><span class="sxs-lookup"><span data-stu-id="bd459-390">Cancellation Token</span></span>    | <span data-ttu-id="bd459-391">Événement déclencheur</span><span class="sxs-lookup"><span data-stu-id="bd459-391">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="bd459-392">L’hôte a complètement démarré.</span><span class="sxs-lookup"><span data-stu-id="bd459-392">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="bd459-393">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="bd459-393">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="bd459-394">Toutes les requêtes doivent être traitées.</span><span class="sxs-lookup"><span data-stu-id="bd459-394">All requests should be processed.</span></span> <span data-ttu-id="bd459-395">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="bd459-395">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="bd459-396">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="bd459-396">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="bd459-397">Des requêtes peuvent encore être en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="bd459-397">Requests may still be processing.</span></span> <span data-ttu-id="bd459-398">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="bd459-398">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="bd459-399">`StopApplication` demande l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-399">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="bd459-400">La classe suivante utilise `StopApplication` pour arrêter normalement une application lors de l’appel de la méthode `Shutdown` de la classe :</span><span class="sxs-lookup"><span data-stu-id="bd459-400">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
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

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="bd459-401">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="bd459-401">IApplicationLifetime interface</span></span>

<span data-ttu-id="bd459-402">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) s’utilise pour les opérations post-démarrage et arrêt.</span><span class="sxs-lookup"><span data-stu-id="bd459-402">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="bd459-403">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes `Action` qui définissent les événements de démarrage et d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="bd459-403">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="bd459-404">Jeton d’annulation</span><span class="sxs-lookup"><span data-stu-id="bd459-404">Cancellation Token</span></span>    | <span data-ttu-id="bd459-405">Événement déclencheur</span><span class="sxs-lookup"><span data-stu-id="bd459-405">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="bd459-406">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="bd459-406">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="bd459-407">L’hôte a complètement démarré.</span><span class="sxs-lookup"><span data-stu-id="bd459-407">The host has fully started.</span></span> |
| [<span data-ttu-id="bd459-408">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="bd459-408">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="bd459-409">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="bd459-409">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="bd459-410">Toutes les requêtes doivent être traitées.</span><span class="sxs-lookup"><span data-stu-id="bd459-410">All requests should be processed.</span></span> <span data-ttu-id="bd459-411">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="bd459-411">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="bd459-412">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="bd459-412">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="bd459-413">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="bd459-413">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="bd459-414">Des requêtes peuvent encore être en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="bd459-414">Requests may still be processing.</span></span> <span data-ttu-id="bd459-415">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="bd459-415">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="bd459-416">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requête l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="bd459-416">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="bd459-417">La classe suivante utilise `StopApplication` pour arrêter normalement une application lors de l’appel de la méthode `Shutdown` de la classe :</span><span class="sxs-lookup"><span data-stu-id="bd459-417">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="bd459-418">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="bd459-418">Scope validation</span></span>

<span data-ttu-id="bd459-419">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) affecte la valeur `true` à [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) si l’environnement de l’application est Développement.</span><span class="sxs-lookup"><span data-stu-id="bd459-419">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="bd459-420">Quand `ValidateScopes` est défini sur `true`, le fournisseur de services par défaut vérifie que :</span><span class="sxs-lookup"><span data-stu-id="bd459-420">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="bd459-421">Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="bd459-421">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="bd459-422">Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.</span><span class="sxs-lookup"><span data-stu-id="bd459-422">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="bd459-423">Le fournisseur de services racine est créé quand [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) est appelé.</span><span class="sxs-lookup"><span data-stu-id="bd459-423">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="bd459-424">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application/du serveur quand le fournisseur démarre avec l’application et qu’il est supprimé quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="bd459-424">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="bd459-425">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="bd459-425">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="bd459-426">Si un service Scoped est créé dans le conteneur racine, la durée de vie du service est promue en singleton, car elle est supprimée par le conteneur racine seulement quand l’application/le serveur est arrêté.</span><span class="sxs-lookup"><span data-stu-id="bd459-426">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="bd459-427">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="bd459-427">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="bd459-428">Pour toujours valider les étendues, notamment dans l’environnement de production, configurez [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) avec [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) sur le créateur d’hôte :</span><span class="sxs-lookup"><span data-stu-id="bd459-428">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="bd459-429">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="bd459-429">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
