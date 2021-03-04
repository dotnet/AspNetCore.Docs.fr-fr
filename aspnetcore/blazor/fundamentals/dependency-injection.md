---
title: ASP.NET Core l' Blazor injection de dépendances
author: guardrex
description: Découvrez comment les Blazor applications peuvent injecter des services dans des composants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/19/2020
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
uid: blazor/fundamentals/dependency-injection
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: aefeac2f3a68a669b7c84cbeee2f6a4ec0621f6f
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109674"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Core l' Blazor injection de dépendances

Par [Rainer Stropek](https://www.timecockpit.com) et [Mike Rousos](https://github.com/mjrousos)

L' [injection de dépendances (di)](xref:fundamentals/dependency-injection) est une technique qui consiste à accéder aux services configurés dans un emplacement central :

* Les services inscrits au Framework peuvent être injectés directement dans des composants d' Blazor applications.
* Blazor les applications définissent et enregistrent des services personnalisés et les rendent disponibles dans toute l’application via l’injection de transactions.

## <a name="default-services"></a>Services par défaut

Les services présentés dans le tableau suivant sont couramment utilisés dans les Blazor applications.

| Service | Durée de vie | Description |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Délimité | <p>Fournit des méthodes pour envoyer des requêtes HTTP et recevoir des réponses HTTP d’une ressource identifiée par un URI.</p><p>L’instance de <xref:System.Net.Http.HttpClient> dans une Blazor WebAssembly application utilise le navigateur pour gérer le trafic HTTP en arrière-plan.</p><p>Blazor Server par défaut, les applications n’incluent pas une <xref:System.Net.Http.HttpClient> configuration en tant que service. Fournissez un <xref:System.Net.Http.HttpClient> à une Blazor Server application.</p><p>Pour plus d’informations, consultez <xref:blazor/call-web-api>.</p><p>Un <xref:System.Net.Http.HttpClient> est inscrit en tant que service étendu, et non Singleton. Pour plus d’informations, consultez la section [durée de vie du service](#service-lifetime) .</p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p>**Blazor WebAssembly**: Singleton</p><p>**Blazor Server**: Étendu</p> | Représente une instance d’un Runtime JavaScript dans laquelle les appels JavaScript sont distribués. Pour plus d’informations, consultez <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p>**Blazor WebAssembly**: Singleton</p><p>**Blazor Server**: Étendu</p> | Contient des assistances pour l’utilisation des URI et de l’état de navigation. Pour plus d’informations, consultez [URI et assistance de l’état de navigation](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Un fournisseur de services personnalisé ne fournit pas automatiquement les services par défaut indiqués dans le tableau. Si vous utilisez un fournisseur de services personnalisé et que vous avez besoin de l’un des services répertoriés dans le tableau, ajoutez les services requis au nouveau fournisseur de services.

## <a name="add-services-to-an-app"></a>Ajouter des services à une application

::: zone pivot="webassembly"

Configurez les services de la collection de services de l’application dans la `Program.Main` méthode de `Program.cs` . Dans l’exemple suivant, l' `MyDependency` implémentation est inscrite pour `IMyDependency` :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        ...

        await builder.Build().RunAsync();
    }
}
```

Une fois l’hôte généré, les services sont disponibles à partir de l’étendue de l’injection de comptes racine avant le rendu des composants. Cela peut être utile pour l’exécution de la logique d’initialisation avant le rendu du contenu :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<WeatherService>();
        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

L’hôte fournit une instance de configuration centrale pour l’application. En s’appuyant sur l’exemple précédent, l’URL du service météo est passée d’une source de configuration par défaut (par exemple, `appsettings.json` ) à `InitializeWeatherAsync` :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<WeatherService>();
        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

::: zone-end

::: zone pivot="server"

Après avoir créé une nouvelle application, examinez la `Startup.ConfigureServices` méthode dans `Startup.cs` :

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Une <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , qui est une liste d’objets [descripteur de service](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) , est passée à la méthode. Les services sont ajoutés dans la `ConfigureServices` méthode en fournissant des descripteurs de service à la collection de services. L’exemple suivant illustre le concept avec l' `IDataAccess` interface et son implémentation concrète `DataAccess` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a>Durée de vie du service

Les services peuvent être configurés avec les durées de vie indiquées dans le tableau suivant.

| Durée de vie | Description |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p>Blazor WebAssembly les applications n’ont pas actuellement de concept d’étendues DI. `Scoped`-les services inscrits se comportent comme des `Singleton` services.</p><p>Le Blazor Server modèle d’hébergement prend en charge la `Scoped` durée de vie des requêtes http, mais pas entre les SignalR messages de connexion/circuit parmi les composants chargés sur le client. La Razor partie pages ou MVC de l’application traite normalement les services délimités et recrée les services sur *chaque requête http* lors de la navigation entre les pages ou les vues, ou à partir d’une page ou d’une vue dans un composant. Les services délimités ne sont pas reconstruits lors de la navigation entre les composants du client, où la communication avec le serveur a lieu via la SignalR connexion du circuit de l’utilisateur, et non par le biais de requêtes http. Dans les scénarios de composants suivants sur le client, les services délimités sont reconstruits car un nouveau circuit est créé pour l’utilisateur :</p><ul><li>L’utilisateur ferme la fenêtre du navigateur. L’utilisateur ouvre une nouvelle fenêtre et revient à l’application.</li><li>L’utilisateur ferme le dernier onglet de l’application dans une fenêtre de navigateur. L’utilisateur ouvre un nouvel onglet et revient à l’application.</li><li>L’utilisateur sélectionne le bouton de rechargement/actualisation du navigateur.</li></ul><p>Pour plus d’informations sur la conservation de l’état utilisateur sur les services étendus dans les Blazor Server applications, consultez <xref:blazor/hosting-models?pivots=server> .</p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI crée une *seule instance* du service. Tous les composants qui requièrent un `Singleton` service reçoivent une instance du même service. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Chaque fois qu’un composant obtient une instance d’un `Transient` service à partir du conteneur de service, il reçoit une *nouvelle instance* du service. |

Le système DI est basé sur le système DI dans ASP.NET Core. Pour plus d’informations, consultez <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Demander un service dans un composant

Une fois les services ajoutés à la collection de services, injectez les services dans les composants à l’aide de la [`@inject`](xref:mvc/views/razor#inject) Razor directive, qui a deux paramètres :

* Type : type du service à injecter.
* Propriété : nom de la propriété qui reçoit le service d’application injecté. La propriété ne nécessite pas de création manuelle. Le compilateur crée la propriété.

Pour plus d’informations, consultez <xref:mvc/views/dependency-injection>.

Utilisez plusieurs [`@inject`](xref:mvc/views/razor#inject) instructions pour injecter différents services.

L’exemple suivant montre comment utiliser [`@inject`](xref:mvc/views/razor#inject) . Le service qui implémente `Services.IDataAccess` est injecté dans la propriété du composant `DataRepository` . Notez la manière dont le code utilise uniquement l' `IDataAccess` abstraction :

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/dependency-injection/CustomerList.razor?name=snippet&highlight=2,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/dependency-injection/CustomerList.razor?name=snippet&highlight=2,19)]

::: moniker-end

En interne, la propriété générée ( `DataRepository` ) utilise l' [ `[Inject]` attribut](xref:Microsoft.AspNetCore.Components.InjectAttribute). En règle générale, cet attribut n’est pas utilisé directement. Si une classe de base est requise pour les composants et les propriétés injectées sont également requises pour la classe de base, ajoutez manuellement l' [ `[Inject]` attribut](xref:Microsoft.AspNetCore.Components.InjectAttribute):

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

Dans les composants dérivés de la classe de base, la [`@inject`](xref:mvc/views/razor#inject) directive n’est pas obligatoire. Le <xref:Microsoft.AspNetCore.Components.InjectAttribute> de la classe de base est suffisant :

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Utiliser DI dans les services

Les services complexes peuvent nécessiter des services supplémentaires. Dans l’exemple suivant, `DataAccess` requiert le <xref:System.Net.Http.HttpClient> service par défaut. [`@inject`](xref:mvc/views/razor#inject)(ou l' [ `[Inject]` attribut](xref:Microsoft.AspNetCore.Components.InjectAttribute)) n’est pas disponible pour une utilisation dans les services. L' *injection de constructeur* doit être utilisée à la place. Les services requis sont ajoutés en ajoutant des paramètres au constructeur du service. Lorsque DI crée le service, il reconnaît les services dont il a besoin dans le constructeur et les fournit en conséquence. Dans l’exemple suivant, le constructeur reçoit un <xref:System.Net.Http.HttpClient> via di. <xref:System.Net.Http.HttpClient> est un service par défaut.

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

Conditions préalables pour l’injection de constructeur :

* Un constructeur doit exister dont les arguments peuvent tous être remplis par DI. Les paramètres supplémentaires non couverts par DI sont autorisés s’ils spécifient des valeurs par défaut.
* Le constructeur applicable doit être `public` .
* Un constructeur applicable doit exister. En cas d’ambiguïté, DI lève une exception.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Classes de composants de base de l’utilitaire pour gérer une étendue DI

Dans ASP.NET Core applications, les services délimités sont généralement étendus à la requête actuelle. Une fois la demande terminée, tous les services délimités ou temporaires sont supprimés par le système DI. Dans Blazor Server les applications, l’étendue de la demande est valable pendant la durée de la connexion cliente, ce qui peut entraîner des services transitoires et de portée bien plus longs que prévu. Dans les Blazor WebAssembly applications, les services inscrits avec une durée de vie limitée sont traités comme des singletons, de sorte qu’ils vivent plus longtemps que les services étendus dans les applications de ASP.net Core standard.

> [!NOTE]
> Pour détecter les services transitoires distants dans une application, consultez la section détecter les services temporaires à usage [temporaire](#detect-transient-disposables) .

Une approche qui limite la durée de vie d’un service dans Blazor les applications est l’utilisation du <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type. <xref:Microsoft.AspNetCore.Components.OwningComponentBase> est un type abstrait dérivé de <xref:Microsoft.AspNetCore.Components.ComponentBase> qui crée une étendue di correspondant à la durée de vie du composant. À l’aide de cette étendue, il est possible d’utiliser l’injection de services avec une durée de vie limitée et de les faire vivre aussi longtemps que le composant. Lorsque le composant est détruit, les services du fournisseur de services étendus du composant sont également supprimés. Cela peut être utile pour les services qui :

* Doit être réutilisé dans un composant, car la durée de vie temporaire n’est pas appropriée.
* Ne doit pas être partagé entre les composants, car la durée de vie Singleton n’est pas appropriée.

Deux versions du <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type sont disponibles :

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> est un enfant abstrait et jetable du <xref:Microsoft.AspNetCore.Components.ComponentBase> type avec une propriété protégée <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> de type <xref:System.IServiceProvider> . Ce fournisseur peut être utilisé pour résoudre les services dont la portée est limitée à la durée de vie du composant.

  Les services d’injection de services injectés dans le composant à l’aide de [`@inject`](xref:mvc/views/razor#inject) ou l' [ `[Inject]` attribut](xref:Microsoft.AspNetCore.Components.InjectAttribute) ne sont pas créés dans l’étendue du composant. Pour utiliser l’étendue du composant, les services doivent être résolus à l’aide <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> de ou de <xref:System.IServiceProvider.GetService%2A> . Les dépendances de tous les services résolus à l’aide du <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> fournisseur sont fournies à partir de cette même étendue.

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/dependency-injection/Preferences.razor?name=snippet&highlight=3,20-21)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/dependency-injection/Preferences.razor?name=snippet&highlight=3,20-21)]

  ::: moniker-end

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> dérive de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> et ajoute une <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> propriété qui retourne une instance de `T` à partir du fournisseur di étendu. Ce type est un moyen pratique d’accéder aux services délimités sans utiliser une instance de <xref:System.IServiceProvider> lorsqu’un service principal est requis par l’application à partir du conteneur di à l’aide de l’étendue du composant. La <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriété est disponible. par conséquent, l’application peut récupérer des services d’autres types, si nécessaire.

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a>Utilisation d’un DbContext Entity Framework Core (EF Core) à partir de DI

Pour plus d’informations, consultez <xref:blazor/blazor-server-ef-core>.

## <a name="detect-transient-disposables"></a>Détecter les supprimables temporaires

Les exemples suivants montrent comment détecter des services transitoires jetables dans une application qui doit utiliser <xref:Microsoft.AspNetCore.Components.OwningComponentBase> . Pour plus d’informations, consultez la section [classes du composant de base de l’utilitaire pour gérer une étendue de l’injection de](#utility-base-component-classes-to-manage-a-di-scope) données.

::: zone pivot="webassembly"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

Le `TransientDisposable` dans l’exemple suivant est détecté ( `Program.cs` ) :

::: moniker range=">= aspnetcore-5.0"

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.DetectIncorrectUsageOfTransients();
        builder.RootComponents.Add<App>("#app");

        builder.Services.AddTransient<TransientDisposable>();
        builder.Services.AddScoped(sp =>
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();
        host.EnableTransientDisposableDetection();
        await host.RunAsync();
    }
}

public class TransientDisposable : IDisposable
{
    public void Dispose() => throw new NotImplementedException();
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.DetectIncorrectUsageOfTransients();
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient<TransientDisposable>();
        builder.Services.AddScoped(sp =>
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();
        host.EnableTransientDisposableDetection();
        await host.RunAsync();
    }
}

public class TransientDisposable : IDisposable
{
    public void Dispose() => throw new NotImplementedException();
}
```

::: moniker-end

::: zone-end

::: zone pivot="server"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

Ajoutez l’espace de noms pour <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> à `Program.cs` :

```csharp
using Microsoft.Extensions.DependencyInjection;
```

Dans `Program.CreateHostBuilder` de `Program.cs` :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .DetectIncorrectUsageOfTransients()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Le `TransientDependency` dans l’exemple suivant est détecté ( `Startup.cs` ) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
    services.AddServerSideBlazor();
    services.AddSingleton<WeatherForecastService>();
    services.AddTransient<TransientDependency>();
    services.AddTransient<ITransitiveTransientDisposableDependency, 
        TransitiveTransientDisposableDependency>();
}

public class TransitiveTransientDisposableDependency 
    : ITransitiveTransientDisposableDependency, IDisposable
{
    public void Dispose() { }
}

public interface ITransitiveTransientDisposableDependency
{
}

public class TransientDependency
{
    private readonly ITransitiveTransientDisposableDependency 
        _transitiveTransientDisposableDependency;

    public TransientDependency(ITransitiveTransientDisposableDependency 
        transitiveTransientDisposableDependency)
    {
        _transitiveTransientDisposableDependency = 
            transitiveTransientDisposableDependency;
    }
}
```

::: zone-end

L’application peut enregistrer des exceptions transitoires temporaires sans lever d’exception. Toutefois, si vous tentez de résoudre un résultat à usage unique transitoire dans <xref:System.InvalidOperationException> , comme le montre l’exemple suivant.

`Pages/TransientDisposable.razor`:

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

Naviguez jusqu’au `TransientDisposable` composant à l’adresse `/transient-disposable` et une <xref:System.InvalidOperationException> exception est levée lorsque l’infrastructure tente de construire une instance de `TransientDisposable` :

> System. InvalidOperationException : tentative de résolution des TransientDisposable de service temporaires temporaires dans une étendue incorrecte. Utilisez une \<T> classe de base de composant « OwningComponentBase » pour le service « t » que vous essayez de résoudre.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/dependency-injection>
* [`IDisposable` conseils pour les instances temporaires et partagées](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
