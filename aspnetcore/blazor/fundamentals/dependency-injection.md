---
title: "ASP.NET Core l' Blazor injection de dépendances"
author: guardrex
description: 'Découvrez comment les Blazor applications peuvent injecter des services dans des composants.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: 32228cc98b4650d5871369511808e519a4f65be4
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343674"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="696c7-103">ASP.NET Core l' Blazor injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="696c7-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="696c7-104">Par [Rainer Stropek](https://www.timecockpit.com) et [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="696c7-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="696c7-105">Blazor prend en charge l' [injection de dépendances (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="696c7-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="696c7-106">Les applications peuvent utiliser des services intégrés en les injectant dans des composants.</span><span class="sxs-lookup"><span data-stu-id="696c7-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="696c7-107">Les applications peuvent également définir et inscrire des services personnalisés et les rendre disponibles dans toute l’application via DI.</span><span class="sxs-lookup"><span data-stu-id="696c7-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="696c7-108">La méthode DI est une technique permettant d’accéder à des services configurés dans un emplacement central.</span><span class="sxs-lookup"><span data-stu-id="696c7-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="696c7-109">Cela peut être utile dans les Blazor applications pour :</span><span class="sxs-lookup"><span data-stu-id="696c7-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="696c7-110">Partager une seule instance d’une classe de service sur de nombreux composants, appelé service *Singleton* .</span><span class="sxs-lookup"><span data-stu-id="696c7-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="696c7-111">Découplez les composants des classes de service concrètes à l’aide d’abstractions de référence.</span><span class="sxs-lookup"><span data-stu-id="696c7-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="696c7-112">Par exemple, considérez une interface `IDataAccess` pour accéder aux données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="696c7-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="696c7-113">L’interface est implémentée par une `DataAccess` classe concrète et inscrite en tant que service dans le conteneur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="696c7-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="696c7-114">Quand un composant utilise DI pour recevoir une `IDataAccess` implémentation, le composant n’est pas couplé au type concret.</span><span class="sxs-lookup"><span data-stu-id="696c7-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="696c7-115">L’implémentation peut être permutée, peut-être pour une implémentation factice dans les tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="696c7-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="696c7-116">Services par défaut</span><span class="sxs-lookup"><span data-stu-id="696c7-116">Default services</span></span>

<span data-ttu-id="696c7-117">Les services par défaut sont automatiquement ajoutés à la collection de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="696c7-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="696c7-118">Service</span><span class="sxs-lookup"><span data-stu-id="696c7-118">Service</span></span> | <span data-ttu-id="696c7-119">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="696c7-119">Lifetime</span></span> | <span data-ttu-id="696c7-120">Description</span><span class="sxs-lookup"><span data-stu-id="696c7-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="696c7-121">Délimité</span><span class="sxs-lookup"><span data-stu-id="696c7-121">Scoped</span></span> | <span data-ttu-id="696c7-122">Fournit des méthodes pour envoyer des requêtes HTTP et recevoir des réponses HTTP d’une ressource identifiée par un URI.</span><span class="sxs-lookup"><span data-stu-id="696c7-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="696c7-123">L’instance de <xref:System.Net.Http.HttpClient> dans une Blazor WebAssembly application utilise le navigateur pour gérer le trafic HTTP en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="696c7-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="696c7-124">Blazor Server par défaut, les applications n’incluent pas une <xref:System.Net.Http.HttpClient> configuration en tant que service.</span><span class="sxs-lookup"><span data-stu-id="696c7-124">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="696c7-125">Fournissez un <xref:System.Net.Http.HttpClient> à une Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="696c7-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="696c7-126">Pour plus d'informations, consultez <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="696c7-126">For more information, see <xref:blazor/call-web-api>.</span></span><br><br><span data-ttu-id="696c7-127">Un <xref:System.Net.Http.HttpClient> est inscrit en tant que service étendu, et non Singleton.</span><span class="sxs-lookup"><span data-stu-id="696c7-127">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="696c7-128">Pour plus d’informations, consultez la section [durée de vie du service](#service-lifetime) .</span><span class="sxs-lookup"><span data-stu-id="696c7-128">For more information, see the [Service lifetime](#service-lifetime) section.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="696c7-129">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="696c7-129">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="696c7-130">Étendu ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="696c7-130">Scoped (Blazor Server)</span></span> | <span data-ttu-id="696c7-131">Représente une instance d’un Runtime JavaScript dans laquelle les appels JavaScript sont distribués.</span><span class="sxs-lookup"><span data-stu-id="696c7-131">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="696c7-132">Pour plus d'informations, consultez <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="696c7-132">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="696c7-133">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="696c7-133">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="696c7-134">Étendu ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="696c7-134">Scoped (Blazor Server)</span></span> | <span data-ttu-id="696c7-135">Contient des assistances pour l’utilisation des URI et de l’état de navigation.</span><span class="sxs-lookup"><span data-stu-id="696c7-135">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="696c7-136">Pour plus d’informations, consultez [URI et assistance de l’état de navigation](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="696c7-136">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="696c7-137">Un fournisseur de services personnalisé ne fournit pas automatiquement les services par défaut indiqués dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="696c7-137">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="696c7-138">Si vous utilisez un fournisseur de services personnalisé et que vous avez besoin de l’un des services répertoriés dans le tableau, ajoutez les services requis au nouveau fournisseur de services.</span><span class="sxs-lookup"><span data-stu-id="696c7-138">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="696c7-139">Ajouter des services à une application</span><span class="sxs-lookup"><span data-stu-id="696c7-139">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="696c7-140">Configurez les services de la collection de services de l’application dans la `Main` méthode de `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="696c7-140">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="696c7-141">Dans l’exemple suivant, l' `MyDependency` implémentation est inscrite pour `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="696c7-141">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="696c7-142">Une fois l’hôte généré, les services sont accessibles à partir de l’étendue de l’injection de comptes racine avant le rendu des composants.</span><span class="sxs-lookup"><span data-stu-id="696c7-142">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="696c7-143">Cela peut être utile pour l’exécution de la logique d’initialisation avant le rendu du contenu :</span><span class="sxs-lookup"><span data-stu-id="696c7-143">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="696c7-144">L’hôte fournit également une instance de configuration centrale pour l’application.</span><span class="sxs-lookup"><span data-stu-id="696c7-144">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="696c7-145">En s’appuyant sur l’exemple précédent, l’URL du service météo est passée d’une source de configuration par défaut (par exemple, `appsettings.json` ) à `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="696c7-145">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

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

<span data-ttu-id="696c7-146">Après avoir créé une nouvelle application, examinez la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="696c7-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="696c7-147"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Une <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , qui est une liste d’objets descripteurs de service (), est passée à la méthode <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> .</span><span class="sxs-lookup"><span data-stu-id="696c7-147">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="696c7-148">Les services sont ajoutés dans la `ConfigureServices` méthode en fournissant des descripteurs de service à la collection de services.</span><span class="sxs-lookup"><span data-stu-id="696c7-148">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="696c7-149">L’exemple suivant illustre le concept avec l' `IDataAccess` interface et son implémentation concrète `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="696c7-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="696c7-150">Durée de vie du service</span><span class="sxs-lookup"><span data-stu-id="696c7-150">Service lifetime</span></span>

<span data-ttu-id="696c7-151">Les services peuvent être configurés avec les durées de vie indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="696c7-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="696c7-152">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="696c7-152">Lifetime</span></span> | <span data-ttu-id="696c7-153">Description</span><span class="sxs-lookup"><span data-stu-id="696c7-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <span data-ttu-id="696c7-154">Blazor WebAssembly les applications n’ont pas actuellement de concept d’étendues DI.</span><span class="sxs-lookup"><span data-stu-id="696c7-154">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="696c7-155">`Scoped`-les services inscrits se comportent comme des `Singleton` services.</span><span class="sxs-lookup"><span data-stu-id="696c7-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="696c7-156">Toutefois, le Blazor Server modèle d’hébergement prend en charge la `Scoped` durée de vie.</span><span class="sxs-lookup"><span data-stu-id="696c7-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="696c7-157">Dans Blazor Server les applications, l’inscription d’un service étendu est limitée à la *connexion*.</span><span class="sxs-lookup"><span data-stu-id="696c7-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="696c7-158">Pour cette raison, il est préférable d’utiliser les services délimités pour les services qui doivent être étendus à l’utilisateur actuel, même si l’objectif actuel est d’exécuter côté client dans le navigateur dans une Blazor WebAssembly application.</span><span class="sxs-lookup"><span data-stu-id="696c7-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser in a Blazor WebAssembly app.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="696c7-159">DI crée une *seule instance* du service.</span><span class="sxs-lookup"><span data-stu-id="696c7-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="696c7-160">Tous les composants qui requièrent un `Singleton` service reçoivent une instance du même service.</span><span class="sxs-lookup"><span data-stu-id="696c7-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="696c7-161">Chaque fois qu’un composant obtient une instance d’un `Transient` service à partir du conteneur de service, il reçoit une *nouvelle instance* du service.</span><span class="sxs-lookup"><span data-stu-id="696c7-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="696c7-162">Le système DI est basé sur le système DI dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="696c7-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="696c7-163">Pour plus d'informations, consultez <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="696c7-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="696c7-164">Demander un service dans un composant</span><span class="sxs-lookup"><span data-stu-id="696c7-164">Request a service in a component</span></span>

<span data-ttu-id="696c7-165">Une fois les services ajoutés à la collection de services, injectez les services dans les composants à l’aide de la directive [ \@ Inject](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="696c7-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="696c7-166">[`@inject`](xref:mvc/views/razor#inject) a deux paramètres :</span><span class="sxs-lookup"><span data-stu-id="696c7-166">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="696c7-167">Type : type du service à injecter.</span><span class="sxs-lookup"><span data-stu-id="696c7-167">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="696c7-168">Propriété : nom de la propriété qui reçoit le service d’application injecté.</span><span class="sxs-lookup"><span data-stu-id="696c7-168">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="696c7-169">La propriété ne nécessite pas de création manuelle.</span><span class="sxs-lookup"><span data-stu-id="696c7-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="696c7-170">Le compilateur crée la propriété.</span><span class="sxs-lookup"><span data-stu-id="696c7-170">The compiler creates the property.</span></span>

<span data-ttu-id="696c7-171">Pour plus d'informations, consultez <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="696c7-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="696c7-172">Utilisez plusieurs [`@inject`](xref:mvc/views/razor#inject) instructions pour injecter différents services.</span><span class="sxs-lookup"><span data-stu-id="696c7-172">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="696c7-173">L’exemple suivant montre comment utiliser [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="696c7-173">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="696c7-174">Le service qui implémente `Services.IDataAccess` est injecté dans la propriété du composant `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="696c7-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="696c7-175">Notez la manière dont le code utilise uniquement l' `IDataAccess` abstraction :</span><span class="sxs-lookup"><span data-stu-id="696c7-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="696c7-176">En interne, la propriété générée ( `DataRepository` ) utilise l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="696c7-176">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="696c7-177">En règle générale, cet attribut n’est pas utilisé directement.</span><span class="sxs-lookup"><span data-stu-id="696c7-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="696c7-178">Si une classe de base est requise pour les composants et les propriétés injectées sont également requises pour la classe de base, ajoutez manuellement l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut :</span><span class="sxs-lookup"><span data-stu-id="696c7-178">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="696c7-179">Dans les composants dérivés de la classe de base, la [`@inject`](xref:mvc/views/razor#inject) directive n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="696c7-179">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="696c7-180">Le <xref:Microsoft.AspNetCore.Components.InjectAttribute> de la classe de base est suffisant :</span><span class="sxs-lookup"><span data-stu-id="696c7-180">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="696c7-181">Utiliser DI dans les services</span><span class="sxs-lookup"><span data-stu-id="696c7-181">Use DI in services</span></span>

<span data-ttu-id="696c7-182">Les services complexes peuvent nécessiter des services supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="696c7-182">Complex services might require additional services.</span></span> <span data-ttu-id="696c7-183">Dans l’exemple précédent, `DataAccess` peut nécessiter le <xref:System.Net.Http.HttpClient> service par défaut.</span><span class="sxs-lookup"><span data-stu-id="696c7-183">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="696c7-184">[`@inject`](xref:mvc/views/razor#inject) (ou l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut) n’est pas disponible pour une utilisation dans les services.</span><span class="sxs-lookup"><span data-stu-id="696c7-184">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="696c7-185">L' *injection de constructeur* doit être utilisée à la place.</span><span class="sxs-lookup"><span data-stu-id="696c7-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="696c7-186">Les services requis sont ajoutés en ajoutant des paramètres au constructeur du service.</span><span class="sxs-lookup"><span data-stu-id="696c7-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="696c7-187">Lorsque DI crée le service, il reconnaît les services dont il a besoin dans le constructeur et les fournit en conséquence.</span><span class="sxs-lookup"><span data-stu-id="696c7-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="696c7-188">Dans l’exemple suivant, le constructeur reçoit un <xref:System.Net.Http.HttpClient> via di.</span><span class="sxs-lookup"><span data-stu-id="696c7-188">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="696c7-189"><xref:System.Net.Http.HttpClient> est un service par défaut.</span><span class="sxs-lookup"><span data-stu-id="696c7-189"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="696c7-190">Conditions préalables pour l’injection de constructeur :</span><span class="sxs-lookup"><span data-stu-id="696c7-190">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="696c7-191">Un constructeur doit exister dont les arguments peuvent tous être remplis par DI.</span><span class="sxs-lookup"><span data-stu-id="696c7-191">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="696c7-192">Les paramètres supplémentaires non couverts par DI sont autorisés s’ils spécifient des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="696c7-192">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="696c7-193">Le constructeur applicable doit être `public` .</span><span class="sxs-lookup"><span data-stu-id="696c7-193">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="696c7-194">Un constructeur applicable doit exister.</span><span class="sxs-lookup"><span data-stu-id="696c7-194">One applicable constructor must exist.</span></span> <span data-ttu-id="696c7-195">En cas d’ambiguïté, DI lève une exception.</span><span class="sxs-lookup"><span data-stu-id="696c7-195">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="696c7-196">Classes de composants de base de l’utilitaire pour gérer une étendue DI</span><span class="sxs-lookup"><span data-stu-id="696c7-196">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="696c7-197">Dans ASP.NET Core applications, les services délimités sont généralement étendus à la requête actuelle.</span><span class="sxs-lookup"><span data-stu-id="696c7-197">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="696c7-198">Une fois la demande terminée, tous les services délimités ou temporaires sont supprimés par le système DI.</span><span class="sxs-lookup"><span data-stu-id="696c7-198">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="696c7-199">Dans Blazor Server les applications, l’étendue de la demande est valable pendant la durée de la connexion cliente, ce qui peut entraîner des services transitoires et de portée bien plus longs que prévu.</span><span class="sxs-lookup"><span data-stu-id="696c7-199">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="696c7-200">Dans les Blazor WebAssembly applications, les services inscrits avec une durée de vie limitée sont traités comme des singletons, de sorte qu’ils vivent plus longtemps que les services étendus dans les applications de ASP.net Core standard.</span><span class="sxs-lookup"><span data-stu-id="696c7-200">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="696c7-201">Pour détecter les services transitoires distants dans une application, consultez la section détecter les services temporaires à usage [temporaire](#detect-transient-disposables) .</span><span class="sxs-lookup"><span data-stu-id="696c7-201">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="696c7-202">Une approche qui limite la durée de vie d’un service dans Blazor les applications est l’utilisation du <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span><span class="sxs-lookup"><span data-stu-id="696c7-202">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="696c7-203"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> est un type abstrait dérivé de <xref:Microsoft.AspNetCore.Components.ComponentBase> qui crée une étendue di correspondant à la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="696c7-203"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="696c7-204">À l’aide de cette étendue, il est possible d’utiliser l’injection de services avec une durée de vie limitée et de les faire vivre aussi longtemps que le composant.</span><span class="sxs-lookup"><span data-stu-id="696c7-204">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="696c7-205">Lorsque le composant est détruit, les services du fournisseur de services étendus du composant sont également supprimés.</span><span class="sxs-lookup"><span data-stu-id="696c7-205">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="696c7-206">Cela peut être utile pour les services qui :</span><span class="sxs-lookup"><span data-stu-id="696c7-206">This can be useful for services that:</span></span>

* <span data-ttu-id="696c7-207">Doit être réutilisé dans un composant, car la durée de vie temporaire n’est pas appropriée.</span><span class="sxs-lookup"><span data-stu-id="696c7-207">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="696c7-208">Ne doit pas être partagé entre les composants, car la durée de vie Singleton n’est pas appropriée.</span><span class="sxs-lookup"><span data-stu-id="696c7-208">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="696c7-209">Deux versions du <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="696c7-209">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="696c7-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> est un enfant abstrait et jetable du <xref:Microsoft.AspNetCore.Components.ComponentBase> type avec une propriété protégée <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> de type <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="696c7-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="696c7-211">Ce fournisseur peut être utilisé pour résoudre les services dont la portée est limitée à la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="696c7-211">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="696c7-212">Les services d’injection de services injectés dans le composant à l’aide de [`@inject`](xref:mvc/views/razor#inject) ou l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut ne sont pas créés dans l’étendue du composant.</span><span class="sxs-lookup"><span data-stu-id="696c7-212">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="696c7-213">Pour utiliser l’étendue du composant, les services doivent être résolus à l’aide <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> de ou de <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="696c7-213">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="696c7-214">Les dépendances de tous les services résolus à l’aide du <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> fournisseur sont fournies à partir de cette même étendue.</span><span class="sxs-lookup"><span data-stu-id="696c7-214">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="696c7-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> dérive de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> et ajoute une <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> propriété qui retourne une instance de `T` à partir du fournisseur di étendu.</span><span class="sxs-lookup"><span data-stu-id="696c7-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="696c7-216">Ce type est un moyen pratique d’accéder aux services délimités sans utiliser une instance de <xref:System.IServiceProvider> lorsqu’un service principal est requis par l’application à partir du conteneur di à l’aide de l’étendue du composant.</span><span class="sxs-lookup"><span data-stu-id="696c7-216">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="696c7-217">La <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriété est disponible. par conséquent, l’application peut récupérer des services d’autres types, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="696c7-217">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="696c7-218">Utilisation d’un DbContext Entity Framework Core (EF Core) à partir de DI</span><span class="sxs-lookup"><span data-stu-id="696c7-218">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="696c7-219">Pour plus d'informations, consultez <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="696c7-219">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="696c7-220">Détecter les supprimables temporaires</span><span class="sxs-lookup"><span data-stu-id="696c7-220">Detect transient disposables</span></span>

<span data-ttu-id="696c7-221">Les exemples suivants montrent comment détecter des services transitoires jetables dans une application qui doit utiliser <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="696c7-221">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="696c7-222">Pour plus d’informations, consultez la section [classes du composant de base de l’utilitaire pour gérer une étendue de l’injection de](#utility-base-component-classes-to-manage-a-di-scope) données.</span><span class="sxs-lookup"><span data-stu-id="696c7-222">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="696c7-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="696c7-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="696c7-224">Le `TransientDisposable` dans l’exemple suivant est détecté ( `Program.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="696c7-224">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

<span data-ttu-id="696c7-225">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="696c7-225">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="696c7-226">`Program`:</span><span class="sxs-lookup"><span data-stu-id="696c7-226">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="696c7-227">Le `TransientDependency` dans l’exemple suivant est détecté ( `Startup.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="696c7-227">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="696c7-228">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="696c7-228">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="696c7-229">`IDisposable` conseils pour les instances temporaires et partagées</span><span class="sxs-lookup"><span data-stu-id="696c7-229">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
