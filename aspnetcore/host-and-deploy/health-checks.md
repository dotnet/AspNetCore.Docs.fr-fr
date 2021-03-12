---
title: Contrôles d’intégrité dans ASP.NET Core
author: rick-anderson
description: Découvrez comment configurer des contrôles d’intégrité pour l’infrastructure ASP.NET Core, comme des applications ou des bases de données.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 06/22/2020
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
uid: host-and-deploy/health-checks
ms.openlocfilehash: 272f1f098ca90434f26d6c057859a00b5519602e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588904"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="a0059-103">Contrôles d’intégrité dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0059-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="a0059-104">Par [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="a0059-104">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a0059-105">ASP.NET Core offre un intergiciel et des bibliothèques de contrôle d’intégrité pour signaler l’intégrité des composants d’infrastructure d’application.</span><span class="sxs-lookup"><span data-stu-id="a0059-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="a0059-106">Les contrôles d’intégrité sont exposés par une application comme des points de terminaison HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0059-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="a0059-107">Les points de terminaison de vérification d’intégrité peuvent être configurés pour de nombreux scénarios de supervision en temps réel :</span><span class="sxs-lookup"><span data-stu-id="a0059-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="a0059-108">Les sondes d’intégrité peuvent être utilisées par les orchestrateurs de conteneurs et les équilibreurs de charge afin de vérifier l’état d’une application.</span><span class="sxs-lookup"><span data-stu-id="a0059-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="a0059-109">Par exemple, un orchestrateur de conteneurs peut répondre à un résultat de non intégrité en arrêtant le déploiement ou en redémarrant un conteneur.</span><span class="sxs-lookup"><span data-stu-id="a0059-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="a0059-110">Face à une application non saine, l’équilibreur de charge peut réagir en redirigeant le trafic vers une instance saine.</span><span class="sxs-lookup"><span data-stu-id="a0059-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="a0059-111">L’utilisation de la mémoire, des disques et des autres ressources de serveur physique peut être supervisée dans le cadre d’un contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="a0059-112">Les contrôles d’intégrité peuvent tester les dépendances d’une application, telles que les bases de données et les points de terminaison de service externes, dans le but de vérifier leur disponibilité et leur bon fonctionnement.</span><span class="sxs-lookup"><span data-stu-id="a0059-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="a0059-113">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a0059-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a0059-114">L’exemple d’application comprend des exemples pour les scénarios décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="a0059-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="a0059-115">Pour exécuter l’exemple d’application selon un scénario donné, utilisez la commande [dotnet run](/dotnet/core/tools/dotnet-run) dans un interpréteur de commandes, à partir du dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="a0059-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="a0059-116">Pour plus d’informations sur l’utilisation de l’exemple d’application, consultez le fichier *README.md* de l’exemple d’application ainsi que les descriptions de scénarios de cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="a0059-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a0059-117">Prérequis</span><span class="sxs-lookup"><span data-stu-id="a0059-117">Prerequisites</span></span>

<span data-ttu-id="a0059-118">Les contrôles d’intégrité sont généralement utilisés avec un service de supervision externe ou un orchestrateur de conteneurs pour vérifier l’état d’une application.</span><span class="sxs-lookup"><span data-stu-id="a0059-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="a0059-119">Avant d’ajouter des contrôles d’intégrité à une application, vous devez décider du système de supervision à utiliser.</span><span class="sxs-lookup"><span data-stu-id="a0059-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="a0059-120">Le système de supervision détermine les types de contrôles d’intégrité qui doivent être créés ainsi que la configuration de leurs points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="a0059-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="a0059-121">Le package [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) est référencé implicitement pour les applications ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="a0059-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="a0059-122">Pour effectuer des contrôles d’intégrité à l’aide de Entity Framework Core, ajoutez une référence de package au package [Microsoft. extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="a0059-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="a0059-123">L’exemple d’application fournit du code de démarrage pour illustrer les contrôles d’intégrité de plusieurs scénarios.</span><span class="sxs-lookup"><span data-stu-id="a0059-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="a0059-124">Le scénario [probe de la base de données](#database-probe) vérifie l’intégrité d’une connexion de base de données à l’aide d’[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="a0059-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="a0059-125">Le scénario [Sondage DbContext](#entity-framework-core-dbcontext-probe) vérifie une base de données à l’aide d’un `DbContext` EF Core.</span><span class="sxs-lookup"><span data-stu-id="a0059-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="a0059-126">Pour explorer les scénarios de base de données, l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="a0059-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="a0059-127">Crée une base de données et fournit sa chaîne de connexion dans le *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="a0059-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="a0059-128">Possède les références de package suivantes dans son fichier de projet :</span><span class="sxs-lookup"><span data-stu-id="a0059-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="a0059-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="a0059-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="a0059-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="a0059-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="a0059-131">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a0059-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="a0059-132">Un autre scénario de contrôle d’intégrité montre comment filtrer des contrôles d’intégrité selon un port de gestion.</span><span class="sxs-lookup"><span data-stu-id="a0059-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="a0059-133">L’exemple d’application vous oblige à créer un fichier *Properties/launchSettings.json* comprenant l’URL de gestion et le port de gestion.</span><span class="sxs-lookup"><span data-stu-id="a0059-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="a0059-134">Pour plus d’informations, consultez la section [Filtrer par port](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="a0059-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="a0059-135">Sondage d’intégrité de base</span><span class="sxs-lookup"><span data-stu-id="a0059-135">Basic health probe</span></span>

<span data-ttu-id="a0059-136">Pour de nombreuses applications, un sondage d’intégrité de base qui signale la disponibilité d’une application pour le traitement des requêtes (*liveness*) suffit à découvrir l’état de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0059-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="a0059-137">La configuration de base inscrit les services de contrôle d’intégrité et appelle l’intergiciel (middleware) des contrôles d’intégrité pour répondre à un point de terminaison d’URL avec une réponse d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="a0059-138">Par défaut, aucun contrôle d’intégrité n’est inscrit pour tester les dépendances ou le sous-système.</span><span class="sxs-lookup"><span data-stu-id="a0059-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="a0059-139">L’application est considérée comme saine si elle est capable de répondre à l’URL de point de terminaison de contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="a0059-140">L’enregistreur de réponse par défaut écrit l’état (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) sous forme de texte en clair qu’il renvoie au client, indiquant si l’état est [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) ou [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="a0059-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="a0059-141">Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a0059-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a0059-142">Créez un point de terminaison de contrôle d’intégrité en appelant `MapHealthChecks` dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a0059-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="a0059-143">Dans l’exemple d’application, le point de terminaison de contrôle d’intégrité est créé au niveau de `/health` (*BasicStartup.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="a0059-144">Pour exécuter le scénario de configuration de base à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0059-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="a0059-145">Exemple Docker</span><span class="sxs-lookup"><span data-stu-id="a0059-145">Docker example</span></span>

<span data-ttu-id="a0059-146">[Docker](xref:host-and-deploy/docker/index) fournit une directive `HEALTHCHECK` intégrée qui peut être utilisée pour vérifier l’état d’une application utilisant la configuration de contrôle d’intégrité de base :</span><span class="sxs-lookup"><span data-stu-id="a0059-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="a0059-147">Créer des contrôles d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-147">Create health checks</span></span>

<span data-ttu-id="a0059-148">Les contrôles d’intégrité sont créés via l’implémentation de l’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>.</span><span class="sxs-lookup"><span data-stu-id="a0059-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="a0059-149">La méthode <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> retourne un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> qui indique l’état d’intégrité comme étant `Healthy`, `Degraded` ou `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="a0059-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="a0059-150">Le résultat est écrit sous la forme de texte en clair avec un code d’état configurable (la configuration est décrite dans la section [Options de contrôle d’intégrité](#health-check-options)).</span><span class="sxs-lookup"><span data-stu-id="a0059-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="a0059-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> peut également retourner des paires clé-valeur facultatives.</span><span class="sxs-lookup"><span data-stu-id="a0059-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="a0059-152">La `ExampleHealthCheck` classe suivante illustre la disposition d’un contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="a0059-153">La logique des contrôles d’intégrité est placée dans la `CheckHealthAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="a0059-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="a0059-154">L’exemple suivant définit une variable factice, `healthCheckResultHealthy` , sur `true` .</span><span class="sxs-lookup"><span data-stu-id="a0059-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="a0059-155">Si la valeur de `healthCheckResultHealthy` est définie sur `false` , l’état [HealthCheckResult. inhealth](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) est retourné.</span><span class="sxs-lookup"><span data-stu-id="a0059-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="a0059-156">Inscrire les services de contrôle d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-156">Register health check services</span></span>

<span data-ttu-id="a0059-157">Le `ExampleHealthCheck` type est ajouté aux services de contrôle d’intégrité avec <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a0059-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="a0059-158">La surcharge <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> de l’exemple suivant définit l’état d’échec (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) de manière à être signalé lorsque le contrôle d’intégrité signale un échec.</span><span class="sxs-lookup"><span data-stu-id="a0059-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="a0059-159">Si l’état d’échec est défini sur `null` (par défaut), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) est signalé.</span><span class="sxs-lookup"><span data-stu-id="a0059-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="a0059-160">Cette surcharge est utile pour les créateurs de bibliothèque, dans les cas où l’état d’échec indiqué par la bibliothèque est appliqué par l’application lorsqu’un échec est signalé par le contrôle d’intégrité, si l’implémentation de ce dernier respecte le paramètre.</span><span class="sxs-lookup"><span data-stu-id="a0059-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="a0059-161">Des *étiquettes* peuvent être utilisées pour filtrer les contrôles d’intégrité (cette procédure est décrite plus loin dans la section [Filtrer les contrôles d’intégrité](#filter-health-checks)).</span><span class="sxs-lookup"><span data-stu-id="a0059-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="a0059-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> peut également exécuter une fonction lambda.</span><span class="sxs-lookup"><span data-stu-id="a0059-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="a0059-163">Dans l’exemple suivant, le nom du contrôle d’intégrité est spécifié en tant que `Example`, et la vérification retourne toujours un état sain :</span><span class="sxs-lookup"><span data-stu-id="a0059-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="a0059-164">Appelez <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> pour passer des arguments à une implémentation de contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="a0059-165">Dans l’exemple suivant, `TestHealthCheckWithArgs` accepte un entier et une chaîne à utiliser lorsque <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> est appelé :</span><span class="sxs-lookup"><span data-stu-id="a0059-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

<span data-ttu-id="a0059-166">`TestHealthCheckWithArgs` est inscrit en appelant `AddTypeActivatedCheck` avec l’entier et la chaîne passés à l’implémentation :</span><span class="sxs-lookup"><span data-stu-id="a0059-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="a0059-167">Utiliser le routage des contrôles d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-167">Use Health Checks Routing</span></span>

<span data-ttu-id="a0059-168">Dans `Startup.Configure` , appelez `MapHealthChecks` sur le générateur de points de terminaison avec l’URL de point de terminaison ou le chemin d’accès relatif :</span><span class="sxs-lookup"><span data-stu-id="a0059-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="a0059-169">Exiger un hôte</span><span class="sxs-lookup"><span data-stu-id="a0059-169">Require host</span></span>

<span data-ttu-id="a0059-170">Appelez `RequireHost` pour spécifier un ou plusieurs hôtes autorisés pour le point de terminaison de contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="a0059-171">Les hôtes doivent être au format Unicode plutôt que Punycode et peuvent inclure un port.</span><span class="sxs-lookup"><span data-stu-id="a0059-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="a0059-172">Si une collection n’est pas fournie, tous les hôtes sont acceptés.</span><span class="sxs-lookup"><span data-stu-id="a0059-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="a0059-173">Pour plus d’informations, consultez la section [Filtrer par port](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="a0059-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="a0059-174">Exiger une autorisation</span><span class="sxs-lookup"><span data-stu-id="a0059-174">Require authorization</span></span>

<span data-ttu-id="a0059-175">Appelez `RequireAuthorization` pour exécuter l’intergiciel (middleware) d’autorisation sur le point de terminaison de demande de contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="a0059-176">Une `RequireAuthorization` surcharge accepte une ou plusieurs stratégies d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="a0059-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="a0059-177">Si aucune stratégie n’est fournie, la stratégie d’autorisation par défaut est utilisée.</span><span class="sxs-lookup"><span data-stu-id="a0059-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="a0059-178">Activer les requêtes d’origines différentes</span><span class="sxs-lookup"><span data-stu-id="a0059-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="a0059-179">Bien que l’exécution manuelle de contrôles d’intégrité à partir d’un navigateur ne soit pas un scénario d’utilisation courant, l’intergiciel (middleware) CORS peut être activé en appelant `RequireCors` sur des points de terminaison de contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="a0059-180">Une `RequireCors` surcharge accepte un délégué de générateur de stratégie cors ( `CorsPolicyBuilder` ) ou un nom de stratégie.</span><span class="sxs-lookup"><span data-stu-id="a0059-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="a0059-181">Si aucune stratégie n’est fournie, la stratégie CORS par défaut est utilisée.</span><span class="sxs-lookup"><span data-stu-id="a0059-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="a0059-182">Pour plus d’informations, consultez <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="a0059-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="a0059-183">Options de contrôle d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-183">Health check options</span></span>

<span data-ttu-id="a0059-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> permet de personnaliser le comportement du contrôle d’intégrité :</span><span class="sxs-lookup"><span data-stu-id="a0059-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="a0059-185">Filtrer les contrôles d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="a0059-186">Personnaliser le code d’état HTTP</span><span class="sxs-lookup"><span data-stu-id="a0059-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="a0059-187">Supprimer les en-têtes de cache</span><span class="sxs-lookup"><span data-stu-id="a0059-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="a0059-188">Personnaliser la sortie</span><span class="sxs-lookup"><span data-stu-id="a0059-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="a0059-189">Filtrer les contrôles d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-189">Filter health checks</span></span>

<span data-ttu-id="a0059-190">Par défaut, l’intergiciel (middleware) des contrôles d’intégrité exécute toutes les vérifications d’intégrité inscrites.</span><span class="sxs-lookup"><span data-stu-id="a0059-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="a0059-191">Pour exécuter un sous-ensemble de contrôles d’intégrité, fournissez une fonction qui retourne une valeur booléenne à l’option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>.</span><span class="sxs-lookup"><span data-stu-id="a0059-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="a0059-192">Dans l’exemple suivant, le contrôle d’intégrité `Bar` est filtré en fonction de son étiquette (`bar_tag`) dans l’instruction conditionnelle de la fonction, où `true` est retourné uniquement si la propriété <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> du contrôle d’intégrité correspond à `foo_tag` ou à `baz_tag` :</span><span class="sxs-lookup"><span data-stu-id="a0059-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="a0059-193">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a0059-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="a0059-194">Dans `Startup.Configure` , le `Predicate` filtre le contrôle d’intégrité « bar ».</span><span class="sxs-lookup"><span data-stu-id="a0059-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="a0059-195">Seules les opérations foo et Baz sont exécutées.</span><span class="sxs-lookup"><span data-stu-id="a0059-195">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="a0059-196">Personnaliser le code d’état HTTP</span><span class="sxs-lookup"><span data-stu-id="a0059-196">Customize the HTTP status code</span></span>

<span data-ttu-id="a0059-197">Utilisez <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> pour personnaliser le mappage de l’état d’intégrité aux codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0059-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="a0059-198">Les affectations <xref:Microsoft.AspNetCore.Http.StatusCodes> suivantes sont les valeurs par défaut utilisées par le middleware.</span><span class="sxs-lookup"><span data-stu-id="a0059-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="a0059-199">Vous pouvez modifier les valeurs de code d’état selon vos besoins.</span><span class="sxs-lookup"><span data-stu-id="a0059-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="a0059-200">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a0059-200">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="a0059-201">Supprimer les en-têtes de cache</span><span class="sxs-lookup"><span data-stu-id="a0059-201">Suppress cache headers</span></span>

<span data-ttu-id="a0059-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> contrôle si l’intergiciel (middleware) de contrôles d’intégrité ajoute des en-têtes HTTP à une réponse de sondage pour empêcher la mise en cache des réponses.</span><span class="sxs-lookup"><span data-stu-id="a0059-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="a0059-203">Si la valeur est `false` (par défaut), le middleware définit ou substitue les en-têtes `Cache-Control`, `Expires` et `Pragma` afin d’empêcher la mise en cache de la réponse.</span><span class="sxs-lookup"><span data-stu-id="a0059-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="a0059-204">Si la valeur est `true`, le middleware ne modifie pas les en-têtes de cache de la réponse.</span><span class="sxs-lookup"><span data-stu-id="a0059-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="a0059-205">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a0059-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="a0059-206">Personnaliser la sortie</span><span class="sxs-lookup"><span data-stu-id="a0059-206">Customize output</span></span>

<span data-ttu-id="a0059-207">Dans `Startup.Configure` , définissez l’option [HealthCheckOptions. ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) sur un délégué pour l’écriture de la réponse :</span><span class="sxs-lookup"><span data-stu-id="a0059-207">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="a0059-208">Le délégué par défaut écrit une réponse minimale constituée de texte en clair, avec la valeur de chaîne [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="a0059-208">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="a0059-209">Les délégués personnalisés suivants génèrent une réponse JSON personnalisée.</span><span class="sxs-lookup"><span data-stu-id="a0059-209">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="a0059-210">Le premier exemple de l’exemple d’application montre comment utiliser <xref:System.Text.Json?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="a0059-210">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="a0059-211">Le deuxième exemple montre comment utiliser [Newtonsoft.Jssur](https://www.nuget.org/packages/Newtonsoft.Json/):</span><span class="sxs-lookup"><span data-stu-id="a0059-211">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="a0059-212">Dans l’exemple d’application, commentez la `SYSTEM_TEXT_JSON` [directive de préprocesseur](xref:index#preprocessor-directives-in-sample-code) dans *CustomWriterStartup.cs* pour activer la `Newtonsoft.Json` version de `WriteResponse` .</span><span class="sxs-lookup"><span data-stu-id="a0059-212">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="a0059-213">L’API contrôles d’intégrité ne fournit pas de prise en charge intégrée des formats de retour JSON complexes, car le format est spécifique à votre choix de système de surveillance.</span><span class="sxs-lookup"><span data-stu-id="a0059-213">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="a0059-214">Personnalisez la réponse dans les exemples précédents en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="a0059-214">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="a0059-215">Pour plus d’informations sur la sérialisation JSON avec `System.Text.Json` , consultez [sérialisation et désérialisation de JSON dans .net](/dotnet/standard/serialization/system-text-json-how-to).</span><span class="sxs-lookup"><span data-stu-id="a0059-215">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="a0059-216">Sondage de base de données</span><span class="sxs-lookup"><span data-stu-id="a0059-216">Database probe</span></span>

<span data-ttu-id="a0059-217">Un contrôle d’intégrité peut spécifier une requête de base de données à exécuter en tant que test booléen pour indiquer si la base de données répond normalement.</span><span class="sxs-lookup"><span data-stu-id="a0059-217">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="a0059-218">L’exemple d’application utilise [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), une bibliothèque de vérification d’intégrité pour les applications ASP.NET Core, pour effectuer une vérification d’intégrité sur une base de données SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a0059-218">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="a0059-219">`AspNetCore.Diagnostics.HealthChecks` exécute une demande `SELECT 1` sur la base de données pour confirmer que la connexion à la base de données est saine.</span><span class="sxs-lookup"><span data-stu-id="a0059-219">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="a0059-220">Lorsque vous vérifiez une connexion de base de données à l’aide d’une requête, choisissez une requête qui est retournée rapidement.</span><span class="sxs-lookup"><span data-stu-id="a0059-220">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="a0059-221">L’utilisation d’une requête risque néanmoins d’entraîner une surcharge de la base de données et d’en diminuer les performances.</span><span class="sxs-lookup"><span data-stu-id="a0059-221">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="a0059-222">Dans la plupart des cas, il n’est pas nécessaire d’utiliser une requête de test.</span><span class="sxs-lookup"><span data-stu-id="a0059-222">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="a0059-223">Il suffit simplement d’établir une connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="a0059-223">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="a0059-224">Si vous avez besoin d’exécuter une requête, choisissez une requête SELECT simple, telle que `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="a0059-224">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="a0059-225">Inclure une référence de package à [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="a0059-225">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="a0059-226">Fournissez une chaîne de connexion de base de données valide dans le *appsettings.json* fichier de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="a0059-226">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="a0059-227">L’application utilise une base de données SQL Server nommée `HealthCheckSample` :</span><span class="sxs-lookup"><span data-stu-id="a0059-227">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="a0059-228">Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a0059-228">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a0059-229">L’exemple d’application appelle la méthode `AddSqlServer` avec la chaîne de connexion de la base de données (*DbHealthStartup.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-229">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a0059-230">Pour créer un point de terminaison de contrôle d’intégrité, appelez `MapHealthChecks` dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a0059-230">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="a0059-231">Pour exécuter le scénario de sondage d’une base de données à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0059-231">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="a0059-232">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a0059-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="a0059-233">Sondage DbContext Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a0059-233">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="a0059-234">La vérification `DbContext` vérifie que l’application peut communiquer avec la base de données configurée pour un `DbContext` EF Core.</span><span class="sxs-lookup"><span data-stu-id="a0059-234">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="a0059-235">La vérification `DbContext` est prise en charge dans les applications qui :</span><span class="sxs-lookup"><span data-stu-id="a0059-235">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="a0059-236">Utilisent [Entity Framework (EF) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="a0059-236">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="a0059-237">Incluent une référence de package à [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="a0059-237">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="a0059-238">`AddDbContextCheck<TContext>` inscrit un contrôle d’intégrité pour un `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="a0059-238">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="a0059-239">Le `DbContext` est fourni en tant que `TContext` à la méthode.</span><span class="sxs-lookup"><span data-stu-id="a0059-239">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="a0059-240">Une surcharge est disponible pour configurer l’état d’échec, les étiquettes et une requête de test personnalisée.</span><span class="sxs-lookup"><span data-stu-id="a0059-240">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="a0059-241">Par défaut :</span><span class="sxs-lookup"><span data-stu-id="a0059-241">By default:</span></span>

* <span data-ttu-id="a0059-242">`DbContextHealthCheck` appelle la méthode `CanConnectAsync` d’EF Core.</span><span class="sxs-lookup"><span data-stu-id="a0059-242">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="a0059-243">Vous pouvez choisir quelle opération doit être exécutée lors du contrôle d’intégrité à l’aide des surcharges de la méthode `AddDbContextCheck`.</span><span class="sxs-lookup"><span data-stu-id="a0059-243">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="a0059-244">Le nom du contrôle d’intégrité correspond à celui du type `TContext`.</span><span class="sxs-lookup"><span data-stu-id="a0059-244">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="a0059-245">Dans l’exemple d’application, `AppDbContext` est fourni à `AddDbContextCheck` et enregistré en tant que service dans `Startup.ConfigureServices` (*DbContextHealthStartup.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-245">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a0059-246">Pour créer un point de terminaison de contrôle d’intégrité, appelez `MapHealthChecks` dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a0059-246">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="a0059-247">Pour exécuter le scénario de sondage `DbContext` à l’aide de l’exemple d’application, vérifiez que la base de données spécifiée par le la chaîne de connexion ne se trouve pas déjà dans l’instance SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a0059-247">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="a0059-248">Si la base de données existe, supprimez-la.</span><span class="sxs-lookup"><span data-stu-id="a0059-248">If the database exists, delete it.</span></span>

<span data-ttu-id="a0059-249">Exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0059-249">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="a0059-250">Une fois que l’application est en cours d’exécution, vérifiez l’état d’intégrité en envoyant une requête au point de terminaison `/health` dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="a0059-250">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="a0059-251">La base de données et `AppDbContext` n’existent pas, donc l’application fournit la réponse suivante :</span><span class="sxs-lookup"><span data-stu-id="a0059-251">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="a0059-252">Déclenchez l’exemple d’application pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="a0059-252">Trigger the sample app to create the database.</span></span> <span data-ttu-id="a0059-253">Envoyez une requête à `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="a0059-253">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="a0059-254">L’application répond :</span><span class="sxs-lookup"><span data-stu-id="a0059-254">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="a0059-255">Envoyez une requête au point de terminaison `/health`.</span><span class="sxs-lookup"><span data-stu-id="a0059-255">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="a0059-256">La base de données et le contexte existent, donc l’application répond :</span><span class="sxs-lookup"><span data-stu-id="a0059-256">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="a0059-257">Déclenchez l’exemple d’application pour supprimer la base de données.</span><span class="sxs-lookup"><span data-stu-id="a0059-257">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="a0059-258">Envoyez une requête à `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="a0059-258">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="a0059-259">L’application répond :</span><span class="sxs-lookup"><span data-stu-id="a0059-259">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="a0059-260">Envoyez une requête au point de terminaison `/health`.</span><span class="sxs-lookup"><span data-stu-id="a0059-260">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="a0059-261">L’application fournit une réponse non intégrité :</span><span class="sxs-lookup"><span data-stu-id="a0059-261">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="a0059-262">Séparer les sondages probe readiness et probe liveness</span><span class="sxs-lookup"><span data-stu-id="a0059-262">Separate readiness and liveness probes</span></span>

<span data-ttu-id="a0059-263">Dans certains scénarios d’hébergement, une paire de contrôles d’intégrité est utilisée pour distinguer deux états d’application :</span><span class="sxs-lookup"><span data-stu-id="a0059-263">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="a0059-264">L’état de *préparation* indique si l’application s’exécute normalement mais n’est pas prête à recevoir des demandes.</span><span class="sxs-lookup"><span data-stu-id="a0059-264">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="a0059-265">L' *activité* indique si une application s’est arrêtée et doit être redémarrée.</span><span class="sxs-lookup"><span data-stu-id="a0059-265">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="a0059-266">Prenons l’exemple suivant : une application doit télécharger un fichier de configuration volumineux avant de pouvoir traiter les demandes.</span><span class="sxs-lookup"><span data-stu-id="a0059-266">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="a0059-267">Nous ne voulons pas que l’application soit redémarrée si le téléchargement initial échoue parce que l’application peut réessayer de télécharger le fichier plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="a0059-267">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="a0059-268">Nous utilisons une *sonde d’activité* pour décrire l’activité du processus, aucune vérification supplémentaire n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="a0059-268">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="a0059-269">Nous voulons également empêcher l’envoi de demandes à l’application avant la réussite du téléchargement du fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0059-269">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="a0059-270">Nous utilisons une *sonde de préparation* pour indiquer un État « non prêt » jusqu’à ce que le téléchargement aboutisse et que l’application soit prête à recevoir des demandes.</span><span class="sxs-lookup"><span data-stu-id="a0059-270">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="a0059-271">L’exemple d’application contient un contrôle d’intégrité permettant de signaler l’achèvement d’une tâche de démarrage de longue durée dans un [service hébergé](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="a0059-271">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="a0059-272">`StartupHostedServiceHealthCheck` expose la propriété `StartupTaskCompleted`, que le service hébergé peut définir sur `true` lorsque sa tâche de longue durée est terminée (*StartupHostedServiceHealthCheck.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-272">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="a0059-273">La tâche de longue durée en arrière-plan est démarrée par un [service hébergé](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="a0059-273">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="a0059-274">À la fin de la tâche, `StartupHostedServiceHealthCheck.StartupTaskCompleted` est défini sur `true` :</span><span class="sxs-lookup"><span data-stu-id="a0059-274">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="a0059-275">Le contrôle d’intégrité est inscrit auprès de <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> dans `Startup.ConfigureServices` en même temps que le service hébergé.</span><span class="sxs-lookup"><span data-stu-id="a0059-275">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="a0059-276">Étant donné que le service hébergé doit définir la propriété sur le contrôle d’intégrité, le contrôle d’intégrité est également inscrit dans le conteneur du service (*LivenessProbeStartup.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-276">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a0059-277">Un point de terminaison de contrôle d’intégrité est créé en appelant `MapHealthChecks` dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a0059-277">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="a0059-278">Dans l’exemple d’application, les points de terminaison de contrôle d’intégrité sont créés à l’adresse :</span><span class="sxs-lookup"><span data-stu-id="a0059-278">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="a0059-279">`/health/ready` pour la vérification de la disponibilité.</span><span class="sxs-lookup"><span data-stu-id="a0059-279">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="a0059-280">Le test qui permet de vérifier si l’application est prête filtre les contrôles d’intégrité pour n’afficher que celui dont l’étiquette est `ready`.</span><span class="sxs-lookup"><span data-stu-id="a0059-280">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="a0059-281">`/health/live` pour la vérification de l’activité.</span><span class="sxs-lookup"><span data-stu-id="a0059-281">`/health/live` for the liveness check.</span></span> <span data-ttu-id="a0059-282">La vérification d’activité filtre le `StartupHostedServiceHealthCheck` en retournant `false` dans le [prédicat HealthCheckOptions.](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (pour plus d’informations, consultez [contrôles d’intégrité de filtre](#filter-health-checks)).</span><span class="sxs-lookup"><span data-stu-id="a0059-282">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="a0059-283">Dans l’exemple de code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0059-283">In the following example code:</span></span>

* <span data-ttu-id="a0059-284">La vérification de disponibilité utilise toutes les vérifications enregistrées avec la balise « Ready ».</span><span class="sxs-lookup"><span data-stu-id="a0059-284">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="a0059-285">Le `Predicate` exclut toutes les vérifications et retourne un 200-OK.</span><span class="sxs-lookup"><span data-stu-id="a0059-285">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="a0059-286">Pour exécuter le scénario de configuration readiness/liveness à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0059-286">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="a0059-287">Dans un navigateur, accédez à `/health/ready` plusieurs fois jusqu’à ce que 15 secondes soient écoulées.</span><span class="sxs-lookup"><span data-stu-id="a0059-287">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="a0059-288">Le contrôle d’intégrité signale *Unhealthy* pendant les 15 premières secondes.</span><span class="sxs-lookup"><span data-stu-id="a0059-288">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="a0059-289">Après 15 secondes, le point de terminaison signale *Healthy*, ce qui reflète l’achèvement de la tâche de longue durée exécutée par le service hébergé.</span><span class="sxs-lookup"><span data-stu-id="a0059-289">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="a0059-290">Cet exemple crée également un éditeur de vérification de l’intégrité (implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) qui exécute la première vérification de disponibilité avec un délai de deux secondes.</span><span class="sxs-lookup"><span data-stu-id="a0059-290">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="a0059-291">Pour plus d’informations, consultez la section [Éditeur de vérification de l’intégrité](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="a0059-291">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="a0059-292">Exemple Kubernetes</span><span class="sxs-lookup"><span data-stu-id="a0059-292">Kubernetes example</span></span>

<span data-ttu-id="a0059-293">Dans un environnement tel que [Kubernetes](https://kubernetes.io/), il peut être utile d’utiliser séparément le test permettant de savoir si la l’application est prête et celui visant à savoir si l’application est active.</span><span class="sxs-lookup"><span data-stu-id="a0059-293">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="a0059-294">Dans Kubernetes, une application peut devoir effectuer une tâche de démarrage de longue durée avant d’accepter des requêtes, telles qu’un test de la disponibilité de la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a0059-294">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="a0059-295">L’utilisation séparée des deux tests permet à l’orchestrateur de faire la distinction entre une application qui fonctionne mais qui n’est pas encore prête, et une application qui n’a pas pu démarrer.</span><span class="sxs-lookup"><span data-stu-id="a0059-295">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="a0059-296">Pour plus d’informations sur les sondages probe readiness et probe liveness dans Kubernetes, consultez [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) dans la documentation Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="a0059-296">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="a0059-297">L’exemple suivant montre une configuration probe readiness Kubernetes :</span><span class="sxs-lookup"><span data-stu-id="a0059-297">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```yml
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="a0059-298">Sonde basée sur les métriques avec un enregistreur de réponse personnalisé</span><span class="sxs-lookup"><span data-stu-id="a0059-298">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="a0059-299">L’exemple d’application montre un contrôle d’intégrité de mémoire avec un enregistreur de réponse personnalisé.</span><span class="sxs-lookup"><span data-stu-id="a0059-299">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="a0059-300">`MemoryHealthCheck` signale un état détérioré si l’application utilise plus de mémoire que le seuil défini (1 Go dans l’exemple d’application).</span><span class="sxs-lookup"><span data-stu-id="a0059-300">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="a0059-301"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> inclut des informations de récupérateur de mémoire pour l’application (*MemoryHealthCheck.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-301">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="a0059-302">Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a0059-302">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a0059-303">Au lieu d’activer le contrôle d’intégrité en le passant à <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` est inscrit en tant que service.</span><span class="sxs-lookup"><span data-stu-id="a0059-303">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="a0059-304">Tous les services <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> inscrits sont disponibles pour les services de contrôle d’intégrité et le middleware.</span><span class="sxs-lookup"><span data-stu-id="a0059-304">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="a0059-305">Nous vous recommandons d’inscrire les services de contrôle d’intégrité en tant que services Singleton.</span><span class="sxs-lookup"><span data-stu-id="a0059-305">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="a0059-306">Dans *CustomWriterStartup.cs* de l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="a0059-306">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="a0059-307">Un point de terminaison de contrôle d’intégrité est créé en appelant `MapHealthChecks` dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a0059-307">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="a0059-308">Un `WriteResponse` délégué est fourni au <propriété Microsoft. AspNetCore. Diagnostics. HealthChecks. HealthCheckOptions. ResponseWriter> pour générer une réponse JSON personnalisée lors de l’exécution du contrôle d’intégrité :</span><span class="sxs-lookup"><span data-stu-id="a0059-308">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="a0059-309">Le `WriteResponse` délégué met en forme le `CompositeHealthCheckResult` en un objet JSON et génère la sortie JSON pour la réponse de contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-309">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="a0059-310">Pour plus d’informations, consultez la section [personnaliser la sortie](#customize-output) .</span><span class="sxs-lookup"><span data-stu-id="a0059-310">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="a0059-311">Pour exécuter le sondage basé sur les métriques avec l’enregistreur de réponse personnalisé à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0059-311">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="a0059-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclut des scénarios de vérification de l’intégrité basés sur des métriques, notamment des vérifications du stockage sur disque et de la vivacité maximale des valeurs.</span><span class="sxs-lookup"><span data-stu-id="a0059-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="a0059-313">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a0059-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="a0059-314">Filtrer par port</span><span class="sxs-lookup"><span data-stu-id="a0059-314">Filter by port</span></span>

<span data-ttu-id="a0059-315">Appelez `RequireHost` `MapHealthChecks` avec un modèle d’URL qui spécifie un port pour restreindre les demandes de contrôle d’intégrité au port spécifié.</span><span class="sxs-lookup"><span data-stu-id="a0059-315">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="a0059-316">Ceci est généralement utilisé dans les environnements de conteneurs pour exposer un port aux services de supervision.</span><span class="sxs-lookup"><span data-stu-id="a0059-316">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="a0059-317">L’exemple d’application configure le port à l’aide du [fournisseur de configuration des variables d’environnement](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="a0059-317">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="a0059-318">Le port est défini dans le fichier *launchSettings.json*, puis transmis au fournisseur de configuration via une variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="a0059-318">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="a0059-319">Vous devez également configurer le serveur pour écouter les requêtes qui arrivent au port de gestion.</span><span class="sxs-lookup"><span data-stu-id="a0059-319">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="a0059-320">Pour utiliser l’exemple d’application dans le but d’illustrer la configuration du port de gestion, créez le fichier *launchSettings.json* dans un dossier *Propriétés*.</span><span class="sxs-lookup"><span data-stu-id="a0059-320">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="a0059-321">Les *Propriétés/launchSettings.js* suivantes du fichier dans l’exemple d’application ne sont pas incluses dans les fichiers projet de l’exemple d’application et doivent être créées manuellement :</span><span class="sxs-lookup"><span data-stu-id="a0059-321">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="a0059-322">Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a0059-322">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a0059-323">Créez un point de terminaison de contrôle d’intégrité en appelant `MapHealthChecks` dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a0059-323">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="a0059-324">Dans l’exemple d’application, un appel à `RequireHost` sur le point de terminaison dans `Startup.Configure` spécifie le port de gestion de la configuration :</span><span class="sxs-lookup"><span data-stu-id="a0059-324">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="a0059-325">Les points de terminaison sont créés dans l’exemple d’application dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a0059-325">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="a0059-326">Dans l’exemple de code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0059-326">In the following example code:</span></span>

* <span data-ttu-id="a0059-327">La vérification de disponibilité utilise toutes les vérifications enregistrées avec la balise « Ready ».</span><span class="sxs-lookup"><span data-stu-id="a0059-327">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="a0059-328">Le `Predicate` exclut toutes les vérifications et retourne un 200-OK.</span><span class="sxs-lookup"><span data-stu-id="a0059-328">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="a0059-329">Vous pouvez éviter de créer le *launchSettings.jssur* le fichier dans l’exemple d’application en définissant le port de gestion explicitement dans le code.</span><span class="sxs-lookup"><span data-stu-id="a0059-329">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="a0059-330">Dans *Program.cs* où <xref:Microsoft.Extensions.Hosting.HostBuilder> est créé, ajoutez un appel à <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> et fournissez le point de terminaison du port de gestion de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0059-330">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="a0059-331">Dans `Configure` *ManagementPortStartup.cs*, spécifiez le port de gestion avec `RequireHost` :</span><span class="sxs-lookup"><span data-stu-id="a0059-331">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="a0059-332">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0059-332">*Program.cs*:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="a0059-333">*ManagementPortStartup.cs* :</span><span class="sxs-lookup"><span data-stu-id="a0059-333">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="a0059-334">Pour exécuter le scénario de configuration du port de gestion à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0059-334">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="a0059-335">Distribuer une bibliothèque de contrôle d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-335">Distribute a health check library</span></span>

<span data-ttu-id="a0059-336">Pour distribuer une bibliothèque comme un contrôle d’intégrité :</span><span class="sxs-lookup"><span data-stu-id="a0059-336">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="a0059-337">Écrivez un contrôle d’intégrité qui implémente l’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> comme une classe autonome.</span><span class="sxs-lookup"><span data-stu-id="a0059-337">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="a0059-338">La classe peut utiliser une [injection de dépendance](xref:fundamentals/dependency-injection), une activation de type et des [options nommées](xref:fundamentals/configuration/options) pour accéder aux données de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0059-338">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="a0059-339">Dans la logique des contrôles d’intégrité de `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="a0059-339">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="a0059-340">`data1` et `data2` sont utilisés dans la méthode pour exécuter la logique de contrôle d’intégrité de la sonde.</span><span class="sxs-lookup"><span data-stu-id="a0059-340">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="a0059-341">`AccessViolationException` est géré.</span><span class="sxs-lookup"><span data-stu-id="a0059-341">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="a0059-342">Lorsqu’un <xref:System.AccessViolationException> se produit, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> est retourné avec le <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> pour permettre aux utilisateurs de configurer l’état d’échec des contrôles d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-342">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="a0059-343">Écrivez une méthode d’extension avec des paramètres que l’application consommatrice appelle dans sa méthode `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a0059-343">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="a0059-344">Dans l’exemple qui suit, prenons la signature de méthode de contrôle d’intégrité suivante :</span><span class="sxs-lookup"><span data-stu-id="a0059-344">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="a0059-345">La signature précédente indique que `ExampleHealthCheck` nécessite des données supplémentaires pour traiter la logique de sondage du contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-345">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="a0059-346">Les données sont fournies au délégué qui est utilisé pour créer l’instance de contrôle d’intégrité lorsque le contrôle d’intégrité est inscrit avec une méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="a0059-346">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="a0059-347">Dans l’exemple qui suit, l’appelant spécifie les éléments facultatifs suivants :</span><span class="sxs-lookup"><span data-stu-id="a0059-347">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="a0059-348">Nom du contrôle d’intégrité (`name`).</span><span class="sxs-lookup"><span data-stu-id="a0059-348">health check name (`name`).</span></span> <span data-ttu-id="a0059-349">Si `null`, `example_health_check` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="a0059-349">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="a0059-350">Point de données de chaîne du contrôle d’intégrité (`data1`).</span><span class="sxs-lookup"><span data-stu-id="a0059-350">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="a0059-351">Point de données Integer du contrôle d’intégrité (`data2`).</span><span class="sxs-lookup"><span data-stu-id="a0059-351">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="a0059-352">Si `null`, `1` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="a0059-352">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="a0059-353">État d’échec (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="a0059-353">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="a0059-354">Par défaut, il s’agit de `null`.</span><span class="sxs-lookup"><span data-stu-id="a0059-354">The default is `null`.</span></span> <span data-ttu-id="a0059-355">Si `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) est signalé pour un état d’échec.</span><span class="sxs-lookup"><span data-stu-id="a0059-355">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="a0059-356">Étiquettes (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="a0059-356">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="a0059-357">Serveur de publication des contrôles d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-357">Health Check Publisher</span></span>

<span data-ttu-id="a0059-358">Quand un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> est ajouté au conteneur du service, le système de contrôle d’intégrité exécute régulièrement vos contrôles d’intégrité et appelle `PublishAsync` avec le résultat.</span><span class="sxs-lookup"><span data-stu-id="a0059-358">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="a0059-359">C’est utile dans un scénario impliquant un système de supervision basé sur les envois (push) et nécessitant que chaque processus appelle le système de supervision régulièrement afin de déterminer l’état d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-359">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="a0059-360">L’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> comprend une seule méthode :</span><span class="sxs-lookup"><span data-stu-id="a0059-360">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="a0059-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> vous autorise à définir :</span><span class="sxs-lookup"><span data-stu-id="a0059-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="a0059-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Délai initial appliqué après le démarrage de l’application avant l’exécution des <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span><span class="sxs-lookup"><span data-stu-id="a0059-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="a0059-363">Le retard est appliqué à une seule fois au démarrage et ne s’applique pas aux itérations ultérieures.</span><span class="sxs-lookup"><span data-stu-id="a0059-363">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="a0059-364">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="a0059-364">The default value is five seconds.</span></span>
* <span data-ttu-id="a0059-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Période d' <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> exécution.</span><span class="sxs-lookup"><span data-stu-id="a0059-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="a0059-366">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="a0059-366">The default value is 30 seconds.</span></span>
* <span data-ttu-id="a0059-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Si <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> a `null` la valeur (valeur par défaut), le service d’éditeur de contrôle d’intégrité exécute toutes les vérifications d’intégrité inscrites.</span><span class="sxs-lookup"><span data-stu-id="a0059-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="a0059-368">Pour exécuter un sous-ensemble de contrôles d’intégrité, fournissez une fonction qui filtre l’ensemble de vérifications.</span><span class="sxs-lookup"><span data-stu-id="a0059-368">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="a0059-369">Le prédicat est évalué à chaque période.</span><span class="sxs-lookup"><span data-stu-id="a0059-369">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="a0059-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Délai d’exécution des contrôles d’intégrité de toutes les <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span><span class="sxs-lookup"><span data-stu-id="a0059-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="a0059-371">Utilisez <xref:System.Threading.Timeout.InfiniteTimeSpan> pour une exécution sans délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="a0059-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="a0059-372">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="a0059-372">The default value is 30 seconds.</span></span>

<span data-ttu-id="a0059-373">Dans l’exemple d’application, `ReadinessPublisher` est une implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="a0059-373">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="a0059-374">L’état du contrôle d’intégrité est journalisé pour chaque vérification au niveau du journal :</span><span class="sxs-lookup"><span data-stu-id="a0059-374">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="a0059-375">Information ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ) si l’état des contrôles d’intégrité est <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="a0059-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="a0059-376">Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ) si l’État est <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> ou <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="a0059-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="a0059-377">Dans l’exemple d’application `LivenessProbeStartup`, la vérification de la disponibilité `StartupHostedService` a un délai de démarrage de deux secondes et exécute la vérification toutes les 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="a0059-377">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="a0059-378">Pour activer l’implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, l’exemple enregistre `ReadinessPublisher` comme un service singleton dans le conteneur [d’injection de dépendance (DI)](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="a0059-378">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="a0059-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclut les serveurs de publication pour plusieurs systèmes, notamment [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="a0059-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="a0059-380">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a0059-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="a0059-381">Restreindre les vérifications d’intégrité avec MapWhen</span><span class="sxs-lookup"><span data-stu-id="a0059-381">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="a0059-382">Utilisez <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> pour créer une branche conditionnelle du pipeline des demandes pour les points de terminaison de contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="a0059-383">Dans l’exemple suivant, `MapWhen` branche le pipeline de demande pour activer l’intergiciel (middleware) des contrôles d’intégrité si une requête d’extraction est reçue pour le `api/HealthCheck` point de terminaison :</span><span class="sxs-lookup"><span data-stu-id="a0059-383">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="a0059-384">Pour plus d’informations, consultez <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.</span><span class="sxs-lookup"><span data-stu-id="a0059-384">For more information, see <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a0059-385">ASP.NET Core offre un intergiciel et des bibliothèques de contrôle d’intégrité pour signaler l’intégrité des composants d’infrastructure d’application.</span><span class="sxs-lookup"><span data-stu-id="a0059-385">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="a0059-386">Les contrôles d’intégrité sont exposés par une application comme des points de terminaison HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0059-386">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="a0059-387">Les points de terminaison de vérification d’intégrité peuvent être configurés pour de nombreux scénarios de supervision en temps réel :</span><span class="sxs-lookup"><span data-stu-id="a0059-387">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="a0059-388">Les sondes d’intégrité peuvent être utilisées par les orchestrateurs de conteneurs et les équilibreurs de charge afin de vérifier l’état d’une application.</span><span class="sxs-lookup"><span data-stu-id="a0059-388">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="a0059-389">Par exemple, un orchestrateur de conteneurs peut répondre à un résultat de non intégrité en arrêtant le déploiement ou en redémarrant un conteneur.</span><span class="sxs-lookup"><span data-stu-id="a0059-389">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="a0059-390">Face à une application non saine, l’équilibreur de charge peut réagir en redirigeant le trafic vers une instance saine.</span><span class="sxs-lookup"><span data-stu-id="a0059-390">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="a0059-391">L’utilisation de la mémoire, des disques et des autres ressources de serveur physique peut être supervisée dans le cadre d’un contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-391">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="a0059-392">Les contrôles d’intégrité peuvent tester les dépendances d’une application, telles que les bases de données et les points de terminaison de service externes, dans le but de vérifier leur disponibilité et leur bon fonctionnement.</span><span class="sxs-lookup"><span data-stu-id="a0059-392">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="a0059-393">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a0059-393">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a0059-394">L’exemple d’application comprend des exemples pour les scénarios décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="a0059-394">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="a0059-395">Pour exécuter l’exemple d’application selon un scénario donné, utilisez la commande [dotnet run](/dotnet/core/tools/dotnet-run) dans un interpréteur de commandes, à partir du dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="a0059-395">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="a0059-396">Pour plus d’informations sur l’utilisation de l’exemple d’application, consultez le fichier *README.md* de l’exemple d’application ainsi que les descriptions de scénarios de cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="a0059-396">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a0059-397">Prérequis</span><span class="sxs-lookup"><span data-stu-id="a0059-397">Prerequisites</span></span>

<span data-ttu-id="a0059-398">Les contrôles d’intégrité sont généralement utilisés avec un service de supervision externe ou un orchestrateur de conteneurs pour vérifier l’état d’une application.</span><span class="sxs-lookup"><span data-stu-id="a0059-398">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="a0059-399">Avant d’ajouter des contrôles d’intégrité à une application, vous devez décider du système de supervision à utiliser.</span><span class="sxs-lookup"><span data-stu-id="a0059-399">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="a0059-400">Le système de supervision détermine les types de contrôles d’intégrité qui doivent être créés ainsi que la configuration de leurs points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="a0059-400">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="a0059-401">Référencez le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou ajoutez une référence de package au package [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="a0059-401">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="a0059-402">L’exemple d’application fournit du code de démarrage pour illustrer les contrôles d’intégrité de plusieurs scénarios.</span><span class="sxs-lookup"><span data-stu-id="a0059-402">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="a0059-403">Le scénario [probe de la base de données](#database-probe) vérifie l’intégrité d’une connexion de base de données à l’aide d’[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="a0059-403">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="a0059-404">Le scénario [Sondage DbContext](#entity-framework-core-dbcontext-probe) vérifie une base de données à l’aide d’un `DbContext` EF Core.</span><span class="sxs-lookup"><span data-stu-id="a0059-404">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="a0059-405">Pour explorer les scénarios de base de données, l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="a0059-405">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="a0059-406">Crée une base de données et fournit sa chaîne de connexion dans le *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="a0059-406">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="a0059-407">Possède les références de package suivantes dans son fichier de projet :</span><span class="sxs-lookup"><span data-stu-id="a0059-407">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="a0059-408">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="a0059-408">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="a0059-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="a0059-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="a0059-410">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a0059-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="a0059-411">Un autre scénario de contrôle d’intégrité montre comment filtrer des contrôles d’intégrité selon un port de gestion.</span><span class="sxs-lookup"><span data-stu-id="a0059-411">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="a0059-412">L’exemple d’application vous oblige à créer un fichier *Properties/launchSettings.json* comprenant l’URL de gestion et le port de gestion.</span><span class="sxs-lookup"><span data-stu-id="a0059-412">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="a0059-413">Pour plus d’informations, consultez la section [Filtrer par port](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="a0059-413">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="a0059-414">Sondage d’intégrité de base</span><span class="sxs-lookup"><span data-stu-id="a0059-414">Basic health probe</span></span>

<span data-ttu-id="a0059-415">Pour de nombreuses applications, un sondage d’intégrité de base qui signale la disponibilité d’une application pour le traitement des requêtes (*liveness*) suffit à découvrir l’état de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0059-415">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="a0059-416">La configuration de base inscrit les services de contrôle d’intégrité et appelle l’intergiciel (middleware) des contrôles d’intégrité pour répondre à un point de terminaison d’URL avec une réponse d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-416">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="a0059-417">Par défaut, aucun contrôle d’intégrité n’est inscrit pour tester les dépendances ou le sous-système.</span><span class="sxs-lookup"><span data-stu-id="a0059-417">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="a0059-418">L’application est considérée comme saine si elle est capable de répondre à l’URL de point de terminaison de contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-418">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="a0059-419">L’enregistreur de réponse par défaut écrit l’état (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) sous forme de texte en clair qu’il renvoie au client, indiquant si l’état est [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) ou [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="a0059-419">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="a0059-420">Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a0059-420">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a0059-421">Ajoutez un point de terminaison pour l’intergiciel de contrôles d’intégrité avec <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> dans le pipeline de traitement des requêtes de `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a0059-421">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="a0059-422">Dans l’exemple d’application, le point de terminaison de contrôle d’intégrité est créé au niveau de `/health` (*BasicStartup.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-422">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="a0059-423">Pour exécuter le scénario de configuration de base à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0059-423">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="a0059-424">Exemple Docker</span><span class="sxs-lookup"><span data-stu-id="a0059-424">Docker example</span></span>

<span data-ttu-id="a0059-425">[Docker](xref:host-and-deploy/docker/index) fournit une directive `HEALTHCHECK` intégrée qui peut être utilisée pour vérifier l’état d’une application utilisant la configuration de contrôle d’intégrité de base :</span><span class="sxs-lookup"><span data-stu-id="a0059-425">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="a0059-426">Créer des contrôles d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-426">Create health checks</span></span>

<span data-ttu-id="a0059-427">Les contrôles d’intégrité sont créés via l’implémentation de l’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>.</span><span class="sxs-lookup"><span data-stu-id="a0059-427">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="a0059-428">La méthode <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> retourne un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> qui indique l’état d’intégrité comme étant `Healthy`, `Degraded` ou `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="a0059-428">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="a0059-429">Le résultat est écrit sous la forme de texte en clair avec un code d’état configurable (la configuration est décrite dans la section [Options de contrôle d’intégrité](#health-check-options)).</span><span class="sxs-lookup"><span data-stu-id="a0059-429">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="a0059-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> peut également retourner des paires clé-valeur facultatives.</span><span class="sxs-lookup"><span data-stu-id="a0059-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="a0059-431">Exemple de contrôle d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-431">Example health check</span></span>

<span data-ttu-id="a0059-432">La `ExampleHealthCheck` classe suivante illustre la disposition d’un contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-432">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="a0059-433">La logique des contrôles d’intégrité est placée dans la `CheckHealthAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="a0059-433">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="a0059-434">L’exemple suivant définit une variable factice, `healthCheckResultHealthy` , sur `true` .</span><span class="sxs-lookup"><span data-stu-id="a0059-434">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="a0059-435">Si la valeur de `healthCheckResultHealthy` est définie sur `false` , l’état [HealthCheckResult. inhealth](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) est retourné.</span><span class="sxs-lookup"><span data-stu-id="a0059-435">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a><span data-ttu-id="a0059-436">Inscrire les services de contrôle d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-436">Register health check services</span></span>

<span data-ttu-id="a0059-437">Le `ExampleHealthCheck` type est ajouté aux services de contrôle d’intégrité dans `Startup.ConfigureServices` avec <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> :</span><span class="sxs-lookup"><span data-stu-id="a0059-437">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="a0059-438">La surcharge <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> de l’exemple suivant définit l’état d’échec (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) de manière à être signalé lorsque le contrôle d’intégrité signale un échec.</span><span class="sxs-lookup"><span data-stu-id="a0059-438">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="a0059-439">Si l’état d’échec est défini sur `null` (par défaut), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) est signalé.</span><span class="sxs-lookup"><span data-stu-id="a0059-439">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="a0059-440">Cette surcharge est utile pour les créateurs de bibliothèque, dans les cas où l’état d’échec indiqué par la bibliothèque est appliqué par l’application lorsqu’un échec est signalé par le contrôle d’intégrité, si l’implémentation de ce dernier respecte le paramètre.</span><span class="sxs-lookup"><span data-stu-id="a0059-440">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="a0059-441">Des *étiquettes* peuvent être utilisées pour filtrer les contrôles d’intégrité (cette procédure est décrite plus loin dans la section [Filtrer les contrôles d’intégrité](#filter-health-checks)).</span><span class="sxs-lookup"><span data-stu-id="a0059-441">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="a0059-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> peut également exécuter une fonction lambda.</span><span class="sxs-lookup"><span data-stu-id="a0059-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="a0059-443">Dans l' `Startup.ConfigureServices` exemple suivant, le nom du contrôle d’intégrité est spécifié comme `Example` et le contrôle retourne toujours un état sain :</span><span class="sxs-lookup"><span data-stu-id="a0059-443">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="a0059-444">Utiliser Health Checks Middleware</span><span class="sxs-lookup"><span data-stu-id="a0059-444">Use Health Checks Middleware</span></span>

<span data-ttu-id="a0059-445">Dans `Startup.Configure`, appelez <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> dans le pipeline de traitement avec l’URL de point de terminaison ou le chemin relatif :</span><span class="sxs-lookup"><span data-stu-id="a0059-445">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="a0059-446">Si les contrôles d’intégrité doivent écouter un port en particulier, utilisez une surcharge de <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> pour définir le port (cette procédure est décrite plus loin dans la section [Filtrer par port](#filter-by-port)) :</span><span class="sxs-lookup"><span data-stu-id="a0059-446">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="a0059-447">Options de contrôle d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-447">Health check options</span></span>

<span data-ttu-id="a0059-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> permet de personnaliser le comportement du contrôle d’intégrité :</span><span class="sxs-lookup"><span data-stu-id="a0059-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="a0059-449">Filtrer les contrôles d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-449">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="a0059-450">Personnaliser le code d’état HTTP</span><span class="sxs-lookup"><span data-stu-id="a0059-450">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="a0059-451">Supprimer les en-têtes de cache</span><span class="sxs-lookup"><span data-stu-id="a0059-451">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="a0059-452">Personnaliser la sortie</span><span class="sxs-lookup"><span data-stu-id="a0059-452">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="a0059-453">Filtrer les contrôles d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-453">Filter health checks</span></span>

<span data-ttu-id="a0059-454">Par défaut, l’intergiciel (middleware) des contrôles d’intégrité exécute toutes les vérifications d’intégrité inscrites.</span><span class="sxs-lookup"><span data-stu-id="a0059-454">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="a0059-455">Pour exécuter un sous-ensemble de contrôles d’intégrité, fournissez une fonction qui retourne une valeur booléenne à l’option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>.</span><span class="sxs-lookup"><span data-stu-id="a0059-455">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="a0059-456">Dans l’exemple suivant, le contrôle d’intégrité `Bar` est filtré en fonction de son étiquette (`bar_tag`) dans l’instruction conditionnelle de la fonction, où `true` est retourné uniquement si la propriété <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> du contrôle d’intégrité correspond à `foo_tag` ou à `baz_tag` :</span><span class="sxs-lookup"><span data-stu-id="a0059-456">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="a0059-457">Personnaliser le code d’état HTTP</span><span class="sxs-lookup"><span data-stu-id="a0059-457">Customize the HTTP status code</span></span>

<span data-ttu-id="a0059-458">Utilisez <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> pour personnaliser le mappage de l’état d’intégrité aux codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0059-458">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="a0059-459">Les affectations <xref:Microsoft.AspNetCore.Http.StatusCodes> suivantes sont les valeurs par défaut utilisées par le middleware.</span><span class="sxs-lookup"><span data-stu-id="a0059-459">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="a0059-460">Vous pouvez modifier les valeurs de code d’état selon vos besoins.</span><span class="sxs-lookup"><span data-stu-id="a0059-460">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="a0059-461">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a0059-461">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="a0059-462">Supprimer les en-têtes de cache</span><span class="sxs-lookup"><span data-stu-id="a0059-462">Suppress cache headers</span></span>

<span data-ttu-id="a0059-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> contrôle si l’intergiciel (middleware) de contrôles d’intégrité ajoute des en-têtes HTTP à une réponse de sondage pour empêcher la mise en cache des réponses.</span><span class="sxs-lookup"><span data-stu-id="a0059-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="a0059-464">Si la valeur est `false` (par défaut), le middleware définit ou substitue les en-têtes `Cache-Control`, `Expires` et `Pragma` afin d’empêcher la mise en cache de la réponse.</span><span class="sxs-lookup"><span data-stu-id="a0059-464">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="a0059-465">Si la valeur est `true`, le middleware ne modifie pas les en-têtes de cache de la réponse.</span><span class="sxs-lookup"><span data-stu-id="a0059-465">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="a0059-466">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a0059-466">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="a0059-467">Personnaliser la sortie</span><span class="sxs-lookup"><span data-stu-id="a0059-467">Customize output</span></span>

<span data-ttu-id="a0059-468">L’option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> obtient ou définit un délégué utilisé pour écrire la réponse.</span><span class="sxs-lookup"><span data-stu-id="a0059-468">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="a0059-469">Le délégué par défaut écrit une réponse minimale constituée de texte en clair, avec la valeur de chaîne [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="a0059-469">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="a0059-470">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a0059-470">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="a0059-471">Le délégué par défaut écrit une réponse minimale constituée de texte en clair, avec la valeur de chaîne [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="a0059-471">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="a0059-472">Le délégué personnalisé suivant, `WriteResponse` , génère une réponse JSON personnalisée :</span><span class="sxs-lookup"><span data-stu-id="a0059-472">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

<span data-ttu-id="a0059-473">Le système de contrôle d’intégrité ne fournit pas de prise en charge intégrée des formats de retour JSON complexes, car le format est spécifique à votre choix de système de surveillance.</span><span class="sxs-lookup"><span data-stu-id="a0059-473">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="a0059-474">N’hésitez pas à personnaliser le `JObject` dans l’exemple précédent si nécessaire pour répondre à vos besoins.</span><span class="sxs-lookup"><span data-stu-id="a0059-474">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="a0059-475">Sondage de base de données</span><span class="sxs-lookup"><span data-stu-id="a0059-475">Database probe</span></span>

<span data-ttu-id="a0059-476">Un contrôle d’intégrité peut spécifier une requête de base de données à exécuter en tant que test booléen pour indiquer si la base de données répond normalement.</span><span class="sxs-lookup"><span data-stu-id="a0059-476">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="a0059-477">L’exemple d’application utilise [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), une bibliothèque de vérification d’intégrité pour les applications ASP.NET Core, pour effectuer une vérification d’intégrité sur une base de données SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a0059-477">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="a0059-478">`AspNetCore.Diagnostics.HealthChecks` exécute une demande `SELECT 1` sur la base de données pour confirmer que la connexion à la base de données est saine.</span><span class="sxs-lookup"><span data-stu-id="a0059-478">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="a0059-479">Lorsque vous vérifiez une connexion de base de données à l’aide d’une requête, choisissez une requête qui est retournée rapidement.</span><span class="sxs-lookup"><span data-stu-id="a0059-479">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="a0059-480">L’utilisation d’une requête risque néanmoins d’entraîner une surcharge de la base de données et d’en diminuer les performances.</span><span class="sxs-lookup"><span data-stu-id="a0059-480">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="a0059-481">Dans la plupart des cas, il n’est pas nécessaire d’utiliser une requête de test.</span><span class="sxs-lookup"><span data-stu-id="a0059-481">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="a0059-482">Il suffit simplement d’établir une connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="a0059-482">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="a0059-483">Si vous avez besoin d’exécuter une requête, choisissez une requête SELECT simple, telle que `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="a0059-483">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="a0059-484">Inclure une référence de package à [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="a0059-484">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="a0059-485">Fournissez une chaîne de connexion de base de données valide dans le *appsettings.json* fichier de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="a0059-485">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="a0059-486">L’application utilise une base de données SQL Server nommée `HealthCheckSample` :</span><span class="sxs-lookup"><span data-stu-id="a0059-486">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="a0059-487">Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a0059-487">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a0059-488">L’exemple d’application appelle la méthode `AddSqlServer` avec la chaîne de connexion de la base de données (*DbHealthStartup.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-488">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a0059-489">Call Health Checks middleware dans le pipeline de traitement des applications dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a0059-489">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="a0059-490">Pour exécuter le scénario de sondage d’une base de données à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0059-490">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="a0059-491">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a0059-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="a0059-492">Sondage DbContext Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a0059-492">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="a0059-493">La vérification `DbContext` vérifie que l’application peut communiquer avec la base de données configurée pour un `DbContext` EF Core.</span><span class="sxs-lookup"><span data-stu-id="a0059-493">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="a0059-494">La vérification `DbContext` est prise en charge dans les applications qui :</span><span class="sxs-lookup"><span data-stu-id="a0059-494">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="a0059-495">Utilisent [Entity Framework (EF) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="a0059-495">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="a0059-496">Incluent une référence de package à [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="a0059-496">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="a0059-497">`AddDbContextCheck<TContext>` inscrit un contrôle d’intégrité pour un `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="a0059-497">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="a0059-498">Le `DbContext` est fourni en tant que `TContext` à la méthode.</span><span class="sxs-lookup"><span data-stu-id="a0059-498">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="a0059-499">Une surcharge est disponible pour configurer l’état d’échec, les étiquettes et une requête de test personnalisée.</span><span class="sxs-lookup"><span data-stu-id="a0059-499">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="a0059-500">Par défaut :</span><span class="sxs-lookup"><span data-stu-id="a0059-500">By default:</span></span>

* <span data-ttu-id="a0059-501">`DbContextHealthCheck` appelle la méthode `CanConnectAsync` d’EF Core.</span><span class="sxs-lookup"><span data-stu-id="a0059-501">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="a0059-502">Vous pouvez choisir quelle opération doit être exécutée lors du contrôle d’intégrité à l’aide des surcharges de la méthode `AddDbContextCheck`.</span><span class="sxs-lookup"><span data-stu-id="a0059-502">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="a0059-503">Le nom du contrôle d’intégrité correspond à celui du type `TContext`.</span><span class="sxs-lookup"><span data-stu-id="a0059-503">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="a0059-504">Dans l’exemple d’application, `AppDbContext` est fourni à `AddDbContextCheck` et enregistré en tant que service dans `Startup.ConfigureServices` (*DbContextHealthStartup.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-504">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a0059-505">Dans l’exemple d’application, `UseHealthChecks` ajoute l’intergiciel de contrôles d’intégrité dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a0059-505">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="a0059-506">Pour exécuter le scénario de sondage `DbContext` à l’aide de l’exemple d’application, vérifiez que la base de données spécifiée par le la chaîne de connexion ne se trouve pas déjà dans l’instance SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a0059-506">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="a0059-507">Si la base de données existe, supprimez-la.</span><span class="sxs-lookup"><span data-stu-id="a0059-507">If the database exists, delete it.</span></span>

<span data-ttu-id="a0059-508">Exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0059-508">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="a0059-509">Une fois que l’application est en cours d’exécution, vérifiez l’état d’intégrité en envoyant une requête au point de terminaison `/health` dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="a0059-509">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="a0059-510">La base de données et `AppDbContext` n’existent pas, donc l’application fournit la réponse suivante :</span><span class="sxs-lookup"><span data-stu-id="a0059-510">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="a0059-511">Déclenchez l’exemple d’application pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="a0059-511">Trigger the sample app to create the database.</span></span> <span data-ttu-id="a0059-512">Envoyez une requête à `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="a0059-512">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="a0059-513">L’application répond :</span><span class="sxs-lookup"><span data-stu-id="a0059-513">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="a0059-514">Envoyez une requête au point de terminaison `/health`.</span><span class="sxs-lookup"><span data-stu-id="a0059-514">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="a0059-515">La base de données et le contexte existent, donc l’application répond :</span><span class="sxs-lookup"><span data-stu-id="a0059-515">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="a0059-516">Déclenchez l’exemple d’application pour supprimer la base de données.</span><span class="sxs-lookup"><span data-stu-id="a0059-516">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="a0059-517">Envoyez une requête à `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="a0059-517">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="a0059-518">L’application répond :</span><span class="sxs-lookup"><span data-stu-id="a0059-518">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="a0059-519">Envoyez une requête au point de terminaison `/health`.</span><span class="sxs-lookup"><span data-stu-id="a0059-519">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="a0059-520">L’application fournit une réponse non intégrité :</span><span class="sxs-lookup"><span data-stu-id="a0059-520">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="a0059-521">Séparer les sondages probe readiness et probe liveness</span><span class="sxs-lookup"><span data-stu-id="a0059-521">Separate readiness and liveness probes</span></span>

<span data-ttu-id="a0059-522">Dans certains scénarios d’hébergement, une paire de contrôles d’intégrité est utilisée pour distinguer deux états d’application :</span><span class="sxs-lookup"><span data-stu-id="a0059-522">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="a0059-523">L’état de *préparation* indique si l’application s’exécute normalement mais n’est pas prête à recevoir des demandes.</span><span class="sxs-lookup"><span data-stu-id="a0059-523">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="a0059-524">L' *activité* indique si une application s’est arrêtée et doit être redémarrée.</span><span class="sxs-lookup"><span data-stu-id="a0059-524">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="a0059-525">Prenons l’exemple suivant : une application doit télécharger un fichier de configuration volumineux avant de pouvoir traiter les demandes.</span><span class="sxs-lookup"><span data-stu-id="a0059-525">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="a0059-526">Nous ne voulons pas que l’application soit redémarrée si le téléchargement initial échoue parce que l’application peut réessayer de télécharger le fichier plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="a0059-526">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="a0059-527">Nous utilisons une *sonde d’activité* pour décrire l’activité du processus, aucune vérification supplémentaire n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="a0059-527">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="a0059-528">Nous voulons également empêcher l’envoi de demandes à l’application avant la réussite du téléchargement du fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0059-528">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="a0059-529">Nous utilisons une *sonde de préparation* pour indiquer un État « non prêt » jusqu’à ce que le téléchargement aboutisse et que l’application soit prête à recevoir des demandes.</span><span class="sxs-lookup"><span data-stu-id="a0059-529">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="a0059-530">L’exemple d’application contient un contrôle d’intégrité permettant de signaler l’achèvement d’une tâche de démarrage de longue durée dans un [service hébergé](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="a0059-530">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="a0059-531">`StartupHostedServiceHealthCheck` expose la propriété `StartupTaskCompleted`, que le service hébergé peut définir sur `true` lorsque sa tâche de longue durée est terminée (*StartupHostedServiceHealthCheck.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-531">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="a0059-532">La tâche de longue durée en arrière-plan est démarrée par un [service hébergé](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="a0059-532">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="a0059-533">À la fin de la tâche, `StartupHostedServiceHealthCheck.StartupTaskCompleted` est défini sur `true` :</span><span class="sxs-lookup"><span data-stu-id="a0059-533">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="a0059-534">Le contrôle d’intégrité est inscrit auprès de <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> dans `Startup.ConfigureServices` en même temps que le service hébergé.</span><span class="sxs-lookup"><span data-stu-id="a0059-534">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="a0059-535">Étant donné que le service hébergé doit définir la propriété sur le contrôle d’intégrité, le contrôle d’intégrité est également inscrit dans le conteneur du service (*LivenessProbeStartup.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-535">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a0059-536">Call Health Checks middleware dans le pipeline de traitement des applications dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a0059-536">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="a0059-537">Dans l’exemple d’application, les points de terminaison de contrôle d’intégrité sont créés au niveau de `/health/ready` pour vérifier si l’application est prête, et au niveau de `/health/live` pour vérifier si l’application est active.</span><span class="sxs-lookup"><span data-stu-id="a0059-537">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="a0059-538">Le test qui permet de vérifier si l’application est prête filtre les contrôles d’intégrité pour n’afficher que celui dont l’étiquette est `ready`.</span><span class="sxs-lookup"><span data-stu-id="a0059-538">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="a0059-539">Le test qui permet de vérifier si l’application est active exclut le `StartupHostedServiceHealthCheck` en retournant `false` dans [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (pour plus d’informations, consultez [Filtrer les contrôles d’intégrité](#filter-health-checks)) :</span><span class="sxs-lookup"><span data-stu-id="a0059-539">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="a0059-540">Pour exécuter le scénario de configuration readiness/liveness à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0059-540">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="a0059-541">Dans un navigateur, accédez à `/health/ready` plusieurs fois jusqu’à ce que 15 secondes soient écoulées.</span><span class="sxs-lookup"><span data-stu-id="a0059-541">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="a0059-542">Le contrôle d’intégrité signale *Unhealthy* pendant les 15 premières secondes.</span><span class="sxs-lookup"><span data-stu-id="a0059-542">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="a0059-543">Après 15 secondes, le point de terminaison signale *Healthy*, ce qui reflète l’achèvement de la tâche de longue durée exécutée par le service hébergé.</span><span class="sxs-lookup"><span data-stu-id="a0059-543">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="a0059-544">Cet exemple crée également un éditeur de vérification de l’intégrité (implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) qui exécute la première vérification de disponibilité avec un délai de deux secondes.</span><span class="sxs-lookup"><span data-stu-id="a0059-544">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="a0059-545">Pour plus d’informations, consultez la section [Éditeur de vérification de l’intégrité](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="a0059-545">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="a0059-546">Exemple Kubernetes</span><span class="sxs-lookup"><span data-stu-id="a0059-546">Kubernetes example</span></span>

<span data-ttu-id="a0059-547">Dans un environnement tel que [Kubernetes](https://kubernetes.io/), il peut être utile d’utiliser séparément le test permettant de savoir si la l’application est prête et celui visant à savoir si l’application est active.</span><span class="sxs-lookup"><span data-stu-id="a0059-547">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="a0059-548">Dans Kubernetes, une application peut devoir effectuer une tâche de démarrage de longue durée avant d’accepter des requêtes, telles qu’un test de la disponibilité de la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a0059-548">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="a0059-549">L’utilisation séparée des deux tests permet à l’orchestrateur de faire la distinction entre une application qui fonctionne mais qui n’est pas encore prête, et une application qui n’a pas pu démarrer.</span><span class="sxs-lookup"><span data-stu-id="a0059-549">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="a0059-550">Pour plus d’informations sur les sondages probe readiness et probe liveness dans Kubernetes, consultez [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) dans la documentation Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="a0059-550">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="a0059-551">L’exemple suivant montre une configuration probe readiness Kubernetes :</span><span class="sxs-lookup"><span data-stu-id="a0059-551">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="a0059-552">Sonde basée sur les métriques avec un enregistreur de réponse personnalisé</span><span class="sxs-lookup"><span data-stu-id="a0059-552">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="a0059-553">L’exemple d’application montre un contrôle d’intégrité de mémoire avec un enregistreur de réponse personnalisé.</span><span class="sxs-lookup"><span data-stu-id="a0059-553">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="a0059-554">`MemoryHealthCheck` signale un État non sain si l’application utilise plus d’un seuil de mémoire donné (1 Go dans l’exemple d’application).</span><span class="sxs-lookup"><span data-stu-id="a0059-554">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="a0059-555"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> inclut des informations de récupérateur de mémoire pour l’application (*MemoryHealthCheck.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-555">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="a0059-556">Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a0059-556">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a0059-557">Au lieu d’activer le contrôle d’intégrité en le passant à <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` est inscrit en tant que service.</span><span class="sxs-lookup"><span data-stu-id="a0059-557">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="a0059-558">Tous les services <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> inscrits sont disponibles pour les services de contrôle d’intégrité et le middleware.</span><span class="sxs-lookup"><span data-stu-id="a0059-558">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="a0059-559">Nous vous recommandons d’inscrire les services de contrôle d’intégrité en tant que services Singleton.</span><span class="sxs-lookup"><span data-stu-id="a0059-559">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="a0059-560">Dans l’exemple d’application (*CustomWriterStartup.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-560">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="a0059-561">Call Health Checks middleware dans le pipeline de traitement des applications dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a0059-561">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="a0059-562">Un délégué `WriteResponse` est fourni à la propriété `ResponseWriter` pour produire une réponse JSON personnalisée lorsque le contrôle d’intégrité est exécuté :</span><span class="sxs-lookup"><span data-stu-id="a0059-562">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="a0059-563">La méthode `WriteResponse` formate le `CompositeHealthCheckResult` en un objet JSON et génère une sortie JSON pour la réponse du contrôle d’intégrité :</span><span class="sxs-lookup"><span data-stu-id="a0059-563">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="a0059-564">Pour exécuter le sondage basé sur les métriques avec l’enregistreur de réponse personnalisé à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0059-564">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="a0059-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclut des scénarios de vérification de l’intégrité basés sur des métriques, notamment des vérifications du stockage sur disque et de la vivacité maximale des valeurs.</span><span class="sxs-lookup"><span data-stu-id="a0059-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="a0059-566">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a0059-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="a0059-567">Filtrer par port</span><span class="sxs-lookup"><span data-stu-id="a0059-567">Filter by port</span></span>

<span data-ttu-id="a0059-568">Si vous appelez <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> avec un port, les requêtes de contrôle d’intégrité seront limitées au port spécifié.</span><span class="sxs-lookup"><span data-stu-id="a0059-568">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="a0059-569">Ceci est généralement utilisé dans les environnements de conteneurs pour exposer un port aux services de supervision.</span><span class="sxs-lookup"><span data-stu-id="a0059-569">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="a0059-570">L’exemple d’application configure le port à l’aide du [fournisseur de configuration des variables d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a0059-570">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="a0059-571">Le port est défini dans le fichier *launchSettings.json*, puis transmis au fournisseur de configuration via une variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="a0059-571">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="a0059-572">Vous devez également configurer le serveur pour écouter les requêtes qui arrivent au port de gestion.</span><span class="sxs-lookup"><span data-stu-id="a0059-572">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="a0059-573">Pour utiliser l’exemple d’application dans le but d’illustrer la configuration du port de gestion, créez le fichier *launchSettings.json* dans un dossier *Propriétés*.</span><span class="sxs-lookup"><span data-stu-id="a0059-573">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="a0059-574">Les *Propriétés/launchSettings.js* suivantes du fichier dans l’exemple d’application ne sont pas incluses dans les fichiers projet de l’exemple d’application et doivent être créées manuellement :</span><span class="sxs-lookup"><span data-stu-id="a0059-574">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="a0059-575">Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a0059-575">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a0059-576">L’appel à <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> spécifie le port de gestion (*ManagementPortStartup.cs*) :</span><span class="sxs-lookup"><span data-stu-id="a0059-576">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="a0059-577">Vous pouvez éviter de créer le fichier *launchSettings.json* dans l’exemple d’application en définissant explicitement les URL et le port de gestion dans le code.</span><span class="sxs-lookup"><span data-stu-id="a0059-577">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="a0059-578">Dans *Program.cs* où <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> est créé, ajoutez un appel à <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> et fournissez le point de terminaison de réponse normal et le point de terminaison de port de gestion de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0059-578">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="a0059-579">Dans *ManagementPortStartup.cs* où <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> est appelé, spécifiez le port de gestion explicitement.</span><span class="sxs-lookup"><span data-stu-id="a0059-579">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="a0059-580">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0059-580">*Program.cs*:</span></span>
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> <span data-ttu-id="a0059-581">*ManagementPortStartup.cs* :</span><span class="sxs-lookup"><span data-stu-id="a0059-581">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="a0059-582">Pour exécuter le scénario de configuration du port de gestion à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0059-582">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="a0059-583">Distribuer une bibliothèque de contrôle d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-583">Distribute a health check library</span></span>

<span data-ttu-id="a0059-584">Pour distribuer une bibliothèque comme un contrôle d’intégrité :</span><span class="sxs-lookup"><span data-stu-id="a0059-584">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="a0059-585">Écrivez un contrôle d’intégrité qui implémente l’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> comme une classe autonome.</span><span class="sxs-lookup"><span data-stu-id="a0059-585">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="a0059-586">La classe peut utiliser une [injection de dépendance](xref:fundamentals/dependency-injection), une activation de type et des [options nommées](xref:fundamentals/configuration/options) pour accéder aux données de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0059-586">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="a0059-587">Dans la logique des contrôles d’intégrité de `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="a0059-587">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="a0059-588">`data1` et `data2` sont utilisés dans la méthode pour exécuter la logique de contrôle d’intégrité de la sonde.</span><span class="sxs-lookup"><span data-stu-id="a0059-588">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="a0059-589">`AccessViolationException` est géré.</span><span class="sxs-lookup"><span data-stu-id="a0059-589">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="a0059-590">Lorsqu’un <xref:System.AccessViolationException> se produit, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> est retourné avec le <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> pour permettre aux utilisateurs de configurer l’état d’échec des contrôles d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-590">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. <span data-ttu-id="a0059-591">Écrivez une méthode d’extension avec des paramètres que l’application consommatrice appelle dans sa méthode `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a0059-591">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="a0059-592">Dans l’exemple qui suit, prenons la signature de méthode de contrôle d’intégrité suivante :</span><span class="sxs-lookup"><span data-stu-id="a0059-592">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="a0059-593">La signature précédente indique que `ExampleHealthCheck` nécessite des données supplémentaires pour traiter la logique de sondage du contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-593">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="a0059-594">Les données sont fournies au délégué qui est utilisé pour créer l’instance de contrôle d’intégrité lorsque le contrôle d’intégrité est inscrit avec une méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="a0059-594">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="a0059-595">Dans l’exemple qui suit, l’appelant spécifie les éléments facultatifs suivants :</span><span class="sxs-lookup"><span data-stu-id="a0059-595">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="a0059-596">Nom du contrôle d’intégrité (`name`).</span><span class="sxs-lookup"><span data-stu-id="a0059-596">health check name (`name`).</span></span> <span data-ttu-id="a0059-597">Si `null`, `example_health_check` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="a0059-597">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="a0059-598">Point de données de chaîne du contrôle d’intégrité (`data1`).</span><span class="sxs-lookup"><span data-stu-id="a0059-598">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="a0059-599">Point de données Integer du contrôle d’intégrité (`data2`).</span><span class="sxs-lookup"><span data-stu-id="a0059-599">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="a0059-600">Si `null`, `1` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="a0059-600">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="a0059-601">État d’échec (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="a0059-601">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="a0059-602">Par défaut, il s’agit de `null`.</span><span class="sxs-lookup"><span data-stu-id="a0059-602">The default is `null`.</span></span> <span data-ttu-id="a0059-603">Si `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) est signalé pour un état d’échec.</span><span class="sxs-lookup"><span data-stu-id="a0059-603">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="a0059-604">Étiquettes (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="a0059-604">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="a0059-605">Serveur de publication des contrôles d’intégrité</span><span class="sxs-lookup"><span data-stu-id="a0059-605">Health Check Publisher</span></span>

<span data-ttu-id="a0059-606">Quand un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> est ajouté au conteneur du service, le système de contrôle d’intégrité exécute régulièrement vos contrôles d’intégrité et appelle `PublishAsync` avec le résultat.</span><span class="sxs-lookup"><span data-stu-id="a0059-606">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="a0059-607">C’est utile dans un scénario impliquant un système de supervision basé sur les envois (push) et nécessitant que chaque processus appelle le système de supervision régulièrement afin de déterminer l’état d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-607">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="a0059-608">L’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> comprend une seule méthode :</span><span class="sxs-lookup"><span data-stu-id="a0059-608">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="a0059-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> vous autorise à définir :</span><span class="sxs-lookup"><span data-stu-id="a0059-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="a0059-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Délai initial appliqué après le démarrage de l’application avant l’exécution des <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span><span class="sxs-lookup"><span data-stu-id="a0059-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="a0059-611">Le retard est appliqué à une seule fois au démarrage et ne s’applique pas aux itérations ultérieures.</span><span class="sxs-lookup"><span data-stu-id="a0059-611">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="a0059-612">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="a0059-612">The default value is five seconds.</span></span>
* <span data-ttu-id="a0059-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Période d' <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> exécution.</span><span class="sxs-lookup"><span data-stu-id="a0059-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="a0059-614">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="a0059-614">The default value is 30 seconds.</span></span>
* <span data-ttu-id="a0059-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Si <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> a `null` la valeur (valeur par défaut), le service d’éditeur de contrôle d’intégrité exécute toutes les vérifications d’intégrité inscrites.</span><span class="sxs-lookup"><span data-stu-id="a0059-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="a0059-616">Pour exécuter un sous-ensemble de contrôles d’intégrité, fournissez une fonction qui filtre l’ensemble de vérifications.</span><span class="sxs-lookup"><span data-stu-id="a0059-616">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="a0059-617">Le prédicat est évalué à chaque période.</span><span class="sxs-lookup"><span data-stu-id="a0059-617">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="a0059-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Délai d’exécution des contrôles d’intégrité de toutes les <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span><span class="sxs-lookup"><span data-stu-id="a0059-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="a0059-619">Utilisez <xref:System.Threading.Timeout.InfiniteTimeSpan> pour une exécution sans délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="a0059-619">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="a0059-620">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="a0059-620">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="a0059-621">Dans la version ASP.NET Core 2.2, le paramètre <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> n’est pas respecté par l’implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ; il définit la valeur de <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span><span class="sxs-lookup"><span data-stu-id="a0059-621">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="a0059-622">Ce problème a été résolu dans ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="a0059-622">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="a0059-623">Dans l’exemple d’application, `ReadinessPublisher` est une implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="a0059-623">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="a0059-624">L’état du contrôle d’intégrité est journalisé pour chaque vérification comme suit :</span><span class="sxs-lookup"><span data-stu-id="a0059-624">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="a0059-625">Information ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ) si l’état des contrôles d’intégrité est <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="a0059-625">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="a0059-626">Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ) si l’État est <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> ou <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="a0059-626">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="a0059-627">Dans l’exemple d’application `LivenessProbeStartup`, la vérification de la disponibilité `StartupHostedService` a un délai de démarrage de deux secondes et exécute la vérification toutes les 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="a0059-627">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="a0059-628">Pour activer l’implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, l’exemple enregistre `ReadinessPublisher` comme un service singleton dans le conteneur [d’injection de dépendance (DI)](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="a0059-628">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="a0059-629">La solution de contournement suivante autorise l’ajout d’une instance <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> au conteneur de service lorsqu’un ou plusieurs autres services hébergés ont déjà été ajoutés à l’application.</span><span class="sxs-lookup"><span data-stu-id="a0059-629">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="a0059-630">Cette solution de contournement n’est pas nécessaire dans ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="a0059-630">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> <span data-ttu-id="a0059-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclut les serveurs de publication pour plusieurs systèmes, notamment [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="a0059-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="a0059-632">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a0059-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="a0059-633">Restreindre les vérifications d’intégrité avec MapWhen</span><span class="sxs-lookup"><span data-stu-id="a0059-633">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="a0059-634">Utilisez <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> pour créer une branche conditionnelle du pipeline des demandes pour les points de terminaison de contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="a0059-634">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="a0059-635">Dans l’exemple suivant, `MapWhen` branche le pipeline de demande pour activer l’intergiciel (middleware) des contrôles d’intégrité si une requête d’extraction est reçue pour le `api/HealthCheck` point de terminaison :</span><span class="sxs-lookup"><span data-stu-id="a0059-635">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="a0059-636">Pour plus d’informations, consultez <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="a0059-636">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
