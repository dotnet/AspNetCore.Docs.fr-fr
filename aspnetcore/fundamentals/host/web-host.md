---
title: Hôte web ASP.NET Core
author: rick-anderson
description: Découvrez l’hôte web dans ASP.NET Core, qui est responsable de la gestion du démarrage et de la durée de vie des applications.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/host/web-host
ms.openlocfilehash: 09383cb9067d7fdc2d7b69213b741e7ae823e9ea
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060012"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="35cc8-103">Hôte web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35cc8-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="35cc8-104">ASP.NET Core les applications configurent et lancent un *hôte* .</span><span class="sxs-lookup"><span data-stu-id="35cc8-104">ASP.NET Core apps configure and launch a *host* .</span></span> <span data-ttu-id="35cc8-105">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="35cc8-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="35cc8-106">Au minimum, l’hôte configure un serveur ainsi qu’un pipeline de traitement des requêtes.</span><span class="sxs-lookup"><span data-stu-id="35cc8-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="35cc8-107">L’hôte peut aussi configurer la journalisation, l’injection de dépendances et la configuration.</span><span class="sxs-lookup"><span data-stu-id="35cc8-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="35cc8-108">Cet article traite de l’hôte Web, qui reste disponible uniquement pour la compatibilité descendante.</span><span class="sxs-lookup"><span data-stu-id="35cc8-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="35cc8-109">L’[Hôte générique](xref:fundamentals/host/generic-host) est recommandé pour tous les types d’applications.</span><span class="sxs-lookup"><span data-stu-id="35cc8-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="35cc8-110">Cet article traite de l’hôte web qui est responsable de l’hébergement des applications web.</span><span class="sxs-lookup"><span data-stu-id="35cc8-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="35cc8-111">Pour d’autres types d’applications, utilisez l’[Hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="35cc8-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="35cc8-112">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="35cc8-112">Set up a host</span></span>

<span data-ttu-id="35cc8-113">Créez un hôte en utilisant une instance de [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="35cc8-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="35cc8-114">Cette opération est généralement effectuée au point d’entrée de l’application, à savoir la méthode `Main`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="35cc8-115">Dans les modèles de projet, `Main` se trouve dans *Program.cs* .</span><span class="sxs-lookup"><span data-stu-id="35cc8-115">In the project templates, `Main` is located in *Program.cs* .</span></span> <span data-ttu-id="35cc8-116">Une application standard appelle [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) pour lancer la configuration d’un hôte :</span><span class="sxs-lookup"><span data-stu-id="35cc8-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="35cc8-117">Le code qui appelle `CreateDefaultBuilder` est dans une méthode nommée `CreateWebHostBuilder`, qui le sépare du code dans `Main` qui appelle `Run` sur l’objet du générateur.</span><span class="sxs-lookup"><span data-stu-id="35cc8-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="35cc8-118">Cette séparation est requise si vous utilisez [les outils Entity Framework Core](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="35cc8-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="35cc8-119">Les outils s’attendent à trouver une méthode `CreateWebHostBuilder` qu’ils peuvent appeler au moment du design pour configurer l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="35cc8-120">Une alternative consiste à implémenter `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="35cc8-121">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="35cc8-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="35cc8-122">`CreateDefaultBuilder` effectue les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="35cc8-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="35cc8-123">Configure le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="35cc8-124">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="35cc8-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="35cc8-125">Définit la [racine du contenu](xref:fundamentals/index#content-root) sur le chemin d’accès retourné par [Directory. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="35cc8-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="35cc8-126">Charge la [configuration de l’hôte](#host-configuration-values) à partir de :</span><span class="sxs-lookup"><span data-stu-id="35cc8-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="35cc8-127">Variables d’environnement comportant le préfixe `ASPNETCORE_` (par exemple, `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="35cc8-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="35cc8-128">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="35cc8-128">Command-line arguments.</span></span>
* <span data-ttu-id="35cc8-129">Charge la configuration de l’application dans l’ordre suivant à partir des éléments ci-après :</span><span class="sxs-lookup"><span data-stu-id="35cc8-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="35cc8-130">*:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="35cc8-130">*:::no-loc(appsettings.json):::* .</span></span>
  * <span data-ttu-id="35cc8-131">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="35cc8-131">*appsettings.{Environment}.json* .</span></span>
  * <span data-ttu-id="35cc8-132">L’outil [Secret Manager (Gestionnaire de secrets)](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development` à l’aide de l’assembly d’entrée.</span><span class="sxs-lookup"><span data-stu-id="35cc8-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="35cc8-133">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="35cc8-133">Environment variables.</span></span>
  * <span data-ttu-id="35cc8-134">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="35cc8-134">Command-line arguments.</span></span>
* <span data-ttu-id="35cc8-135">Configure la [journalisation](xref:fundamentals/logging/index) des sorties de la console et du débogage.</span><span class="sxs-lookup"><span data-stu-id="35cc8-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="35cc8-136">La journalisation comprend les règles de [filtrage de journal](xref:fundamentals/logging/index#log-filtering) spécifiées dans une section de configuration de journalisation d’un *:::no-loc(appsettings.json):::* ou *appSettings. { Fichier Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="35cc8-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *:::no-loc(appsettings.json):::* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="35cc8-137">Lors de l’exécution derrière IIS avec le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` active l' [intégration IIS](xref:host-and-deploy/iis/index), qui configure l’adresse et le port de base de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="35cc8-138">L’intégration IIS configure également l’application pour la [capture des erreurs de démarrage](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="35cc8-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="35cc8-139">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="35cc8-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="35cc8-140">Définissez [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) sur `true` si l’environnement de l’application est Développement.</span><span class="sxs-lookup"><span data-stu-id="35cc8-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="35cc8-141">Pour plus d’informations, consultez [Validation de l’étendue](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="35cc8-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="35cc8-142">La configuration définie par `CreateDefaultBuilder` peut être remplacée et enrichie par [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) et les autres méthodes et les méthodes d’extension de [ IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="35cc8-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="35cc8-143">En voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="35cc8-143">A few examples follow:</span></span>

* <span data-ttu-id="35cc8-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) permet de spécifier une configuration `IConfiguration` supplémentaire pour l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="35cc8-145">L’appel `ConfigureAppConfiguration` suivant ajoute un délégué pour inclure la configuration de l’application dans le fichier *appsettings.xml* .</span><span class="sxs-lookup"><span data-stu-id="35cc8-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="35cc8-146">`ConfigureAppConfiguration` peut être appelé plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="35cc8-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="35cc8-147">Notez que cette configuration ne s’applique pas à l’hôte (par exemple, les URL de serveur ou l’environnement).</span><span class="sxs-lookup"><span data-stu-id="35cc8-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="35cc8-148">Voir la section [Valeurs de configuration de l’hôte](#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="35cc8-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="35cc8-149">L’appel `ConfigureLogging` suivant ajoute un délégué pour configurer le niveau de journalisation minimal ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) sur [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="35cc8-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="35cc8-150">Ce paramètre remplace les paramètres dans *appsettings.Development.jssur* ( `LogLevel.Debug` ) et *appsettings.Production.jssur* ( `LogLevel.Error` ) configuré par `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="35cc8-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="35cc8-151">`ConfigureLogging` peut être appelé plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="35cc8-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="35cc8-152">L’appel suivant à `ConfigureKestrel` remplace la valeur par défaut de [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) (30 000 000 octets) établie lors de la configuration de Kestrel par `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="35cc8-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="35cc8-153">L’appel suivant à [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) remplace la valeur par défaut de [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) (30 000 000 octets) établie lors de la configuration de Kestrel par `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="35cc8-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="35cc8-154">La [racine de contenu](xref:fundamentals/index#content-root) détermine l’emplacement où l’hôte recherche les fichiers de contenu, tels que les fichiers de vue MVC.</span><span class="sxs-lookup"><span data-stu-id="35cc8-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="35cc8-155">Quand l’application est démarrée à partir du dossier racine du projet, ce dossier est utilisé comme racine de contenu.</span><span class="sxs-lookup"><span data-stu-id="35cc8-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="35cc8-156">Il s’agit du dossier par défaut utilisé dans [Visual Studio](https://visualstudio.microsoft.com) et les [modèles dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="35cc8-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="35cc8-157">Pour plus d’informations sur la configuration d’application, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="35cc8-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="35cc8-158">Au lieu d’utiliser la méthode statique `CreateDefaultBuilder`, vous pouvez aussi créer un hôte à partir de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder). Cette approche est prise en charge dans ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="35cc8-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="35cc8-159">Lors de la configuration d’un hôte, les méthodes [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) et [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) peuvent être fournies.</span><span class="sxs-lookup"><span data-stu-id="35cc8-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="35cc8-160">Si une classe `Startup` est spécifiée, elle doit définir une méthode `Configure`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="35cc8-161">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="35cc8-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="35cc8-162">Les appels multiples à `ConfigureServices` s’ajoutent les uns aux autres.</span><span class="sxs-lookup"><span data-stu-id="35cc8-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="35cc8-163">Les appels multiples à `Configure` ou `UseStartup` sur `WebHostBuilder` remplacent les paramètres précédents.</span><span class="sxs-lookup"><span data-stu-id="35cc8-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="35cc8-164">Valeurs de configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="35cc8-164">Host configuration values</span></span>

<span data-ttu-id="35cc8-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) s’appuie sur les approches suivantes pour définir les valeurs de configuration de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="35cc8-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="35cc8-166">Configuration du générateur de l’hôte, qui inclut des variables d’environnement au format `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="35cc8-167">Par exemple : `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="35cc8-168">Des extensions comme [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) et [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (voir la section [Remplacer la configuration](#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="35cc8-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="35cc8-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) et la clé associée.</span><span class="sxs-lookup"><span data-stu-id="35cc8-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="35cc8-170">Quand une valeur est définie avec `UseSetting`, elle est définie au format chaîne indépendamment du type.</span><span class="sxs-lookup"><span data-stu-id="35cc8-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="35cc8-171">L’hôte utilise l’option qui définit une valeur en dernier.</span><span class="sxs-lookup"><span data-stu-id="35cc8-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="35cc8-172">Pour plus d’informations, consultez [Remplacer une configuration](#override-configuration) dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="35cc8-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="35cc8-173">Clé d’application (Nom)</span><span class="sxs-lookup"><span data-stu-id="35cc8-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="35cc8-174">La `IWebHostEnvironment.ApplicationName` propriété est définie automatiquement quand [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) ou [configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) est appelé lors de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="35cc8-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="35cc8-175">La valeur affectée correspond au nom de l’assembly contenant le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="35cc8-176">Pour définir la valeur explicitement, utilisez [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey) :</span><span class="sxs-lookup"><span data-stu-id="35cc8-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="35cc8-177">La propriété [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) est définie automatiquement quand [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) ou [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) est appelé pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="35cc8-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="35cc8-178">La valeur affectée correspond au nom de l’assembly contenant le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="35cc8-179">Pour définir la valeur explicitement, utilisez [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey) :</span><span class="sxs-lookup"><span data-stu-id="35cc8-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="35cc8-180">**Clé**  : applicationName</span><span class="sxs-lookup"><span data-stu-id="35cc8-180">**Key** : applicationName</span></span>  
<span data-ttu-id="35cc8-181">**Type** : *chaîne*</span><span class="sxs-lookup"><span data-stu-id="35cc8-181">**Type** : *string*</span></span>  
<span data-ttu-id="35cc8-182">**Par défaut**  : nom de l’assembly contenant le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-182">**Default** : The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="35cc8-183">**Définir à l’aide** de : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="35cc8-183">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="35cc8-184">**Variable d’environnement** : `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="35cc8-184">**Environment variable** : `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="35cc8-185">Capture des erreurs de démarrage</span><span class="sxs-lookup"><span data-stu-id="35cc8-185">Capture Startup Errors</span></span>

<span data-ttu-id="35cc8-186">Ce paramètre contrôle la capture des erreurs de démarrage.</span><span class="sxs-lookup"><span data-stu-id="35cc8-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="35cc8-187">**Clé** : captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="35cc8-187">**Key** : captureStartupErrors</span></span>  
<span data-ttu-id="35cc8-188">**Type** : *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="35cc8-188">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="35cc8-189">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="35cc8-189">**Default** : Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="35cc8-190">**Définir à l’aide** de : `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="35cc8-190">**Set using** : `CaptureStartupErrors`</span></span>  
<span data-ttu-id="35cc8-191">**Variable d’environnement** : `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="35cc8-191">**Environment variable** : `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="35cc8-192">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="35cc8-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="35cc8-193">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="35cc8-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="35cc8-194">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="35cc8-194">Content root</span></span>

<span data-ttu-id="35cc8-195">Ce paramètre détermine où ASP.NET Core commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="35cc8-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="35cc8-196">**Clé** : contentRoot</span><span class="sxs-lookup"><span data-stu-id="35cc8-196">**Key** : contentRoot</span></span>  
<span data-ttu-id="35cc8-197">**Type** : *chaîne*</span><span class="sxs-lookup"><span data-stu-id="35cc8-197">**Type** : *string*</span></span>  
<span data-ttu-id="35cc8-198">**Valeur par défaut** : dossier où réside l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-198">**Default** : Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="35cc8-199">**Définir à l’aide** de : `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="35cc8-199">**Set using** : `UseContentRoot`</span></span>  
<span data-ttu-id="35cc8-200">**Variable d’environnement** : `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="35cc8-200">**Environment variable** : `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="35cc8-201">La racine du contenu est également utilisée comme chemin de base pour la [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="35cc8-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="35cc8-202">Si le chemin d’accès racine du contenu n’existe pas, le démarrage de l’hôte échoue.</span><span class="sxs-lookup"><span data-stu-id="35cc8-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="35cc8-203">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="35cc8-203">For more information, see:</span></span>

* [<span data-ttu-id="35cc8-204">Notions de base : racine du contenu</span><span class="sxs-lookup"><span data-stu-id="35cc8-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="35cc8-205">Racine web</span><span class="sxs-lookup"><span data-stu-id="35cc8-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="35cc8-206">Erreurs détaillées</span><span class="sxs-lookup"><span data-stu-id="35cc8-206">Detailed Errors</span></span>

<span data-ttu-id="35cc8-207">Détermine si les erreurs détaillées doivent être capturées.</span><span class="sxs-lookup"><span data-stu-id="35cc8-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="35cc8-208">**Clé** : detailedErrors</span><span class="sxs-lookup"><span data-stu-id="35cc8-208">**Key** : detailedErrors</span></span>  
<span data-ttu-id="35cc8-209">**Type** : *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="35cc8-209">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="35cc8-210">**Valeur par défaut** : false</span><span class="sxs-lookup"><span data-stu-id="35cc8-210">**Default** : false</span></span>  
<span data-ttu-id="35cc8-211">**Définir à l’aide** de : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="35cc8-211">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="35cc8-212">**Variable d’environnement** : `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="35cc8-212">**Environment variable** : `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="35cc8-213">Quand cette fonctionnalité est activée (ou que <a href="#environment">l’environnement</a> est défini sur `Development`), l’application capture les exceptions détaillées.</span><span class="sxs-lookup"><span data-stu-id="35cc8-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="35cc8-214">Environnement</span><span class="sxs-lookup"><span data-stu-id="35cc8-214">Environment</span></span>

<span data-ttu-id="35cc8-215">Définit l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-215">Sets the app's environment.</span></span>

<span data-ttu-id="35cc8-216">**Clé** : environment</span><span class="sxs-lookup"><span data-stu-id="35cc8-216">**Key** : environment</span></span>  
<span data-ttu-id="35cc8-217">**Type** : *chaîne*</span><span class="sxs-lookup"><span data-stu-id="35cc8-217">**Type** : *string*</span></span>  
<span data-ttu-id="35cc8-218">**Valeur par défaut** : Production</span><span class="sxs-lookup"><span data-stu-id="35cc8-218">**Default** : Production</span></span>  
<span data-ttu-id="35cc8-219">**Définir à l’aide** de : `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="35cc8-219">**Set using** : `UseEnvironment`</span></span>  
<span data-ttu-id="35cc8-220">**Variable d’environnement** : `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="35cc8-220">**Environment variable** : `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="35cc8-221">L’environnement peut être défini à n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="35cc8-221">The environment can be set to any value.</span></span> <span data-ttu-id="35cc8-222">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="35cc8-223">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="35cc8-223">Values aren't case sensitive.</span></span> <span data-ttu-id="35cc8-224">Par défaut, *l’environnement* est fourni par la variable d’environnement `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="35cc8-225">Si vous utilisez [Visual Studio](https://visualstudio.microsoft.com), les variables d’environnement peuvent être définies dans le fichier *launchSettings.json* .</span><span class="sxs-lookup"><span data-stu-id="35cc8-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="35cc8-226">Pour plus d'informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="35cc8-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="35cc8-227">Assemblys d’hébergement au démarrage</span><span class="sxs-lookup"><span data-stu-id="35cc8-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="35cc8-228">Définit les assemblys d’hébergement au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="35cc8-229">**Clé** : hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="35cc8-229">**Key** : hostingStartupAssemblies</span></span>  
<span data-ttu-id="35cc8-230">**Type** : *chaîne*</span><span class="sxs-lookup"><span data-stu-id="35cc8-230">**Type** : *string*</span></span>  
<span data-ttu-id="35cc8-231">**Valeur par défaut** : chaîne vide</span><span class="sxs-lookup"><span data-stu-id="35cc8-231">**Default** : Empty string</span></span>  
<span data-ttu-id="35cc8-232">**Définir à l’aide** de : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="35cc8-232">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="35cc8-233">**Variable d’environnement** : `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="35cc8-233">**Environment variable** : `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="35cc8-234">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="35cc8-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="35cc8-235">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="35cc8-236">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="35cc8-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="35cc8-237">Port HTTPS</span><span class="sxs-lookup"><span data-stu-id="35cc8-237">HTTPS Port</span></span>

<span data-ttu-id="35cc8-238">Définissez le port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="35cc8-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="35cc8-239">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="35cc8-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="35cc8-240">**Clé** : https_port **Type** : *chaîne*
**Valeur par défaut** : une valeur par défaut n’est pas définie.</span><span class="sxs-lookup"><span data-stu-id="35cc8-240">**Key** : https_port **Type** : *string*
**Default** : A default value isn't set.</span></span>
<span data-ttu-id="35cc8-241">**Définir à l’aide** de : `UseSetting` 
 **variable d’environnement** :`ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="35cc8-241">**Set using** : `UseSetting`
**Environment variable** : `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="35cc8-242">Assemblys d’hébergement à exclure au démarrage</span><span class="sxs-lookup"><span data-stu-id="35cc8-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="35cc8-243">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="35cc8-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="35cc8-244">**Clé** : hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="35cc8-244">**Key** : hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="35cc8-245">**Type** : *chaîne*</span><span class="sxs-lookup"><span data-stu-id="35cc8-245">**Type** : *string*</span></span>  
<span data-ttu-id="35cc8-246">**Valeur par défaut** : chaîne vide</span><span class="sxs-lookup"><span data-stu-id="35cc8-246">**Default** : Empty string</span></span>  
<span data-ttu-id="35cc8-247">**Définir à l’aide** de : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="35cc8-247">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="35cc8-248">**Variable d’environnement** : `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="35cc8-248">**Environment variable** : `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="35cc8-249">URL d’hébergement préférées</span><span class="sxs-lookup"><span data-stu-id="35cc8-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="35cc8-250">Indique si l’hôte doit écouter les URL configurées avec `WebHostBuilder` au lieu d’écouter celles configurées avec l’implémentation `IServer`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="35cc8-251">**Clé** : preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="35cc8-251">**Key** : preferHostingUrls</span></span>  
<span data-ttu-id="35cc8-252">**Type** : *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="35cc8-252">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="35cc8-253">**Valeur par défaut** : true</span><span class="sxs-lookup"><span data-stu-id="35cc8-253">**Default** : true</span></span>  
<span data-ttu-id="35cc8-254">**Définir à l’aide** de : `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="35cc8-254">**Set using** : `PreferHostingUrls`</span></span>  
<span data-ttu-id="35cc8-255">**Variable d’environnement** : `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="35cc8-255">**Environment variable** : `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="35cc8-256">Bloquer les assemblys d’hébergement au démarrage</span><span class="sxs-lookup"><span data-stu-id="35cc8-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="35cc8-257">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="35cc8-258">Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="35cc8-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="35cc8-259">**Clé** : preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="35cc8-259">**Key** : preventHostingStartup</span></span>  
<span data-ttu-id="35cc8-260">**Type** : *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="35cc8-260">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="35cc8-261">**Valeur par défaut** : false</span><span class="sxs-lookup"><span data-stu-id="35cc8-261">**Default** : false</span></span>  
<span data-ttu-id="35cc8-262">**Définir à l’aide** de : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="35cc8-262">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="35cc8-263">**Variable d’environnement** : `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="35cc8-263">**Environment variable** : `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="35cc8-264">URL du serveur</span><span class="sxs-lookup"><span data-stu-id="35cc8-264">Server URLs</span></span>

<span data-ttu-id="35cc8-265">Indique les adresses IP ou les adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="35cc8-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="35cc8-266">**Clé** : urls</span><span class="sxs-lookup"><span data-stu-id="35cc8-266">**Key** : urls</span></span>  
<span data-ttu-id="35cc8-267">**Type** : *chaîne*</span><span class="sxs-lookup"><span data-stu-id="35cc8-267">**Type** : *string*</span></span>  
<span data-ttu-id="35cc8-268">**Par défaut** : http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="35cc8-268">**Default** : http://localhost:5000</span></span>  
<span data-ttu-id="35cc8-269">**Définir à l’aide** de : `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="35cc8-269">**Set using** : `UseUrls`</span></span>  
<span data-ttu-id="35cc8-270">**Variable d’environnement** : `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="35cc8-270">**Environment variable** : `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="35cc8-271">Liste de préfixes d’URL séparés par des points-virgules (;) correspondant aux URL auxquelles le serveur doit répondre.</span><span class="sxs-lookup"><span data-stu-id="35cc8-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="35cc8-272">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="35cc8-273">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="35cc8-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="35cc8-274">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="35cc8-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="35cc8-275">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="35cc8-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="35cc8-276">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="35cc8-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="35cc8-277">Pour plus d'informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="35cc8-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="35cc8-278">Délai d’arrêt</span><span class="sxs-lookup"><span data-stu-id="35cc8-278">Shutdown Timeout</span></span>

<span data-ttu-id="35cc8-279">Spécifie le délai d’attente avant l’arrêt de l’hôte web.</span><span class="sxs-lookup"><span data-stu-id="35cc8-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="35cc8-280">**Clé** : shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="35cc8-280">**Key** : shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="35cc8-281">**Type** : *int*</span><span class="sxs-lookup"><span data-stu-id="35cc8-281">**Type** : *int*</span></span>  
<span data-ttu-id="35cc8-282">**Valeur par défaut** : 5</span><span class="sxs-lookup"><span data-stu-id="35cc8-282">**Default** : 5</span></span>  
<span data-ttu-id="35cc8-283">**Définir à l’aide** de : `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="35cc8-283">**Set using** : `UseShutdownTimeout`</span></span>  
<span data-ttu-id="35cc8-284">**Variable d’environnement** : `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="35cc8-284">**Environment variable** : `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="35cc8-285">La clé accepte une valeur *int* avec `UseSetting` (par exemple, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), mais la méthode d’extension [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) prend une valeur [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="35cc8-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="35cc8-286">Pendant la période du délai d’attente, l’hébergement :</span><span class="sxs-lookup"><span data-stu-id="35cc8-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="35cc8-287">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="35cc8-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="35cc8-288">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="35cc8-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="35cc8-289">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="35cc8-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="35cc8-290">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="35cc8-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="35cc8-291">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="35cc8-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="35cc8-292">Assembly de démarrage</span><span class="sxs-lookup"><span data-stu-id="35cc8-292">Startup Assembly</span></span>

<span data-ttu-id="35cc8-293">Détermine l’assembly à rechercher pour la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="35cc8-294">**Clé** : startupAssembly</span><span class="sxs-lookup"><span data-stu-id="35cc8-294">**Key** : startupAssembly</span></span>  
<span data-ttu-id="35cc8-295">**Type** : *chaîne*</span><span class="sxs-lookup"><span data-stu-id="35cc8-295">**Type** : *string*</span></span>  
<span data-ttu-id="35cc8-296">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="35cc8-296">**Default** : The app's assembly</span></span>  
<span data-ttu-id="35cc8-297">**Définir à l’aide** de : `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="35cc8-297">**Set using** : `UseStartup`</span></span>  
<span data-ttu-id="35cc8-298">**Variable d’environnement** : `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="35cc8-298">**Environment variable** : `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="35cc8-299">L’assembly peut être référencé par nom (`string`) ou type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="35cc8-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="35cc8-300">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="35cc8-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="35cc8-301">Racine web</span><span class="sxs-lookup"><span data-stu-id="35cc8-301">Web root</span></span>

<span data-ttu-id="35cc8-302">Définit le chemin relatif des ressources statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="35cc8-303">**Clé** : webroot</span><span class="sxs-lookup"><span data-stu-id="35cc8-303">**Key** : webroot</span></span>  
<span data-ttu-id="35cc8-304">**Type** : *chaîne*</span><span class="sxs-lookup"><span data-stu-id="35cc8-304">**Type** : *string*</span></span>  
<span data-ttu-id="35cc8-305">**Valeur par défaut** : la valeur par défaut est `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="35cc8-305">**Default** : The default is `wwwroot`.</span></span> <span data-ttu-id="35cc8-306">Le chemin d’accès à *{root content}/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="35cc8-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="35cc8-307">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="35cc8-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="35cc8-308">**Définir à l’aide** de : `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="35cc8-308">**Set using** : `UseWebRoot`</span></span>  
<span data-ttu-id="35cc8-309">**Variable d’environnement** : `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="35cc8-309">**Environment variable** : `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="35cc8-310">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="35cc8-310">For more information, see:</span></span>

* [<span data-ttu-id="35cc8-311">Notions de base : racine Web</span><span class="sxs-lookup"><span data-stu-id="35cc8-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="35cc8-312">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="35cc8-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="35cc8-313">Remplacer la configuration</span><span class="sxs-lookup"><span data-stu-id="35cc8-313">Override configuration</span></span>

<span data-ttu-id="35cc8-314">Utilisez [Configuration](xref:fundamentals/configuration/index) pour configurer l’hôte web.</span><span class="sxs-lookup"><span data-stu-id="35cc8-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="35cc8-315">Dans l’exemple suivant, la configuration de l’hôte est spécifiée de façon facultative dans un fichier *hostsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="35cc8-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="35cc8-316">Toute configuration chargée à partir du fichier *hostsettings.json* est remplaçable par des arguments de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="35cc8-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="35cc8-317">La configuration définie (dans `config`) est utilisée pour configurer l’hôte avec [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="35cc8-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="35cc8-318">La configuration `IWebHostBuilder` est ajoutée à la configuration de l’application, mais l’inverse n’est pas vrai &mdash;`ConfigureAppConfiguration` n’a pas d’incidence sur la configuration `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="35cc8-319">La configuration fournie par `UseUrls` est d’abord remplacée par la configuration *hostsettings.json* , puis par la configuration des arguments de ligne de commande :</span><span class="sxs-lookup"><span data-stu-id="35cc8-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="35cc8-320">*hostsettings.json*  :</span><span class="sxs-lookup"><span data-stu-id="35cc8-320">*hostsettings.json* :</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="35cc8-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) copie uniquement les clés du fourni `IConfiguration` dans la configuration du générateur de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="35cc8-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="35cc8-322">Par conséquent, le fait de définir `reloadOnChange: true` pour les fichiers de paramètres XML, JSON et INI n’a aucun effet.</span><span class="sxs-lookup"><span data-stu-id="35cc8-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="35cc8-323">Pour spécifier l’exécution de l’hôte sur une URL particulière, vous pouvez passer la valeur souhaitée à partir d’une invite de commandes lors de l’exécution de [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="35cc8-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="35cc8-324">L’argument de ligne de commande remplace la valeur `urls` du fichier *hostsettings.json* , et le serveur écoute le port 8080 :</span><span class="sxs-lookup"><span data-stu-id="35cc8-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="35cc8-325">Gérer l’hôte</span><span class="sxs-lookup"><span data-stu-id="35cc8-325">Manage the host</span></span>

<span data-ttu-id="35cc8-326">**Exécuter**</span><span class="sxs-lookup"><span data-stu-id="35cc8-326">**Run**</span></span>

<span data-ttu-id="35cc8-327">La méthode `Run` démarre l’application web et bloque le thread appelant jusqu’à l’arrêt de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="35cc8-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="35cc8-328">**Start**</span><span class="sxs-lookup"><span data-stu-id="35cc8-328">**Start**</span></span>

<span data-ttu-id="35cc8-329">Appelez la méthode `Start` pour exécuter l’hôte en mode non bloquant :</span><span class="sxs-lookup"><span data-stu-id="35cc8-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="35cc8-330">Si une liste d’URL est passée à la méthode `Start`, l’hôte écoute les URL spécifiées :</span><span class="sxs-lookup"><span data-stu-id="35cc8-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="35cc8-331">L’application peut initialiser et démarrer un nouvel hôte ayant les valeurs par défaut préconfigurées de `CreateDefaultBuilder` en utilisant une méthode d’usage statique.</span><span class="sxs-lookup"><span data-stu-id="35cc8-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="35cc8-332">Ces méthodes démarrent le serveur sans sortie de console et, avec [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown), elles attendent un arrêt (Ctrl-C/SIGINT ou SIGTERM) :</span><span class="sxs-lookup"><span data-stu-id="35cc8-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="35cc8-333">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="35cc8-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="35cc8-334">Commencez par un `RequestDelegate` :</span><span class="sxs-lookup"><span data-stu-id="35cc8-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="35cc8-335">Envoyez une requête à `http://localhost:5000` dans le navigateur pour recevoir la réponse « Hello World! »</span><span class="sxs-lookup"><span data-stu-id="35cc8-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="35cc8-336">`WaitForShutdown` bloque la requête jusqu’à l’émission d’une commande d’arrêt (Ctrl-C/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="35cc8-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="35cc8-337">L’application affiche le message `Console.WriteLine` et attend que l’utilisateur appuie sur une touche pour s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="35cc8-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="35cc8-338">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="35cc8-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="35cc8-339">Commencez par une URL et `RequestDelegate` :</span><span class="sxs-lookup"><span data-stu-id="35cc8-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="35cc8-340">Produit le même résultat que **Start(RequestDelegate app)** , sauf que l’application répond sur `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-340">Produces the same result as **Start(RequestDelegate app)** , except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="35cc8-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="35cc8-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="35cc8-342">Utilisez une instance de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) pour utiliser le middleware de routage :</span><span class="sxs-lookup"><span data-stu-id="35cc8-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="35cc8-343">Utilisez les requêtes de navigateur suivantes avec l’exemple :</span><span class="sxs-lookup"><span data-stu-id="35cc8-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="35cc8-344">Requête</span><span class="sxs-lookup"><span data-stu-id="35cc8-344">Request</span></span>                                    | <span data-ttu-id="35cc8-345">response</span><span class="sxs-lookup"><span data-stu-id="35cc8-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="35cc8-346">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="35cc8-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="35cc8-347">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="35cc8-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="35cc8-348">Lève une exception avec la chaîne « ooops! »</span><span class="sxs-lookup"><span data-stu-id="35cc8-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="35cc8-349">Lève une exception avec la chaîne « Uh oh! »</span><span class="sxs-lookup"><span data-stu-id="35cc8-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="35cc8-350">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="35cc8-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="35cc8-351">Hello World!</span><span class="sxs-lookup"><span data-stu-id="35cc8-351">Hello World!</span></span>                             |

<span data-ttu-id="35cc8-352">`WaitForShutdown` bloque la requête jusqu’à l’émission d’une commande d’arrêt (Ctrl-C/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="35cc8-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="35cc8-353">L’application affiche le message `Console.WriteLine` et attend que l’utilisateur appuie sur une touche pour s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="35cc8-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="35cc8-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="35cc8-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="35cc8-355">Utilisez une URL et une instance de `IRouteBuilder` :</span><span class="sxs-lookup"><span data-stu-id="35cc8-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="35cc8-356">Produit le même résultat que **Start(Action\<IRouteBuilder> routeBuilder)** , sauf que l’application répond sur `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)** , except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="35cc8-357">**StartWith(Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="35cc8-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="35cc8-358">Fournissez un délégué pour configurer un `IApplicationBuilder` :</span><span class="sxs-lookup"><span data-stu-id="35cc8-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="35cc8-359">Envoyez une requête à `http://localhost:5000` dans le navigateur pour recevoir la réponse « Hello World! »</span><span class="sxs-lookup"><span data-stu-id="35cc8-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="35cc8-360">`WaitForShutdown` bloque la requête jusqu’à l’émission d’une commande d’arrêt (Ctrl-C/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="35cc8-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="35cc8-361">L’application affiche le message `Console.WriteLine` et attend que l’utilisateur appuie sur une touche pour s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="35cc8-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="35cc8-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="35cc8-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="35cc8-363">Fournissez une URL et un délégué pour configurer un `IApplicationBuilder` :</span><span class="sxs-lookup"><span data-stu-id="35cc8-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="35cc8-364">Produit le même résultat que **StartWith(Action\<IApplicationBuilder> app)** , sauf que l’application répond sur `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)** , except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="35cc8-365">Interface IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="35cc8-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="35cc8-366">L' `IWebHostEnvironment` interface fournit des informations sur l’environnement d’hébergement Web de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="35cc8-367">Utilisez [l’injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir l’interface `IWebHostEnvironment` afin d’utiliser ses propriétés et méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="35cc8-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="35cc8-368">Vous pouvez utiliser une [approche basée sur une convention](xref:fundamentals/environments#environment-based-startup-class-and-methods) pour configurer l’application au démarrage en fonction de l’environnement.</span><span class="sxs-lookup"><span data-stu-id="35cc8-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="35cc8-369">Vous pouvez également injecter `IWebHostEnvironment` dans le constructeur `Startup` pour l’utiliser dans `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="35cc8-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="35cc8-370">En plus de la méthode d’extension `IsDevelopment`, `IWebHostEnvironment` fournit les méthodes `IsStaging`, `IsProduction` et `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="35cc8-371">Pour plus d'informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="35cc8-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="35cc8-372">Le service `IWebHostEnvironment` peut également être injecté directement dans la méthode `Configure` pour configurer le pipeline de traitement :</span><span class="sxs-lookup"><span data-stu-id="35cc8-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="35cc8-373">`IWebHostEnvironment` peut être injecté dans la méthode `Invoke` lors de la création d’un [intergiciel (middleware)](xref:fundamentals/middleware/write) personnalisé :</span><span class="sxs-lookup"><span data-stu-id="35cc8-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="35cc8-374">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="35cc8-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="35cc8-375">[L’interface IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) fournit des informations sur l’environnement d’hébergement web de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="35cc8-376">Utilisez [l’injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir l’interface `IHostingEnvironment` afin d’utiliser ses propriétés et méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="35cc8-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="35cc8-377">Vous pouvez utiliser une [approche basée sur une convention](xref:fundamentals/environments#environment-based-startup-class-and-methods) pour configurer l’application au démarrage en fonction de l’environnement.</span><span class="sxs-lookup"><span data-stu-id="35cc8-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="35cc8-378">Vous pouvez également injecter `IHostingEnvironment` dans le constructeur `Startup` pour l’utiliser dans `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="35cc8-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="35cc8-379">En plus de la méthode d’extension `IsDevelopment`, `IHostingEnvironment` fournit les méthodes `IsStaging`, `IsProduction` et `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="35cc8-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="35cc8-380">Pour plus d'informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="35cc8-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="35cc8-381">Le service `IHostingEnvironment` peut également être injecté directement dans la méthode `Configure` pour configurer le pipeline de traitement :</span><span class="sxs-lookup"><span data-stu-id="35cc8-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="35cc8-382">`IHostingEnvironment` peut être injecté dans la méthode `Invoke` lors de la création d’un [intergiciel (middleware)](xref:fundamentals/middleware/write) personnalisé :</span><span class="sxs-lookup"><span data-stu-id="35cc8-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="35cc8-383">Interface IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="35cc8-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="35cc8-384">`IHostApplicationLifetime` permet les activités postérieures au démarrage et à l’arrêt.</span><span class="sxs-lookup"><span data-stu-id="35cc8-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="35cc8-385">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes `Action` qui définissent les événements de démarrage et d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="35cc8-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="35cc8-386">Jeton d’annulation</span><span class="sxs-lookup"><span data-stu-id="35cc8-386">Cancellation Token</span></span>    | <span data-ttu-id="35cc8-387">Événement déclencheur</span><span class="sxs-lookup"><span data-stu-id="35cc8-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="35cc8-388">L’hôte a complètement démarré.</span><span class="sxs-lookup"><span data-stu-id="35cc8-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="35cc8-389">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="35cc8-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="35cc8-390">Toutes les requêtes doivent être traitées.</span><span class="sxs-lookup"><span data-stu-id="35cc8-390">All requests should be processed.</span></span> <span data-ttu-id="35cc8-391">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="35cc8-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="35cc8-392">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="35cc8-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="35cc8-393">Des requêtes peuvent encore être en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="35cc8-393">Requests may still be processing.</span></span> <span data-ttu-id="35cc8-394">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="35cc8-394">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="35cc8-395">`StopApplication` demande l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="35cc8-396">La classe suivante utilise `StopApplication` pour arrêter normalement une application lors de l’appel de la méthode `Shutdown` de la classe :</span><span class="sxs-lookup"><span data-stu-id="35cc8-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="35cc8-397">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="35cc8-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="35cc8-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) s’utilise pour les opérations post-démarrage et arrêt.</span><span class="sxs-lookup"><span data-stu-id="35cc8-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="35cc8-399">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes `Action` qui définissent les événements de démarrage et d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="35cc8-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="35cc8-400">Jeton d’annulation</span><span class="sxs-lookup"><span data-stu-id="35cc8-400">Cancellation Token</span></span>    | <span data-ttu-id="35cc8-401">Événement déclencheur</span><span class="sxs-lookup"><span data-stu-id="35cc8-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="35cc8-402">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="35cc8-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="35cc8-403">L’hôte a complètement démarré.</span><span class="sxs-lookup"><span data-stu-id="35cc8-403">The host has fully started.</span></span> |
| [<span data-ttu-id="35cc8-404">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="35cc8-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="35cc8-405">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="35cc8-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="35cc8-406">Toutes les requêtes doivent être traitées.</span><span class="sxs-lookup"><span data-stu-id="35cc8-406">All requests should be processed.</span></span> <span data-ttu-id="35cc8-407">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="35cc8-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="35cc8-408">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="35cc8-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="35cc8-409">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="35cc8-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="35cc8-410">Des requêtes peuvent encore être en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="35cc8-410">Requests may still be processing.</span></span> <span data-ttu-id="35cc8-411">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="35cc8-411">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="35cc8-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requête l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="35cc8-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="35cc8-413">La classe suivante utilise `StopApplication` pour arrêter normalement une application lors de l’appel de la méthode `Shutdown` de la classe :</span><span class="sxs-lookup"><span data-stu-id="35cc8-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="35cc8-414">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="35cc8-414">Scope validation</span></span>

<span data-ttu-id="35cc8-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) affecte la valeur `true` à [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) si l’environnement de l’application est Développement.</span><span class="sxs-lookup"><span data-stu-id="35cc8-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="35cc8-416">Quand `ValidateScopes` est défini sur `true`, le fournisseur de services par défaut vérifie que :</span><span class="sxs-lookup"><span data-stu-id="35cc8-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="35cc8-417">Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="35cc8-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="35cc8-418">Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.</span><span class="sxs-lookup"><span data-stu-id="35cc8-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="35cc8-419">Le fournisseur de services racine est créé quand [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) est appelé.</span><span class="sxs-lookup"><span data-stu-id="35cc8-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="35cc8-420">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application/du serveur quand le fournisseur démarre avec l’application et qu’il est supprimé quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="35cc8-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="35cc8-421">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="35cc8-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="35cc8-422">Si un service Scoped est créé dans le conteneur racine, la durée de vie du service est promue en singleton, car elle est supprimée par le conteneur racine seulement quand l’application/le serveur est arrêté.</span><span class="sxs-lookup"><span data-stu-id="35cc8-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="35cc8-423">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="35cc8-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="35cc8-424">Pour toujours valider les étendues, notamment dans l’environnement de production, configurez [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) avec [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) sur le créateur d’hôte :</span><span class="sxs-lookup"><span data-stu-id="35cc8-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="35cc8-425">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="35cc8-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
