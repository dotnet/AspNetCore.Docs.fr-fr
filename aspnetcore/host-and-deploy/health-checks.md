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
# <a name="health-checks-in-aspnet-core"></a>Contrôles d’intégrité dans ASP.NET Core

Par [Glenn Condron](https://github.com/glennc)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core offre un intergiciel et des bibliothèques de contrôle d’intégrité pour signaler l’intégrité des composants d’infrastructure d’application.

Les contrôles d’intégrité sont exposés par une application comme des points de terminaison HTTP. Les points de terminaison de vérification d’intégrité peuvent être configurés pour de nombreux scénarios de supervision en temps réel :

* Les sondes d’intégrité peuvent être utilisées par les orchestrateurs de conteneurs et les équilibreurs de charge afin de vérifier l’état d’une application. Par exemple, un orchestrateur de conteneurs peut répondre à un résultat de non intégrité en arrêtant le déploiement ou en redémarrant un conteneur. Face à une application non saine, l’équilibreur de charge peut réagir en redirigeant le trafic vers une instance saine.
* L’utilisation de la mémoire, des disques et des autres ressources de serveur physique peut être supervisée dans le cadre d’un contrôle d’intégrité.
* Les contrôles d’intégrité peuvent tester les dépendances d’une application, telles que les bases de données et les points de terminaison de service externes, dans le but de vérifier leur disponibilité et leur bon fonctionnement.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

L’exemple d’application comprend des exemples pour les scénarios décrits dans cette rubrique. Pour exécuter l’exemple d’application selon un scénario donné, utilisez la commande [dotnet run](/dotnet/core/tools/dotnet-run) dans un interpréteur de commandes, à partir du dossier du projet. Pour plus d’informations sur l’utilisation de l’exemple d’application, consultez le fichier *README.md* de l’exemple d’application ainsi que les descriptions de scénarios de cette rubrique.

## <a name="prerequisites"></a>Prérequis

Les contrôles d’intégrité sont généralement utilisés avec un service de supervision externe ou un orchestrateur de conteneurs pour vérifier l’état d’une application. Avant d’ajouter des contrôles d’intégrité à une application, vous devez décider du système de supervision à utiliser. Le système de supervision détermine les types de contrôles d’intégrité qui doivent être créés ainsi que la configuration de leurs points de terminaison.

Le package [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) est référencé implicitement pour les applications ASP.net core. Pour effectuer des contrôles d’intégrité à l’aide de Entity Framework Core, ajoutez une référence de package au package [Microsoft. extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .

L’exemple d’application fournit du code de démarrage pour illustrer les contrôles d’intégrité de plusieurs scénarios. Le scénario [probe de la base de données](#database-probe) vérifie l’intégrité d’une connexion de base de données à l’aide d’[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Le scénario [Sondage DbContext](#entity-framework-core-dbcontext-probe) vérifie une base de données à l’aide d’un `DbContext` EF Core. Pour explorer les scénarios de base de données, l’exemple d’application :

* Crée une base de données et fournit sa chaîne de connexion dans le *appsettings.json* fichier.
* Possède les références de package suivantes dans son fichier de projet :
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.

Un autre scénario de contrôle d’intégrité montre comment filtrer des contrôles d’intégrité selon un port de gestion. L’exemple d’application vous oblige à créer un fichier *Properties/launchSettings.json* comprenant l’URL de gestion et le port de gestion. Pour plus d’informations, consultez la section [Filtrer par port](#filter-by-port).

## <a name="basic-health-probe"></a>Sondage d’intégrité de base

Pour de nombreuses applications, un sondage d’intégrité de base qui signale la disponibilité d’une application pour le traitement des requêtes (*liveness*) suffit à découvrir l’état de l’application.

La configuration de base inscrit les services de contrôle d’intégrité et appelle l’intergiciel (middleware) des contrôles d’intégrité pour répondre à un point de terminaison d’URL avec une réponse d’intégrité. Par défaut, aucun contrôle d’intégrité n’est inscrit pour tester les dépendances ou le sous-système. L’application est considérée comme saine si elle est capable de répondre à l’URL de point de terminaison de contrôle d’intégrité. L’enregistreur de réponse par défaut écrit l’état (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) sous forme de texte en clair qu’il renvoie au client, indiquant si l’état est [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) ou [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. Créez un point de terminaison de contrôle d’intégrité en appelant `MapHealthChecks` dans `Startup.Configure` .

Dans l’exemple d’application, le point de terminaison de contrôle d’intégrité est créé au niveau de `/health` (*BasicStartup.cs*) :

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

Pour exécuter le scénario de configuration de base à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Exemple Docker

[Docker](xref:host-and-deploy/docker/index) fournit une directive `HEALTHCHECK` intégrée qui peut être utilisée pour vérifier l’état d’une application utilisant la configuration de contrôle d’intégrité de base :

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Créer des contrôles d’intégrité

Les contrôles d’intégrité sont créés via l’implémentation de l’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>. La méthode <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> retourne un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> qui indique l’état d’intégrité comme étant `Healthy`, `Degraded` ou `Unhealthy`. Le résultat est écrit sous la forme de texte en clair avec un code d’état configurable (la configuration est décrite dans la section [Options de contrôle d’intégrité](#health-check-options)). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> peut également retourner des paires clé-valeur facultatives.

La `ExampleHealthCheck` classe suivante illustre la disposition d’un contrôle d’intégrité. La logique des contrôles d’intégrité est placée dans la `CheckHealthAsync` méthode. L’exemple suivant définit une variable factice, `healthCheckResultHealthy` , sur `true` . Si la valeur de `healthCheckResultHealthy` est définie sur `false` , l’état [HealthCheckResult. inhealth](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) est retourné.

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

## <a name="register-health-check-services"></a>Inscrire les services de contrôle d’intégrité

Le `ExampleHealthCheck` type est ajouté aux services de contrôle d’intégrité avec <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> dans `Startup.ConfigureServices` :

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

La surcharge <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> de l’exemple suivant définit l’état d’échec (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) de manière à être signalé lorsque le contrôle d’intégrité signale un échec. Si l’état d’échec est défini sur `null` (par défaut), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) est signalé. Cette surcharge est utile pour les créateurs de bibliothèque, dans les cas où l’état d’échec indiqué par la bibliothèque est appliqué par l’application lorsqu’un échec est signalé par le contrôle d’intégrité, si l’implémentation de ce dernier respecte le paramètre.

Des *étiquettes* peuvent être utilisées pour filtrer les contrôles d’intégrité (cette procédure est décrite plus loin dans la section [Filtrer les contrôles d’intégrité](#filter-health-checks)).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> peut également exécuter une fonction lambda. Dans l’exemple suivant, le nom du contrôle d’intégrité est spécifié en tant que `Example`, et la vérification retourne toujours un état sain :

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

Appelez <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> pour passer des arguments à une implémentation de contrôle d’intégrité. Dans l’exemple suivant, `TestHealthCheckWithArgs` accepte un entier et une chaîne à utiliser lorsque <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> est appelé :

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

`TestHealthCheckWithArgs` est inscrit en appelant `AddTypeActivatedCheck` avec l’entier et la chaîne passés à l’implémentation :

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a>Utiliser le routage des contrôles d’intégrité

Dans `Startup.Configure` , appelez `MapHealthChecks` sur le générateur de points de terminaison avec l’URL de point de terminaison ou le chemin d’accès relatif :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a>Exiger un hôte

Appelez `RequireHost` pour spécifier un ou plusieurs hôtes autorisés pour le point de terminaison de contrôle d’intégrité. Les hôtes doivent être au format Unicode plutôt que Punycode et peuvent inclure un port. Si une collection n’est pas fournie, tous les hôtes sont acceptés.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

Pour plus d’informations, consultez la section [Filtrer par port](#filter-by-port).

### <a name="require-authorization"></a>Exiger une autorisation

Appelez `RequireAuthorization` pour exécuter l’intergiciel (middleware) d’autorisation sur le point de terminaison de demande de contrôle d’intégrité. Une `RequireAuthorization` surcharge accepte une ou plusieurs stratégies d’autorisation. Si aucune stratégie n’est fournie, la stratégie d’autorisation par défaut est utilisée.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a>Activer les requêtes d’origines différentes

Bien que l’exécution manuelle de contrôles d’intégrité à partir d’un navigateur ne soit pas un scénario d’utilisation courant, l’intergiciel (middleware) CORS peut être activé en appelant `RequireCors` sur des points de terminaison de contrôle d’intégrité. Une `RequireCors` surcharge accepte un délégué de générateur de stratégie cors ( `CorsPolicyBuilder` ) ou un nom de stratégie. Si aucune stratégie n’est fournie, la stratégie CORS par défaut est utilisée. Pour plus d’informations, consultez <xref:security/cors>.

## <a name="health-check-options"></a>Options de contrôle d’intégrité

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> permet de personnaliser le comportement du contrôle d’intégrité :

* [Filtrer les contrôles d’intégrité](#filter-health-checks)
* [Personnaliser le code d’état HTTP](#customize-the-http-status-code)
* [Supprimer les en-têtes de cache](#suppress-cache-headers)
* [Personnaliser la sortie](#customize-output)

### <a name="filter-health-checks"></a>Filtrer les contrôles d’intégrité

Par défaut, l’intergiciel (middleware) des contrôles d’intégrité exécute toutes les vérifications d’intégrité inscrites. Pour exécuter un sous-ensemble de contrôles d’intégrité, fournissez une fonction qui retourne une valeur booléenne à l’option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>. Dans l’exemple suivant, le contrôle d’intégrité `Bar` est filtré en fonction de son étiquette (`bar_tag`) dans l’instruction conditionnelle de la fonction, où `true` est retourné uniquement si la propriété <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> du contrôle d’intégrité correspond à `foo_tag` ou à `baz_tag` :

Dans `Startup.ConfigureServices` :

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

Dans `Startup.Configure` , le `Predicate` filtre le contrôle d’intégrité « bar ». Seules les opérations foo et Baz sont exécutées.

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

### <a name="customize-the-http-status-code"></a>Personnaliser le code d’état HTTP

Utilisez <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> pour personnaliser le mappage de l’état d’intégrité aux codes d’état HTTP. Les affectations <xref:Microsoft.AspNetCore.Http.StatusCodes> suivantes sont les valeurs par défaut utilisées par le middleware. Vous pouvez modifier les valeurs de code d’état selon vos besoins.

Dans `Startup.Configure` :

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

### <a name="suppress-cache-headers"></a>Supprimer les en-têtes de cache

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> contrôle si l’intergiciel (middleware) de contrôles d’intégrité ajoute des en-têtes HTTP à une réponse de sondage pour empêcher la mise en cache des réponses. Si la valeur est `false` (par défaut), le middleware définit ou substitue les en-têtes `Cache-Control`, `Expires` et `Pragma` afin d’empêcher la mise en cache de la réponse. Si la valeur est `true`, le middleware ne modifie pas les en-têtes de cache de la réponse.

Dans `Startup.Configure` :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a>Personnaliser la sortie

Dans `Startup.Configure` , définissez l’option [HealthCheckOptions. ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) sur un délégué pour l’écriture de la réponse :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

Le délégué par défaut écrit une réponse minimale constituée de texte en clair, avec la valeur de chaîne [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Les délégués personnalisés suivants génèrent une réponse JSON personnalisée.

Le premier exemple de l’exemple d’application montre comment utiliser <xref:System.Text.Json?displayProperty=fullName> :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

Le deuxième exemple montre comment utiliser [Newtonsoft.Jssur](https://www.nuget.org/packages/Newtonsoft.Json/):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

Dans l’exemple d’application, commentez la `SYSTEM_TEXT_JSON` [directive de préprocesseur](xref:index#preprocessor-directives-in-sample-code) dans *CustomWriterStartup.cs* pour activer la `Newtonsoft.Json` version de `WriteResponse` .

L’API contrôles d’intégrité ne fournit pas de prise en charge intégrée des formats de retour JSON complexes, car le format est spécifique à votre choix de système de surveillance. Personnalisez la réponse dans les exemples précédents en fonction des besoins. Pour plus d’informations sur la sérialisation JSON avec `System.Text.Json` , consultez [sérialisation et désérialisation de JSON dans .net](/dotnet/standard/serialization/system-text-json-how-to).

## <a name="database-probe"></a>Sondage de base de données

Un contrôle d’intégrité peut spécifier une requête de base de données à exécuter en tant que test booléen pour indiquer si la base de données répond normalement.

L’exemple d’application utilise [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), une bibliothèque de vérification d’intégrité pour les applications ASP.NET Core, pour effectuer une vérification d’intégrité sur une base de données SQL Server. `AspNetCore.Diagnostics.HealthChecks` exécute une demande `SELECT 1` sur la base de données pour confirmer que la connexion à la base de données est saine.

> [!WARNING]
> Lorsque vous vérifiez une connexion de base de données à l’aide d’une requête, choisissez une requête qui est retournée rapidement. L’utilisation d’une requête risque néanmoins d’entraîner une surcharge de la base de données et d’en diminuer les performances. Dans la plupart des cas, il n’est pas nécessaire d’utiliser une requête de test. Il suffit simplement d’établir une connexion à la base de données. Si vous avez besoin d’exécuter une requête, choisissez une requête SELECT simple, telle que `SELECT 1`.

Inclure une référence de package à [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Fournissez une chaîne de connexion de base de données valide dans le *appsettings.json* fichier de l’exemple d’application. L’application utilise une base de données SQL Server nommée `HealthCheckSample` :

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. L’exemple d’application appelle la méthode `AddSqlServer` avec la chaîne de connexion de la base de données (*DbHealthStartup.cs*) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Pour créer un point de terminaison de contrôle d’intégrité, appelez `MapHealthChecks` dans `Startup.Configure` :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Pour exécuter le scénario de sondage d’une base de données à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.

## <a name="entity-framework-core-dbcontext-probe"></a>Sondage DbContext Entity Framework Core

La vérification `DbContext` vérifie que l’application peut communiquer avec la base de données configurée pour un `DbContext` EF Core. La vérification `DbContext` est prise en charge dans les applications qui :

* Utilisent [Entity Framework (EF) Core](/ef/core/).
* Incluent une référence de package à [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` inscrit un contrôle d’intégrité pour un `DbContext`. Le `DbContext` est fourni en tant que `TContext` à la méthode. Une surcharge est disponible pour configurer l’état d’échec, les étiquettes et une requête de test personnalisée.

Par défaut :

* `DbContextHealthCheck` appelle la méthode `CanConnectAsync` d’EF Core. Vous pouvez choisir quelle opération doit être exécutée lors du contrôle d’intégrité à l’aide des surcharges de la méthode `AddDbContextCheck`.
* Le nom du contrôle d’intégrité correspond à celui du type `TContext`.

Dans l’exemple d’application, `AppDbContext` est fourni à `AddDbContextCheck` et enregistré en tant que service dans `Startup.ConfigureServices` (*DbContextHealthStartup.cs*) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Pour créer un point de terminaison de contrôle d’intégrité, appelez `MapHealthChecks` dans `Startup.Configure` :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Pour exécuter le scénario de sondage `DbContext` à l’aide de l’exemple d’application, vérifiez que la base de données spécifiée par le la chaîne de connexion ne se trouve pas déjà dans l’instance SQL Server. Si la base de données existe, supprimez-la.

Exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario dbcontext
```

Une fois que l’application est en cours d’exécution, vérifiez l’état d’intégrité en envoyant une requête au point de terminaison `/health` dans un navigateur. La base de données et `AppDbContext` n’existent pas, donc l’application fournit la réponse suivante :

```
Unhealthy
```

Déclenchez l’exemple d’application pour créer la base de données. Envoyez une requête à `/createdatabase`. L’application répond :

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Envoyez une requête au point de terminaison `/health`. La base de données et le contexte existent, donc l’application répond :

```
Healthy
```

Déclenchez l’exemple d’application pour supprimer la base de données. Envoyez une requête à `/deletedatabase`. L’application répond :

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Envoyez une requête au point de terminaison `/health`. L’application fournit une réponse non intégrité :

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Séparer les sondages probe readiness et probe liveness

Dans certains scénarios d’hébergement, une paire de contrôles d’intégrité est utilisée pour distinguer deux états d’application :

* L’état de *préparation* indique si l’application s’exécute normalement mais n’est pas prête à recevoir des demandes.
* L' *activité* indique si une application s’est arrêtée et doit être redémarrée.

Prenons l’exemple suivant : une application doit télécharger un fichier de configuration volumineux avant de pouvoir traiter les demandes. Nous ne voulons pas que l’application soit redémarrée si le téléchargement initial échoue parce que l’application peut réessayer de télécharger le fichier plusieurs fois. Nous utilisons une *sonde d’activité* pour décrire l’activité du processus, aucune vérification supplémentaire n’est effectuée. Nous voulons également empêcher l’envoi de demandes à l’application avant la réussite du téléchargement du fichier de configuration. Nous utilisons une *sonde de préparation* pour indiquer un État « non prêt » jusqu’à ce que le téléchargement aboutisse et que l’application soit prête à recevoir des demandes.

L’exemple d’application contient un contrôle d’intégrité permettant de signaler l’achèvement d’une tâche de démarrage de longue durée dans un [service hébergé](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck` expose la propriété `StartupTaskCompleted`, que le service hébergé peut définir sur `true` lorsque sa tâche de longue durée est terminée (*StartupHostedServiceHealthCheck.cs*) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

La tâche de longue durée en arrière-plan est démarrée par un [service hébergé](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*). À la fin de la tâche, `StartupHostedServiceHealthCheck.StartupTaskCompleted` est défini sur `true` :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Le contrôle d’intégrité est inscrit auprès de <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> dans `Startup.ConfigureServices` en même temps que le service hébergé. Étant donné que le service hébergé doit définir la propriété sur le contrôle d’intégrité, le contrôle d’intégrité est également inscrit dans le conteneur du service (*LivenessProbeStartup.cs*) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Un point de terminaison de contrôle d’intégrité est créé en appelant `MapHealthChecks` dans `Startup.Configure` . Dans l’exemple d’application, les points de terminaison de contrôle d’intégrité sont créés à l’adresse :

* `/health/ready` pour la vérification de la disponibilité. Le test qui permet de vérifier si l’application est prête filtre les contrôles d’intégrité pour n’afficher que celui dont l’étiquette est `ready`.
* `/health/live` pour la vérification de l’activité. La vérification d’activité filtre le `StartupHostedServiceHealthCheck` en retournant `false` dans le [prédicat HealthCheckOptions.](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (pour plus d’informations, consultez [contrôles d’intégrité de filtre](#filter-health-checks)).

Dans l’exemple de code suivant :

* La vérification de disponibilité utilise toutes les vérifications enregistrées avec la balise « Ready ».
* Le `Predicate` exclut toutes les vérifications et retourne un 200-OK.

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

Pour exécuter le scénario de configuration readiness/liveness à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario liveness
```

Dans un navigateur, accédez à `/health/ready` plusieurs fois jusqu’à ce que 15 secondes soient écoulées. Le contrôle d’intégrité signale *Unhealthy* pendant les 15 premières secondes. Après 15 secondes, le point de terminaison signale *Healthy*, ce qui reflète l’achèvement de la tâche de longue durée exécutée par le service hébergé.

Cet exemple crée également un éditeur de vérification de l’intégrité (implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) qui exécute la première vérification de disponibilité avec un délai de deux secondes. Pour plus d’informations, consultez la section [Éditeur de vérification de l’intégrité](#health-check-publisher).

### <a name="kubernetes-example"></a>Exemple Kubernetes

Dans un environnement tel que [Kubernetes](https://kubernetes.io/), il peut être utile d’utiliser séparément le test permettant de savoir si la l’application est prête et celui visant à savoir si l’application est active. Dans Kubernetes, une application peut devoir effectuer une tâche de démarrage de longue durée avant d’accepter des requêtes, telles qu’un test de la disponibilité de la base de données sous-jacente. L’utilisation séparée des deux tests permet à l’orchestrateur de faire la distinction entre une application qui fonctionne mais qui n’est pas encore prête, et une application qui n’a pas pu démarrer. Pour plus d’informations sur les sondages probe readiness et probe liveness dans Kubernetes, consultez [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) dans la documentation Kubernetes.

L’exemple suivant montre une configuration probe readiness Kubernetes :

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Sonde basée sur les métriques avec un enregistreur de réponse personnalisé

L’exemple d’application montre un contrôle d’intégrité de mémoire avec un enregistreur de réponse personnalisé.

`MemoryHealthCheck` signale un état détérioré si l’application utilise plus de mémoire que le seuil défini (1 Go dans l’exemple d’application). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> inclut des informations de récupérateur de mémoire pour l’application (*MemoryHealthCheck.cs*) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. Au lieu d’activer le contrôle d’intégrité en le passant à <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` est inscrit en tant que service. Tous les services <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> inscrits sont disponibles pour les services de contrôle d’intégrité et le middleware. Nous vous recommandons d’inscrire les services de contrôle d’intégrité en tant que services Singleton.

Dans *CustomWriterStartup.cs* de l’exemple d’application :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Un point de terminaison de contrôle d’intégrité est créé en appelant `MapHealthChecks` dans `Startup.Configure` . Un `WriteResponse` délégué est fourni au <propriété Microsoft. AspNetCore. Diagnostics. HealthChecks. HealthCheckOptions. ResponseWriter> pour générer une réponse JSON personnalisée lors de l’exécution du contrôle d’intégrité :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

Le `WriteResponse` délégué met en forme le `CompositeHealthCheckResult` en un objet JSON et génère la sortie JSON pour la réponse de contrôle d’intégrité. Pour plus d’informations, consultez la section [personnaliser la sortie](#customize-output) .

Pour exécuter le sondage basé sur les métriques avec l’enregistreur de réponse personnalisé à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclut des scénarios de vérification de l’intégrité basés sur des métriques, notamment des vérifications du stockage sur disque et de la vivacité maximale des valeurs.
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.

## <a name="filter-by-port"></a>Filtrer par port

Appelez `RequireHost` `MapHealthChecks` avec un modèle d’URL qui spécifie un port pour restreindre les demandes de contrôle d’intégrité au port spécifié. Ceci est généralement utilisé dans les environnements de conteneurs pour exposer un port aux services de supervision.

L’exemple d’application configure le port à l’aide du [fournisseur de configuration des variables d’environnement](xref:fundamentals/configuration/index#environment-variables). Le port est défini dans le fichier *launchSettings.json*, puis transmis au fournisseur de configuration via une variable d’environnement. Vous devez également configurer le serveur pour écouter les requêtes qui arrivent au port de gestion.

Pour utiliser l’exemple d’application dans le but d’illustrer la configuration du port de gestion, créez le fichier *launchSettings.json* dans un dossier *Propriétés*.

Les *Propriétés/launchSettings.js* suivantes du fichier dans l’exemple d’application ne sont pas incluses dans les fichiers projet de l’exemple d’application et doivent être créées manuellement :

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

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. Créez un point de terminaison de contrôle d’intégrité en appelant `MapHealthChecks` dans `Startup.Configure` .

Dans l’exemple d’application, un appel à `RequireHost` sur le point de terminaison dans `Startup.Configure` spécifie le port de gestion de la configuration :

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

Les points de terminaison sont créés dans l’exemple d’application dans `Startup.Configure` . Dans l’exemple de code suivant :

* La vérification de disponibilité utilise toutes les vérifications enregistrées avec la balise « Ready ».
* Le `Predicate` exclut toutes les vérifications et retourne un 200-OK.

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
> Vous pouvez éviter de créer le *launchSettings.jssur* le fichier dans l’exemple d’application en définissant le port de gestion explicitement dans le code. Dans *Program.cs* où <xref:Microsoft.Extensions.Hosting.HostBuilder> est créé, ajoutez un appel à <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> et fournissez le point de terminaison du port de gestion de l’application. Dans `Configure` *ManagementPortStartup.cs*, spécifiez le port de gestion avec `RequireHost` :
>
> *Program.cs*:
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
> *ManagementPortStartup.cs* :
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

Pour exécuter le scénario de configuration du port de gestion à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Distribuer une bibliothèque de contrôle d’intégrité

Pour distribuer une bibliothèque comme un contrôle d’intégrité :

1. Écrivez un contrôle d’intégrité qui implémente l’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> comme une classe autonome. La classe peut utiliser une [injection de dépendance](xref:fundamentals/dependency-injection), une activation de type et des [options nommées](xref:fundamentals/configuration/options) pour accéder aux données de configuration.

   Dans la logique des contrôles d’intégrité de `CheckHealthAsync` :

   * `data1` et `data2` sont utilisés dans la méthode pour exécuter la logique de contrôle d’intégrité de la sonde.
   * `AccessViolationException` est géré.

   Lorsqu’un <xref:System.AccessViolationException> se produit, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> est retourné avec le <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> pour permettre aux utilisateurs de configurer l’état d’échec des contrôles d’intégrité.

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

1. Écrivez une méthode d’extension avec des paramètres que l’application consommatrice appelle dans sa méthode `Startup.Configure`. Dans l’exemple qui suit, prenons la signature de méthode de contrôle d’intégrité suivante :

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   La signature précédente indique que `ExampleHealthCheck` nécessite des données supplémentaires pour traiter la logique de sondage du contrôle d’intégrité. Les données sont fournies au délégué qui est utilisé pour créer l’instance de contrôle d’intégrité lorsque le contrôle d’intégrité est inscrit avec une méthode d’extension. Dans l’exemple qui suit, l’appelant spécifie les éléments facultatifs suivants :

   * Nom du contrôle d’intégrité (`name`). Si `null`, `example_health_check` est utilisé.
   * Point de données de chaîne du contrôle d’intégrité (`data1`).
   * Point de données Integer du contrôle d’intégrité (`data2`). Si `null`, `1` est utilisé.
   * État d’échec (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Par défaut, il s’agit de `null`. Si `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) est signalé pour un état d’échec.
   * Étiquettes (`IEnumerable<string>`).

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

## <a name="health-check-publisher"></a>Serveur de publication des contrôles d’intégrité

Quand un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> est ajouté au conteneur du service, le système de contrôle d’intégrité exécute régulièrement vos contrôles d’intégrité et appelle `PublishAsync` avec le résultat. C’est utile dans un scénario impliquant un système de supervision basé sur les envois (push) et nécessitant que chaque processus appelle le système de supervision régulièrement afin de déterminer l’état d’intégrité.

L’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> comprend une seule méthode :

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> vous autorise à définir :

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Délai initial appliqué après le démarrage de l’application avant l’exécution des <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances. Le retard est appliqué à une seule fois au démarrage et ne s’applique pas aux itérations ultérieures. La valeur par défaut est de cinq secondes.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Période d' <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> exécution. La valeur par défaut est de 30 secondes.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Si <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> a `null` la valeur (valeur par défaut), le service d’éditeur de contrôle d’intégrité exécute toutes les vérifications d’intégrité inscrites. Pour exécuter un sous-ensemble de contrôles d’intégrité, fournissez une fonction qui filtre l’ensemble de vérifications. Le prédicat est évalué à chaque période.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Délai d’exécution des contrôles d’intégrité de toutes les <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances. Utilisez <xref:System.Threading.Timeout.InfiniteTimeSpan> pour une exécution sans délai d’attente. La valeur par défaut est de 30 secondes.

Dans l’exemple d’application, `ReadinessPublisher` est une implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. L’état du contrôle d’intégrité est journalisé pour chaque vérification au niveau du journal :

* Information ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ) si l’état des contrôles d’intégrité est <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .
* Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ) si l’État est <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> ou <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

Dans l’exemple d’application `LivenessProbeStartup`, la vérification de la disponibilité `StartupHostedService` a un délai de démarrage de deux secondes et exécute la vérification toutes les 30 secondes. Pour activer l’implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, l’exemple enregistre `ReadinessPublisher` comme un service singleton dans le conteneur [d’injection de dépendance (DI)](xref:fundamentals/dependency-injection) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclut les serveurs de publication pour plusieurs systèmes, notamment [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.

## <a name="restrict-health-checks-with-mapwhen"></a>Restreindre les vérifications d’intégrité avec MapWhen

Utilisez <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> pour créer une branche conditionnelle du pipeline des demandes pour les points de terminaison de contrôle d’intégrité.

Dans l’exemple suivant, `MapWhen` branche le pipeline de demande pour activer l’intergiciel (middleware) des contrôles d’intégrité si une requête d’extraction est reçue pour le `api/HealthCheck` point de terminaison :

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

Pour plus d’informations, consultez <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core offre un intergiciel et des bibliothèques de contrôle d’intégrité pour signaler l’intégrité des composants d’infrastructure d’application.

Les contrôles d’intégrité sont exposés par une application comme des points de terminaison HTTP. Les points de terminaison de vérification d’intégrité peuvent être configurés pour de nombreux scénarios de supervision en temps réel :

* Les sondes d’intégrité peuvent être utilisées par les orchestrateurs de conteneurs et les équilibreurs de charge afin de vérifier l’état d’une application. Par exemple, un orchestrateur de conteneurs peut répondre à un résultat de non intégrité en arrêtant le déploiement ou en redémarrant un conteneur. Face à une application non saine, l’équilibreur de charge peut réagir en redirigeant le trafic vers une instance saine.
* L’utilisation de la mémoire, des disques et des autres ressources de serveur physique peut être supervisée dans le cadre d’un contrôle d’intégrité.
* Les contrôles d’intégrité peuvent tester les dépendances d’une application, telles que les bases de données et les points de terminaison de service externes, dans le but de vérifier leur disponibilité et leur bon fonctionnement.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

L’exemple d’application comprend des exemples pour les scénarios décrits dans cette rubrique. Pour exécuter l’exemple d’application selon un scénario donné, utilisez la commande [dotnet run](/dotnet/core/tools/dotnet-run) dans un interpréteur de commandes, à partir du dossier du projet. Pour plus d’informations sur l’utilisation de l’exemple d’application, consultez le fichier *README.md* de l’exemple d’application ainsi que les descriptions de scénarios de cette rubrique.

## <a name="prerequisites"></a>Prérequis

Les contrôles d’intégrité sont généralement utilisés avec un service de supervision externe ou un orchestrateur de conteneurs pour vérifier l’état d’une application. Avant d’ajouter des contrôles d’intégrité à une application, vous devez décider du système de supervision à utiliser. Le système de supervision détermine les types de contrôles d’intégrité qui doivent être créés ainsi que la configuration de leurs points de terminaison.

Référencez le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou ajoutez une référence de package au package [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks).

L’exemple d’application fournit du code de démarrage pour illustrer les contrôles d’intégrité de plusieurs scénarios. Le scénario [probe de la base de données](#database-probe) vérifie l’intégrité d’une connexion de base de données à l’aide d’[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Le scénario [Sondage DbContext](#entity-framework-core-dbcontext-probe) vérifie une base de données à l’aide d’un `DbContext` EF Core. Pour explorer les scénarios de base de données, l’exemple d’application :

* Crée une base de données et fournit sa chaîne de connexion dans le *appsettings.json* fichier.
* Possède les références de package suivantes dans son fichier de projet :
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.

Un autre scénario de contrôle d’intégrité montre comment filtrer des contrôles d’intégrité selon un port de gestion. L’exemple d’application vous oblige à créer un fichier *Properties/launchSettings.json* comprenant l’URL de gestion et le port de gestion. Pour plus d’informations, consultez la section [Filtrer par port](#filter-by-port).

## <a name="basic-health-probe"></a>Sondage d’intégrité de base

Pour de nombreuses applications, un sondage d’intégrité de base qui signale la disponibilité d’une application pour le traitement des requêtes (*liveness*) suffit à découvrir l’état de l’application.

La configuration de base inscrit les services de contrôle d’intégrité et appelle l’intergiciel (middleware) des contrôles d’intégrité pour répondre à un point de terminaison d’URL avec une réponse d’intégrité. Par défaut, aucun contrôle d’intégrité n’est inscrit pour tester les dépendances ou le sous-système. L’application est considérée comme saine si elle est capable de répondre à l’URL de point de terminaison de contrôle d’intégrité. L’enregistreur de réponse par défaut écrit l’état (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) sous forme de texte en clair qu’il renvoie au client, indiquant si l’état est [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) ou [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. Ajoutez un point de terminaison pour l’intergiciel de contrôles d’intégrité avec <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> dans le pipeline de traitement des requêtes de `Startup.Configure` .

Dans l’exemple d’application, le point de terminaison de contrôle d’intégrité est créé au niveau de `/health` (*BasicStartup.cs*) :

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

Pour exécuter le scénario de configuration de base à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Exemple Docker

[Docker](xref:host-and-deploy/docker/index) fournit une directive `HEALTHCHECK` intégrée qui peut être utilisée pour vérifier l’état d’une application utilisant la configuration de contrôle d’intégrité de base :

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Créer des contrôles d’intégrité

Les contrôles d’intégrité sont créés via l’implémentation de l’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>. La méthode <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> retourne un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> qui indique l’état d’intégrité comme étant `Healthy`, `Degraded` ou `Unhealthy`. Le résultat est écrit sous la forme de texte en clair avec un code d’état configurable (la configuration est décrite dans la section [Options de contrôle d’intégrité](#health-check-options)). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> peut également retourner des paires clé-valeur facultatives.

### <a name="example-health-check"></a>Exemple de contrôle d’intégrité

La `ExampleHealthCheck` classe suivante illustre la disposition d’un contrôle d’intégrité. La logique des contrôles d’intégrité est placée dans la `CheckHealthAsync` méthode. L’exemple suivant définit une variable factice, `healthCheckResultHealthy` , sur `true` . Si la valeur de `healthCheckResultHealthy` est définie sur `false` , l’état [HealthCheckResult. inhealth](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) est retourné.

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

### <a name="register-health-check-services"></a>Inscrire les services de contrôle d’intégrité

Le `ExampleHealthCheck` type est ajouté aux services de contrôle d’intégrité dans `Startup.ConfigureServices` avec <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> :

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

La surcharge <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> de l’exemple suivant définit l’état d’échec (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) de manière à être signalé lorsque le contrôle d’intégrité signale un échec. Si l’état d’échec est défini sur `null` (par défaut), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) est signalé. Cette surcharge est utile pour les créateurs de bibliothèque, dans les cas où l’état d’échec indiqué par la bibliothèque est appliqué par l’application lorsqu’un échec est signalé par le contrôle d’intégrité, si l’implémentation de ce dernier respecte le paramètre.

Des *étiquettes* peuvent être utilisées pour filtrer les contrôles d’intégrité (cette procédure est décrite plus loin dans la section [Filtrer les contrôles d’intégrité](#filter-health-checks)).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> peut également exécuter une fonction lambda. Dans l' `Startup.ConfigureServices` exemple suivant, le nom du contrôle d’intégrité est spécifié comme `Example` et le contrôle retourne toujours un état sain :

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a>Utiliser Health Checks Middleware

Dans `Startup.Configure`, appelez <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> dans le pipeline de traitement avec l’URL de point de terminaison ou le chemin relatif :

```csharp
app.UseHealthChecks("/health");
```

Si les contrôles d’intégrité doivent écouter un port en particulier, utilisez une surcharge de <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> pour définir le port (cette procédure est décrite plus loin dans la section [Filtrer par port](#filter-by-port)) :

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a>Options de contrôle d’intégrité

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> permet de personnaliser le comportement du contrôle d’intégrité :

* [Filtrer les contrôles d’intégrité](#filter-health-checks)
* [Personnaliser le code d’état HTTP](#customize-the-http-status-code)
* [Supprimer les en-têtes de cache](#suppress-cache-headers)
* [Personnaliser la sortie](#customize-output)

### <a name="filter-health-checks"></a>Filtrer les contrôles d’intégrité

Par défaut, l’intergiciel (middleware) des contrôles d’intégrité exécute toutes les vérifications d’intégrité inscrites. Pour exécuter un sous-ensemble de contrôles d’intégrité, fournissez une fonction qui retourne une valeur booléenne à l’option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>. Dans l’exemple suivant, le contrôle d’intégrité `Bar` est filtré en fonction de son étiquette (`bar_tag`) dans l’instruction conditionnelle de la fonction, où `true` est retourné uniquement si la propriété <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> du contrôle d’intégrité correspond à `foo_tag` ou à `baz_tag` :

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

### <a name="customize-the-http-status-code"></a>Personnaliser le code d’état HTTP

Utilisez <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> pour personnaliser le mappage de l’état d’intégrité aux codes d’état HTTP. Les affectations <xref:Microsoft.AspNetCore.Http.StatusCodes> suivantes sont les valeurs par défaut utilisées par le middleware. Vous pouvez modifier les valeurs de code d’état selon vos besoins.

Dans `Startup.Configure` :

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

### <a name="suppress-cache-headers"></a>Supprimer les en-têtes de cache

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> contrôle si l’intergiciel (middleware) de contrôles d’intégrité ajoute des en-têtes HTTP à une réponse de sondage pour empêcher la mise en cache des réponses. Si la valeur est `false` (par défaut), le middleware définit ou substitue les en-têtes `Cache-Control`, `Expires` et `Pragma` afin d’empêcher la mise en cache de la réponse. Si la valeur est `true`, le middleware ne modifie pas les en-têtes de cache de la réponse.

Dans `Startup.Configure` :

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a>Personnaliser la sortie

L’option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> obtient ou définit un délégué utilisé pour écrire la réponse. Le délégué par défaut écrit une réponse minimale constituée de texte en clair, avec la valeur de chaîne [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).

Dans `Startup.Configure` :

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

Le délégué par défaut écrit une réponse minimale constituée de texte en clair, avec la valeur de chaîne [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Le délégué personnalisé suivant, `WriteResponse` , génère une réponse JSON personnalisée :

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

Le système de contrôle d’intégrité ne fournit pas de prise en charge intégrée des formats de retour JSON complexes, car le format est spécifique à votre choix de système de surveillance. N’hésitez pas à personnaliser le `JObject` dans l’exemple précédent si nécessaire pour répondre à vos besoins.

## <a name="database-probe"></a>Sondage de base de données

Un contrôle d’intégrité peut spécifier une requête de base de données à exécuter en tant que test booléen pour indiquer si la base de données répond normalement.

L’exemple d’application utilise [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), une bibliothèque de vérification d’intégrité pour les applications ASP.NET Core, pour effectuer une vérification d’intégrité sur une base de données SQL Server. `AspNetCore.Diagnostics.HealthChecks` exécute une demande `SELECT 1` sur la base de données pour confirmer que la connexion à la base de données est saine.

> [!WARNING]
> Lorsque vous vérifiez une connexion de base de données à l’aide d’une requête, choisissez une requête qui est retournée rapidement. L’utilisation d’une requête risque néanmoins d’entraîner une surcharge de la base de données et d’en diminuer les performances. Dans la plupart des cas, il n’est pas nécessaire d’utiliser une requête de test. Il suffit simplement d’établir une connexion à la base de données. Si vous avez besoin d’exécuter une requête, choisissez une requête SELECT simple, telle que `SELECT 1`.

Inclure une référence de package à [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Fournissez une chaîne de connexion de base de données valide dans le *appsettings.json* fichier de l’exemple d’application. L’application utilise une base de données SQL Server nommée `HealthCheckSample` :

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. L’exemple d’application appelle la méthode `AddSqlServer` avec la chaîne de connexion de la base de données (*DbHealthStartup.cs*) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Call Health Checks middleware dans le pipeline de traitement des applications dans `Startup.Configure` :

```csharp
app.UseHealthChecks("/health");
```

Pour exécuter le scénario de sondage d’une base de données à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.

## <a name="entity-framework-core-dbcontext-probe"></a>Sondage DbContext Entity Framework Core

La vérification `DbContext` vérifie que l’application peut communiquer avec la base de données configurée pour un `DbContext` EF Core. La vérification `DbContext` est prise en charge dans les applications qui :

* Utilisent [Entity Framework (EF) Core](/ef/core/).
* Incluent une référence de package à [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` inscrit un contrôle d’intégrité pour un `DbContext`. Le `DbContext` est fourni en tant que `TContext` à la méthode. Une surcharge est disponible pour configurer l’état d’échec, les étiquettes et une requête de test personnalisée.

Par défaut :

* `DbContextHealthCheck` appelle la méthode `CanConnectAsync` d’EF Core. Vous pouvez choisir quelle opération doit être exécutée lors du contrôle d’intégrité à l’aide des surcharges de la méthode `AddDbContextCheck`.
* Le nom du contrôle d’intégrité correspond à celui du type `TContext`.

Dans l’exemple d’application, `AppDbContext` est fourni à `AddDbContextCheck` et enregistré en tant que service dans `Startup.ConfigureServices` (*DbContextHealthStartup.cs*) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Dans l’exemple d’application, `UseHealthChecks` ajoute l’intergiciel de contrôles d’intégrité dans `Startup.Configure` .

```csharp
app.UseHealthChecks("/health");
```

Pour exécuter le scénario de sondage `DbContext` à l’aide de l’exemple d’application, vérifiez que la base de données spécifiée par le la chaîne de connexion ne se trouve pas déjà dans l’instance SQL Server. Si la base de données existe, supprimez-la.

Exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario dbcontext
```

Une fois que l’application est en cours d’exécution, vérifiez l’état d’intégrité en envoyant une requête au point de terminaison `/health` dans un navigateur. La base de données et `AppDbContext` n’existent pas, donc l’application fournit la réponse suivante :

```
Unhealthy
```

Déclenchez l’exemple d’application pour créer la base de données. Envoyez une requête à `/createdatabase`. L’application répond :

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Envoyez une requête au point de terminaison `/health`. La base de données et le contexte existent, donc l’application répond :

```
Healthy
```

Déclenchez l’exemple d’application pour supprimer la base de données. Envoyez une requête à `/deletedatabase`. L’application répond :

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Envoyez une requête au point de terminaison `/health`. L’application fournit une réponse non intégrité :

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Séparer les sondages probe readiness et probe liveness

Dans certains scénarios d’hébergement, une paire de contrôles d’intégrité est utilisée pour distinguer deux états d’application :

* L’état de *préparation* indique si l’application s’exécute normalement mais n’est pas prête à recevoir des demandes.
* L' *activité* indique si une application s’est arrêtée et doit être redémarrée.

Prenons l’exemple suivant : une application doit télécharger un fichier de configuration volumineux avant de pouvoir traiter les demandes. Nous ne voulons pas que l’application soit redémarrée si le téléchargement initial échoue parce que l’application peut réessayer de télécharger le fichier plusieurs fois. Nous utilisons une *sonde d’activité* pour décrire l’activité du processus, aucune vérification supplémentaire n’est effectuée. Nous voulons également empêcher l’envoi de demandes à l’application avant la réussite du téléchargement du fichier de configuration. Nous utilisons une *sonde de préparation* pour indiquer un État « non prêt » jusqu’à ce que le téléchargement aboutisse et que l’application soit prête à recevoir des demandes.

L’exemple d’application contient un contrôle d’intégrité permettant de signaler l’achèvement d’une tâche de démarrage de longue durée dans un [service hébergé](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck` expose la propriété `StartupTaskCompleted`, que le service hébergé peut définir sur `true` lorsque sa tâche de longue durée est terminée (*StartupHostedServiceHealthCheck.cs*) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

La tâche de longue durée en arrière-plan est démarrée par un [service hébergé](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*). À la fin de la tâche, `StartupHostedServiceHealthCheck.StartupTaskCompleted` est défini sur `true` :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Le contrôle d’intégrité est inscrit auprès de <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> dans `Startup.ConfigureServices` en même temps que le service hébergé. Étant donné que le service hébergé doit définir la propriété sur le contrôle d’intégrité, le contrôle d’intégrité est également inscrit dans le conteneur du service (*LivenessProbeStartup.cs*) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Call Health Checks middleware dans le pipeline de traitement des applications dans `Startup.Configure` . Dans l’exemple d’application, les points de terminaison de contrôle d’intégrité sont créés au niveau de `/health/ready` pour vérifier si l’application est prête, et au niveau de `/health/live` pour vérifier si l’application est active. Le test qui permet de vérifier si l’application est prête filtre les contrôles d’intégrité pour n’afficher que celui dont l’étiquette est `ready`. Le test qui permet de vérifier si l’application est active exclut le `StartupHostedServiceHealthCheck` en retournant `false` dans [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (pour plus d’informations, consultez [Filtrer les contrôles d’intégrité](#filter-health-checks)) :

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

Pour exécuter le scénario de configuration readiness/liveness à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario liveness
```

Dans un navigateur, accédez à `/health/ready` plusieurs fois jusqu’à ce que 15 secondes soient écoulées. Le contrôle d’intégrité signale *Unhealthy* pendant les 15 premières secondes. Après 15 secondes, le point de terminaison signale *Healthy*, ce qui reflète l’achèvement de la tâche de longue durée exécutée par le service hébergé.

Cet exemple crée également un éditeur de vérification de l’intégrité (implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) qui exécute la première vérification de disponibilité avec un délai de deux secondes. Pour plus d’informations, consultez la section [Éditeur de vérification de l’intégrité](#health-check-publisher).

### <a name="kubernetes-example"></a>Exemple Kubernetes

Dans un environnement tel que [Kubernetes](https://kubernetes.io/), il peut être utile d’utiliser séparément le test permettant de savoir si la l’application est prête et celui visant à savoir si l’application est active. Dans Kubernetes, une application peut devoir effectuer une tâche de démarrage de longue durée avant d’accepter des requêtes, telles qu’un test de la disponibilité de la base de données sous-jacente. L’utilisation séparée des deux tests permet à l’orchestrateur de faire la distinction entre une application qui fonctionne mais qui n’est pas encore prête, et une application qui n’a pas pu démarrer. Pour plus d’informations sur les sondages probe readiness et probe liveness dans Kubernetes, consultez [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) dans la documentation Kubernetes.

L’exemple suivant montre une configuration probe readiness Kubernetes :

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Sonde basée sur les métriques avec un enregistreur de réponse personnalisé

L’exemple d’application montre un contrôle d’intégrité de mémoire avec un enregistreur de réponse personnalisé.

`MemoryHealthCheck` signale un État non sain si l’application utilise plus d’un seuil de mémoire donné (1 Go dans l’exemple d’application). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> inclut des informations de récupérateur de mémoire pour l’application (*MemoryHealthCheck.cs*) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. Au lieu d’activer le contrôle d’intégrité en le passant à <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` est inscrit en tant que service. Tous les services <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> inscrits sont disponibles pour les services de contrôle d’intégrité et le middleware. Nous vous recommandons d’inscrire les services de contrôle d’intégrité en tant que services Singleton.

Dans l’exemple d’application (*CustomWriterStartup.cs*) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Call Health Checks middleware dans le pipeline de traitement des applications dans `Startup.Configure` . Un délégué `WriteResponse` est fourni à la propriété `ResponseWriter` pour produire une réponse JSON personnalisée lorsque le contrôle d’intégrité est exécuté :

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

La méthode `WriteResponse` formate le `CompositeHealthCheckResult` en un objet JSON et génère une sortie JSON pour la réponse du contrôle d’intégrité :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Pour exécuter le sondage basé sur les métriques avec l’enregistreur de réponse personnalisé à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclut des scénarios de vérification de l’intégrité basés sur des métriques, notamment des vérifications du stockage sur disque et de la vivacité maximale des valeurs.
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.

## <a name="filter-by-port"></a>Filtrer par port

Si vous appelez <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> avec un port, les requêtes de contrôle d’intégrité seront limitées au port spécifié. Ceci est généralement utilisé dans les environnements de conteneurs pour exposer un port aux services de supervision.

L’exemple d’application configure le port à l’aide du [fournisseur de configuration des variables d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Le port est défini dans le fichier *launchSettings.json*, puis transmis au fournisseur de configuration via une variable d’environnement. Vous devez également configurer le serveur pour écouter les requêtes qui arrivent au port de gestion.

Pour utiliser l’exemple d’application dans le but d’illustrer la configuration du port de gestion, créez le fichier *launchSettings.json* dans un dossier *Propriétés*.

Les *Propriétés/launchSettings.js* suivantes du fichier dans l’exemple d’application ne sont pas incluses dans les fichiers projet de l’exemple d’application et doivent être créées manuellement :

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

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. L’appel à <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> spécifie le port de gestion (*ManagementPortStartup.cs*) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> Vous pouvez éviter de créer le fichier *launchSettings.json* dans l’exemple d’application en définissant explicitement les URL et le port de gestion dans le code. Dans *Program.cs* où <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> est créé, ajoutez un appel à <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> et fournissez le point de terminaison de réponse normal et le point de terminaison de port de gestion de l’application. Dans *ManagementPortStartup.cs* où <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> est appelé, spécifiez le port de gestion explicitement.
>
> *Program.cs*:
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
> *ManagementPortStartup.cs* :
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

Pour exécuter le scénario de configuration du port de gestion à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Distribuer une bibliothèque de contrôle d’intégrité

Pour distribuer une bibliothèque comme un contrôle d’intégrité :

1. Écrivez un contrôle d’intégrité qui implémente l’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> comme une classe autonome. La classe peut utiliser une [injection de dépendance](xref:fundamentals/dependency-injection), une activation de type et des [options nommées](xref:fundamentals/configuration/options) pour accéder aux données de configuration.

   Dans la logique des contrôles d’intégrité de `CheckHealthAsync` :

   * `data1` et `data2` sont utilisés dans la méthode pour exécuter la logique de contrôle d’intégrité de la sonde.
   * `AccessViolationException` est géré.

   Lorsqu’un <xref:System.AccessViolationException> se produit, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> est retourné avec le <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> pour permettre aux utilisateurs de configurer l’état d’échec des contrôles d’intégrité.

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

1. Écrivez une méthode d’extension avec des paramètres que l’application consommatrice appelle dans sa méthode `Startup.Configure`. Dans l’exemple qui suit, prenons la signature de méthode de contrôle d’intégrité suivante :

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   La signature précédente indique que `ExampleHealthCheck` nécessite des données supplémentaires pour traiter la logique de sondage du contrôle d’intégrité. Les données sont fournies au délégué qui est utilisé pour créer l’instance de contrôle d’intégrité lorsque le contrôle d’intégrité est inscrit avec une méthode d’extension. Dans l’exemple qui suit, l’appelant spécifie les éléments facultatifs suivants :

   * Nom du contrôle d’intégrité (`name`). Si `null`, `example_health_check` est utilisé.
   * Point de données de chaîne du contrôle d’intégrité (`data1`).
   * Point de données Integer du contrôle d’intégrité (`data2`). Si `null`, `1` est utilisé.
   * État d’échec (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Par défaut, il s’agit de `null`. Si `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) est signalé pour un état d’échec.
   * Étiquettes (`IEnumerable<string>`).

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

## <a name="health-check-publisher"></a>Serveur de publication des contrôles d’intégrité

Quand un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> est ajouté au conteneur du service, le système de contrôle d’intégrité exécute régulièrement vos contrôles d’intégrité et appelle `PublishAsync` avec le résultat. C’est utile dans un scénario impliquant un système de supervision basé sur les envois (push) et nécessitant que chaque processus appelle le système de supervision régulièrement afin de déterminer l’état d’intégrité.

L’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> comprend une seule méthode :

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> vous autorise à définir :

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Délai initial appliqué après le démarrage de l’application avant l’exécution des <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances. Le retard est appliqué à une seule fois au démarrage et ne s’applique pas aux itérations ultérieures. La valeur par défaut est de cinq secondes.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Période d' <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> exécution. La valeur par défaut est de 30 secondes.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Si <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> a `null` la valeur (valeur par défaut), le service d’éditeur de contrôle d’intégrité exécute toutes les vérifications d’intégrité inscrites. Pour exécuter un sous-ensemble de contrôles d’intégrité, fournissez une fonction qui filtre l’ensemble de vérifications. Le prédicat est évalué à chaque période.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Délai d’exécution des contrôles d’intégrité de toutes les <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances. Utilisez <xref:System.Threading.Timeout.InfiniteTimeSpan> pour une exécution sans délai d’attente. La valeur par défaut est de 30 secondes.

> [!WARNING]
> Dans la version ASP.NET Core 2.2, le paramètre <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> n’est pas respecté par l’implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ; il définit la valeur de <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>. Ce problème a été résolu dans ASP.NET Core 3,0.

Dans l’exemple d’application, `ReadinessPublisher` est une implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. L’état du contrôle d’intégrité est journalisé pour chaque vérification comme suit :

* Information ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ) si l’état des contrôles d’intégrité est <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .
* Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ) si l’État est <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> ou <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

Dans l’exemple d’application `LivenessProbeStartup`, la vérification de la disponibilité `StartupHostedService` a un délai de démarrage de deux secondes et exécute la vérification toutes les 30 secondes. Pour activer l’implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, l’exemple enregistre `ReadinessPublisher` comme un service singleton dans le conteneur [d’injection de dépendance (DI)](xref:fundamentals/dependency-injection) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> La solution de contournement suivante autorise l’ajout d’une instance <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> au conteneur de service lorsqu’un ou plusieurs autres services hébergés ont déjà été ajoutés à l’application. Cette solution de contournement n’est pas nécessaire dans ASP.NET Core 3,0.
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
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclut les serveurs de publication pour plusieurs systèmes, notamment [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas géré ou pris en charge par Microsoft.

## <a name="restrict-health-checks-with-mapwhen"></a>Restreindre les vérifications d’intégrité avec MapWhen

Utilisez <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> pour créer une branche conditionnelle du pipeline des demandes pour les points de terminaison de contrôle d’intégrité.

Dans l’exemple suivant, `MapWhen` branche le pipeline de demande pour activer l’intergiciel (middleware) des contrôles d’intégrité si une requête d’extraction est reçue pour le `api/HealthCheck` point de terminaison :

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

Pour plus d’informations, consultez <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end
