---
title: ASP.NET Core l' Blazor injection de dépendances
author: guardrex
description: Découvrez comment les Blazor applications peuvent injecter des services dans des composants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
ms.openlocfilehash: a636498ee3fedbbf0fe021407a718f95a02f70c3
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234164"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a>ASP.NET Core l' Blazor injection de dépendances

Par [Rainer Stropek](https://www.timecockpit.com) et [Mike Rousos](https://github.com/mjrousos)

Blazor prend en charge l' [injection de dépendances (di)](xref:fundamentals/dependency-injection). Les applications peuvent utiliser des services intégrés en les injectant dans des composants. Les applications peuvent également définir et inscrire des services personnalisés et les rendre disponibles dans toute l’application via DI.

La méthode DI est une technique permettant d’accéder à des services configurés dans un emplacement central. Cela peut être utile dans les Blazor applications pour :

* Partager une seule instance d’une classe de service sur de nombreux composants, appelé service *Singleton* .
* Découplez les composants des classes de service concrètes à l’aide d’abstractions de référence. Par exemple, considérez une interface `IDataAccess` pour accéder aux données dans l’application. L’interface est implémentée par une `DataAccess` classe concrète et inscrite en tant que service dans le conteneur de services de l’application. Quand un composant utilise DI pour recevoir une `IDataAccess` implémentation, le composant n’est pas couplé au type concret. L’implémentation peut être permutée, peut-être pour une implémentation factice dans les tests unitaires.

## <a name="default-services"></a>Services par défaut

Les services par défaut sont automatiquement ajoutés à la collection de services de l’application.

| Service | Durée de vie | Description |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Délimité | Fournit des méthodes pour envoyer des requêtes HTTP et recevoir des réponses HTTP d’une ressource identifiée par un URI.<br><br>L’instance de <xref:System.Net.Http.HttpClient> dans une Blazor WebAssembly application utilise le navigateur pour gérer le trafic HTTP en arrière-plan.<br><br>Blazor Server par défaut, les applications n’incluent pas une <xref:System.Net.Http.HttpClient> configuration en tant que service. Fournissez un <xref:System.Net.Http.HttpClient> à une Blazor Server application.<br><br>Pour plus d'informations, consultez <xref:blazor/call-web-api>. |
| <xref:Microsoft.JSInterop.IJSRuntime> | Singleton ( Blazor WebAssembly )<br>Étendu ( Blazor Server ) | Représente une instance d’un Runtime JavaScript dans laquelle les appels JavaScript sont distribués. Pour plus d'informations, consultez <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton ( Blazor WebAssembly )<br>Étendu ( Blazor Server ) | Contient des assistances pour l’utilisation des URI et de l’état de navigation. Pour plus d’informations, consultez [URI et assistance de l’état de navigation](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Un fournisseur de services personnalisé ne fournit pas automatiquement les services par défaut indiqués dans le tableau. Si vous utilisez un fournisseur de services personnalisé et que vous avez besoin de l’un des services répertoriés dans le tableau, ajoutez les services requis au nouveau fournisseur de services.

## <a name="add-services-to-an-app"></a>Ajouter des services à une application

### Blazor WebAssembly

Configurez les services de la collection de services de l’application dans la `Main` méthode de `Program.cs` . Dans l’exemple suivant, l' `MyDependency` implémentation est inscrite pour `IMyDependency` :

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        await builder.Build().RunAsync();
    }
}
```

Une fois l’hôte généré, les services sont accessibles à partir de l’étendue de l’injection de comptes racine avant le rendu des composants. Cela peut être utile pour l’exécution de la logique d’initialisation avant le rendu du contenu :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

L’hôte fournit également une instance de configuration centrale pour l’application. En s’appuyant sur l’exemple précédent, l’URL du service météo est passée d’une source de configuration par défaut (par exemple, `appsettings.json` ) à `InitializeWeatherAsync` :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

Après avoir créé une nouvelle application, examinez la `Startup.ConfigureServices` méthode :

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Une <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , qui est une liste d’objets descripteurs de service (), est passée à la méthode <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> . Les services sont ajoutés dans la `ConfigureServices` méthode en fournissant des descripteurs de service à la collection de services. L’exemple suivant illustre le concept avec l' `IDataAccess` interface et son implémentation concrète `DataAccess` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Durée de vie du service

Les services peuvent être configurés avec les durées de vie indiquées dans le tableau suivant.

| Durée de vie | Description |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly les applications n’ont pas actuellement de concept d’étendues DI. `Scoped`-les services inscrits se comportent comme des `Singleton` services. Toutefois, le Blazor Server modèle d’hébergement prend en charge la `Scoped` durée de vie. Dans Blazor Server les applications, l’inscription d’un service étendu est limitée à la *connexion* . Pour cette raison, il est préférable d’utiliser les services délimités pour les services qui doivent être étendus à l’utilisateur actuel, même si l’objectif actuel est d’exécuter côté client dans le navigateur. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI crée une *seule instance* du service. Tous les composants qui requièrent un `Singleton` service reçoivent une instance du même service. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Chaque fois qu’un composant obtient une instance d’un `Transient` service à partir du conteneur de service, il reçoit une *nouvelle instance* du service. |

Le système DI est basé sur le système DI dans ASP.NET Core. Pour plus d'informations, consultez <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Demander un service dans un composant

Une fois les services ajoutés à la collection de services, injectez les services dans les composants à l’aide de la directive [ \@ Inject](xref:mvc/views/razor#inject) Razor . [`@inject`](xref:mvc/views/razor#inject) a deux paramètres :

* Type : type du service à injecter.
* Propriété : nom de la propriété qui reçoit le service d’application injecté. La propriété ne nécessite pas de création manuelle. Le compilateur crée la propriété.

Pour plus d'informations, consultez <xref:mvc/views/dependency-injection>.

Utilisez plusieurs [`@inject`](xref:mvc/views/razor#inject) instructions pour injecter différents services.

L’exemple suivant montre comment utiliser [`@inject`](xref:mvc/views/razor#inject) . Le service qui implémente `Services.IDataAccess` est injecté dans la propriété du composant `DataRepository` . Notez la manière dont le code utilise uniquement l' `IDataAccess` abstraction :

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

En interne, la propriété générée ( `DataRepository` ) utilise l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut. En règle générale, cet attribut n’est pas utilisé directement. Si une classe de base est requise pour les composants et les propriétés injectées sont également requises pour la classe de base, ajoutez manuellement l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut :

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

Les services complexes peuvent nécessiter des services supplémentaires. Dans l’exemple précédent, `DataAccess` peut nécessiter le <xref:System.Net.Http.HttpClient> service par défaut. [`@inject`](xref:mvc/views/razor#inject) (ou l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut) n’est pas disponible pour une utilisation dans les services. L' *injection de constructeur* doit être utilisée à la place. Les services requis sont ajoutés en ajoutant des paramètres au constructeur du service. Lorsque DI crée le service, il reconnaît les services dont il a besoin dans le constructeur et les fournit en conséquence. Dans l’exemple suivant, le constructeur reçoit un <xref:System.Net.Http.HttpClient> via di. <xref:System.Net.Http.HttpClient> est un service par défaut.

```csharp
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

  Les services d’injection de services injectés dans le composant à l’aide de [`@inject`](xref:mvc/views/razor#inject) ou l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut ne sont pas créés dans l’étendue du composant. Pour utiliser l’étendue du composant, les services doivent être résolus à l’aide <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> de ou de <xref:System.IServiceProvider.GetService%2A> . Les dépendances de tous les services résolus à l’aide du <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> fournisseur sont fournies à partir de cette même étendue.

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

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

Pour plus d'informations, consultez <xref:blazor/blazor-server-ef-core>.

## <a name="detect-transient-disposables"></a>Détecter les supprimables temporaires

Les exemples suivants montrent comment détecter des services transitoires jetables dans une application qui doit utiliser <xref:Microsoft.AspNetCore.Components.OwningComponentBase> . Pour plus d’informations, consultez la section [classes du composant de base de l’utilitaire pour gérer une étendue de l’injection de](#utility-base-component-classes-to-manage-a-di-scope) données.

### Blazor WebAssembly

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

Le `TransientDisposable` dans l’exemple suivant est détecté ( `Program.cs` ) :

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

`Program`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

Le `TransientDependency` dans l’exemple suivant est détecté ( `Startup.cs` ) :

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/dependency-injection>
* [`IDisposable` conseils pour les instances temporaires et partagées](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
