---
title: Injection de dépendances dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ASP.NET Core implémente l’injection de dépendances et comment l’utiliser.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 6f677cc4fc26eb9d50ab6e149b7363079ae756a9
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678559"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="d6555-103">Injection de dépendances dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6555-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d6555-104">Par [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)et [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="d6555-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="d6555-105">ASP.NET Core prend en charge le modèle de conception logicielle d’injection de dépendances, technique qui permet d’obtenir une [inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="d6555-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="d6555-106">Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d6555-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="d6555-107">Pour plus d’informations sur l’utilisation de l’injection de dépendances dans les applications console, consultez [injection de dépendances dans .net](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="d6555-107">For information on using dependency injection in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="d6555-108">Pour plus d’informations sur l’injection de dépendances d’options, consultez <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="d6555-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="d6555-109">Cette rubrique fournit des informations sur l’injection de dépendances dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6555-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="d6555-110">Pour plus d’informations sur l’utilisation de l’injection de dépendances dans les applications console, consultez [injection de dépendances dans .net](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d6555-110">For information on using dependency injection  in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="d6555-111">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d6555-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="d6555-112">Vue d’ensemble de l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="d6555-112">Overview of dependency injection</span></span>

<span data-ttu-id="d6555-113">Une *dépendance* est un objet dont dépend un autre objet.</span><span class="sxs-lookup"><span data-stu-id="d6555-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="d6555-114">Examinez la `MyDependency` classe suivante avec une `WriteMessage` méthode dont dépendent d’autres classes :</span><span class="sxs-lookup"><span data-stu-id="d6555-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="d6555-115">Une classe peut créer une instance de la `MyDependency` classe pour utiliser sa `WriteMessage` méthode.</span><span class="sxs-lookup"><span data-stu-id="d6555-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="d6555-116">Dans l’exemple suivant, la `MyDependency` classe est une dépendance de la `IndexModel` classe :</span><span class="sxs-lookup"><span data-stu-id="d6555-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="d6555-117">La classe crée et dépend directement de la `MyDependency` classe.</span><span class="sxs-lookup"><span data-stu-id="d6555-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="d6555-118">Les dépendances de code, comme dans l’exemple précédent, sont problématiques et doivent être évitées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="d6555-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="d6555-119">Pour remplacer `MyDependency` par une implémentation différente, vous `IndexModel` devez modifier la classe.</span><span class="sxs-lookup"><span data-stu-id="d6555-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="d6555-120">Si `MyDependency` a des dépendances, ils doivent également être configurés par la `IndexModel` classe.</span><span class="sxs-lookup"><span data-stu-id="d6555-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="d6555-121">Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.</span><span class="sxs-lookup"><span data-stu-id="d6555-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="d6555-122">Cette implémentation complique le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="d6555-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="d6555-123">L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="d6555-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="d6555-124">L’injection de dépendances résout ces problèmes via :</span><span class="sxs-lookup"><span data-stu-id="d6555-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="d6555-125">L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.</span><span class="sxs-lookup"><span data-stu-id="d6555-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="d6555-126">L’inscription de la dépendance dans un conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="d6555-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="d6555-127">ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="d6555-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d6555-128">Les services sont généralement inscrits dans la méthode de l’application `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d6555-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d6555-129">*Injection* du service dans le constructeur de la classe où il est utilisé.</span><span class="sxs-lookup"><span data-stu-id="d6555-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="d6555-130">Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="d6555-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="d6555-131">Dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l' `IMyDependency` interface définit la `WriteMessage` méthode :</span><span class="sxs-lookup"><span data-stu-id="d6555-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="d6555-132">Cette interface est implémentée par un type concret, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="d6555-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="d6555-133">L’exemple d’application inscrit le `IMyDependency` service avec le type concret `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d6555-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="d6555-134">La <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> méthode enregistre le service avec une durée de vie limitée, la durée de vie d’une requête unique.</span><span class="sxs-lookup"><span data-stu-id="d6555-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="d6555-135">Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="d6555-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="d6555-136">Dans l’exemple d’application, le `IMyDependency` service est demandé et utilisé pour appeler la `WriteMessage` méthode :</span><span class="sxs-lookup"><span data-stu-id="d6555-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="d6555-137">En utilisant le modèle DI, le contrôleur :</span><span class="sxs-lookup"><span data-stu-id="d6555-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="d6555-138">N’utilise pas le type concret `MyDependency` , mais uniquement l' `IMyDependency` interface qu’il implémente.</span><span class="sxs-lookup"><span data-stu-id="d6555-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="d6555-139">Cela facilite la modification de l’implémentation que le contrôleur utilise sans modifier le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="d6555-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="d6555-140">Ne crée pas d’instance de `MyDependency` , elle est créée par le conteneur di.</span><span class="sxs-lookup"><span data-stu-id="d6555-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="d6555-141">L’implémentation de l' `IMyDependency` interface peut être améliorée à l’aide de l’API de journalisation intégrée :</span><span class="sxs-lookup"><span data-stu-id="d6555-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="d6555-142">La méthode mise à jour `ConfigureServices` inscrit la nouvelle `IMyDependency` implémentation :</span><span class="sxs-lookup"><span data-stu-id="d6555-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="d6555-143">`MyDependency2` dépend de <xref:Microsoft.Extensions.Logging.ILogger%601> , qu’il demande dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="d6555-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="d6555-144">`ILogger<TCategoryName>` est un [service fourni par le Framework](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="d6555-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="d6555-145">Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée.</span><span class="sxs-lookup"><span data-stu-id="d6555-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="d6555-146">Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances.</span><span class="sxs-lookup"><span data-stu-id="d6555-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="d6555-147">Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu.</span><span class="sxs-lookup"><span data-stu-id="d6555-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="d6555-148">L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances* , *graphique de dépendance* ou *graphique d’objet* .</span><span class="sxs-lookup"><span data-stu-id="d6555-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph* .</span></span>

<span data-ttu-id="d6555-149">Le conteneur se résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui évite d’avoir à inscrire chaque [type construit (Générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="d6555-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="d6555-150">Dans la terminologie d’injection de dépendances, un service :</span><span class="sxs-lookup"><span data-stu-id="d6555-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="d6555-151">Est généralement un objet qui fournit un service à d’autres objets, tels que le `IMyDependency` service.</span><span class="sxs-lookup"><span data-stu-id="d6555-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="d6555-152">N’est pas associé à un service Web, bien que le service puisse utiliser un service Web.</span><span class="sxs-lookup"><span data-stu-id="d6555-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="d6555-153">Le Framework fournit un système de [journalisation](xref:fundamentals/logging/index) robuste.</span><span class="sxs-lookup"><span data-stu-id="d6555-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="d6555-154">Les `IMyDependency` implémentations présentées dans les exemples précédents ont été écrites pour illustrer la di de base, et non pour implémenter la journalisation.</span><span class="sxs-lookup"><span data-stu-id="d6555-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="d6555-155">La plupart des applications n’ont pas besoin d’écrire des enregistreurs.</span><span class="sxs-lookup"><span data-stu-id="d6555-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="d6555-156">Le code suivant illustre l’utilisation de la journalisation par défaut, qui ne nécessite pas l’inscription de services dans `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d6555-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="d6555-157">À l’aide du code précédent, il n’est pas nécessaire de mettre à jour `ConfigureServices` , car la [journalisation](xref:fundamentals/logging/index) est assurée par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="d6555-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="d6555-158">Services injectés au démarrage</span><span class="sxs-lookup"><span data-stu-id="d6555-158">Services injected into Startup</span></span>

<span data-ttu-id="d6555-159">Les services peuvent être injectés dans le `Startup` constructeur et la `Startup.Configure` méthode.</span><span class="sxs-lookup"><span data-stu-id="d6555-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="d6555-160">Seuls les services suivants peuvent être injectés dans le `Startup` constructeur lors de l’utilisation de l’hôte générique ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ) :</span><span class="sxs-lookup"><span data-stu-id="d6555-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="d6555-161">Tout service enregistré avec le conteneur DI peut être injecté dans la `Startup.Configure` méthode :</span><span class="sxs-lookup"><span data-stu-id="d6555-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="d6555-162">Pour plus d’informations, consultez <xref:fundamentals/startup> et [configuration de l’accès au démarrage](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="d6555-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="d6555-163">Inscrire des groupes de services avec des méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="d6555-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="d6555-164">L’infrastructure de ASP.NET Core utilise une convention pour inscrire un groupe de services connexes.</span><span class="sxs-lookup"><span data-stu-id="d6555-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="d6555-165">La Convention consiste à utiliser une `Add{GROUP_NAME}` méthode d’extension unique pour inscrire tous les services requis par une fonctionnalité de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="d6555-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="d6555-166">Par exemple, la méthode d’extension <Microsoft. extensions. DependencyInjection. MvcServiceCollectionExtensions. AddControllers> inscrit les services requis pour les contrôleurs MVC.</span><span class="sxs-lookup"><span data-stu-id="d6555-166">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="d6555-167">Le code suivant est généré par le :::no-loc(Razor)::: modèle pages à l’aide de comptes d’utilisateur individuels et montre comment ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> et <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A> :</span><span class="sxs-lookup"><span data-stu-id="d6555-167">The following code is generated by the :::no-loc(Razor)::: Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="d6555-168">Durées de service</span><span class="sxs-lookup"><span data-stu-id="d6555-168">Service lifetimes</span></span>

<span data-ttu-id="d6555-169">Les services peuvent être enregistrés avec l’une des durées de vie suivantes :</span><span class="sxs-lookup"><span data-stu-id="d6555-169">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="d6555-170">Temporaire</span><span class="sxs-lookup"><span data-stu-id="d6555-170">Transient</span></span>
* <span data-ttu-id="d6555-171">Délimité</span><span class="sxs-lookup"><span data-stu-id="d6555-171">Scoped</span></span>
* <span data-ttu-id="d6555-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-172">Singleton</span></span>

<span data-ttu-id="d6555-173">Les sections suivantes décrivent chacune des durées de vie précédentes.</span><span class="sxs-lookup"><span data-stu-id="d6555-173">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="d6555-174">Choisissez une durée de vie appropriée pour chaque service inscrit.</span><span class="sxs-lookup"><span data-stu-id="d6555-174">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="d6555-175">Temporaire</span><span class="sxs-lookup"><span data-stu-id="d6555-175">Transient</span></span>

<span data-ttu-id="d6555-176">Des services à durée de vie temporaire (Transient) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="d6555-176">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="d6555-177">Cette durée de vie convient parfaitement aux services légers et sans état.</span><span class="sxs-lookup"><span data-stu-id="d6555-177">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="d6555-178">Inscrire des services temporaires avec <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .</span><span class="sxs-lookup"><span data-stu-id="d6555-178">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="d6555-179">Dans les applications qui traitent les demandes, les services transitoires sont supprimés à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="d6555-179">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="d6555-180">Délimité</span><span class="sxs-lookup"><span data-stu-id="d6555-180">Scoped</span></span>

<span data-ttu-id="d6555-181">Les services à durée de vie délimitée (Scoped) sont créés une seule fois par requête de client (connexion).</span><span class="sxs-lookup"><span data-stu-id="d6555-181">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="d6555-182">Inscrire les services délimités avec <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .</span><span class="sxs-lookup"><span data-stu-id="d6555-182">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="d6555-183">Dans les applications qui traitent les requêtes, les services délimités sont supprimés à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="d6555-183">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="d6555-184">Lors de l’utilisation de Entity Framework Core, la <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> méthode d’extension inscrit les `DbContext` types avec une durée de vie limitée par défaut.</span><span class="sxs-lookup"><span data-stu-id="d6555-184">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="d6555-185">Do \* **not** _ résoudre un service étendu à partir d’un singleton et veiller à ne pas le faire indirectement, par exemple via un service temporaire.</span><span class="sxs-lookup"><span data-stu-id="d6555-185">Do \* **not** _ resolve a scoped service from a singleton and be careful not to do so indirectly, for example, through a transient service.</span></span> <span data-ttu-id="d6555-186">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="d6555-186">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="d6555-187">Il convient d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="d6555-187">It's fine to:</span></span>

<span data-ttu-id="d6555-188">_ Résoudre un service Singleton à partir d’un service étendu ou temporaire.</span><span class="sxs-lookup"><span data-stu-id="d6555-188">_ Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="d6555-189">Résoudre un service étendu à partir d’un autre service étendu ou temporaire.</span><span class="sxs-lookup"><span data-stu-id="d6555-189">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="d6555-190">Par défaut, dans l’environnement de développement, la résolution d’un service à partir d’un autre service avec une durée de vie plus longue lève une exception.</span><span class="sxs-lookup"><span data-stu-id="d6555-190">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="d6555-191">Pour plus d’informations, consultez [Validation de l’étendue](#sv).</span><span class="sxs-lookup"><span data-stu-id="d6555-191">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="d6555-192">Pour utiliser les services délimités dans des intergiciels (middleware), utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="d6555-192">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="d6555-193">Injectez le service dans la méthode ou l’intergiciel (middleware) `Invoke` `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="d6555-193">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="d6555-194">L’utilisation de l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) lève une exception Runtime, car elle force le service de portée à se comporter comme un singleton.</span><span class="sxs-lookup"><span data-stu-id="d6555-194">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="d6555-195">L’exemple de la section [options de durée de vie et d’inscription](#lifetime-and-registration-options) illustre l' `InvokeAsync` approche.</span><span class="sxs-lookup"><span data-stu-id="d6555-195">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="d6555-196">Utilisez [un intergiciel (middleware) basé sur l’usine](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="d6555-196">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="d6555-197">Les intergiciels (middleware) inscrits à l’aide de cette approche sont activés par demande du client (connexion), ce qui permet d’injecter des services étendus dans la méthode de l’intergiciel (middleware) `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="d6555-197">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="d6555-198">Pour plus d'informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="d6555-198">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="d6555-199">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-199">Singleton</span></span>

<span data-ttu-id="d6555-200">Les services de durée de vie Singleton sont créés :</span><span class="sxs-lookup"><span data-stu-id="d6555-200">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="d6555-201">La première fois qu’ils sont demandés.</span><span class="sxs-lookup"><span data-stu-id="d6555-201">The first time they're requested.</span></span>
* <span data-ttu-id="d6555-202">Par le développeur, lors de la fourniture d’une instance d’implémentation directement au conteneur.</span><span class="sxs-lookup"><span data-stu-id="d6555-202">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="d6555-203">Cette approche est rarement nécessaire.</span><span class="sxs-lookup"><span data-stu-id="d6555-203">This approach is rarely needed.</span></span>

<span data-ttu-id="d6555-204">Chaque requête ultérieure utilise la même instance.</span><span class="sxs-lookup"><span data-stu-id="d6555-204">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="d6555-205">Si l’application requiert un comportement Singleton, autorisez le conteneur de service à gérer la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="d6555-205">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="d6555-206">N’implémentez pas le modèle de conception Singleton et fournissez du code pour supprimer le singleton.</span><span class="sxs-lookup"><span data-stu-id="d6555-206">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="d6555-207">Les services ne doivent jamais être supprimés par du code qui a résolu le service à partir du conteneur.</span><span class="sxs-lookup"><span data-stu-id="d6555-207">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="d6555-208">Si un type ou une fabrique est inscrit en tant que singleton, le conteneur supprime automatiquement le singleton.</span><span class="sxs-lookup"><span data-stu-id="d6555-208">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="d6555-209">Inscrire des services Singleton avec <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> .</span><span class="sxs-lookup"><span data-stu-id="d6555-209">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="d6555-210">Les services Singleton doivent être thread-safe et sont souvent utilisés dans les services sans État.</span><span class="sxs-lookup"><span data-stu-id="d6555-210">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="d6555-211">Dans les applications qui traitent les requêtes, les services Singleton sont supprimés lorsque le <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> est supprimé lors de l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="d6555-211">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="d6555-212">Étant donné que la mémoire n’est pas libérée tant que l’application n’est pas arrêtée, envisagez d’utiliser la mémoire avec un service Singleton.</span><span class="sxs-lookup"><span data-stu-id="d6555-212">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="d6555-213">Do \* **not** _ résoudre un service étendu à partir d’un singleton.</span><span class="sxs-lookup"><span data-stu-id="d6555-213">Do \* **not** _ resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d6555-214">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="d6555-214">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="d6555-215">Il est parfait de résoudre un service Singleton à partir d’un service étendu ou temporaire.</span><span class="sxs-lookup"><span data-stu-id="d6555-215">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="d6555-216">Méthodes d’inscription du service</span><span class="sxs-lookup"><span data-stu-id="d6555-216">Service registration methods</span></span>

<span data-ttu-id="d6555-217">Le Framework fournit des méthodes d’extension d’inscription de service qui sont utiles dans des scénarios spécifiques :</span><span class="sxs-lookup"><span data-stu-id="d6555-217">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="d6555-218">Méthode</span><span class="sxs-lookup"><span data-stu-id="d6555-218">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="d6555-219">Automatique</span><span class="sxs-lookup"><span data-stu-id="d6555-219">Automatic</span></span><br><span data-ttu-id="d6555-220">object</span><span class="sxs-lookup"><span data-stu-id="d6555-220">object</span></span><br><span data-ttu-id="d6555-221">suppression</span><span class="sxs-lookup"><span data-stu-id="d6555-221">disposal</span></span> | <span data-ttu-id="d6555-222">Multiple</span><span class="sxs-lookup"><span data-stu-id="d6555-222">Multiple</span></span><br><span data-ttu-id="d6555-223">implémentations</span><span class="sxs-lookup"><span data-stu-id="d6555-223">implementations</span></span> | <span data-ttu-id="d6555-224">Passage d’args</span><span class="sxs-lookup"><span data-stu-id="d6555-224">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="d6555-225">Exemple :</span><span class="sxs-lookup"><span data-stu-id="d6555-225">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="d6555-226">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-226">Yes</span></span>                             | <span data-ttu-id="d6555-227">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-227">Yes</span></span>                         | <span data-ttu-id="d6555-228">Non</span><span class="sxs-lookup"><span data-stu-id="d6555-228">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="d6555-229">Exemples :</span><span class="sxs-lookup"><span data-stu-id="d6555-229">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="d6555-230">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-230">Yes</span></span>                             | <span data-ttu-id="d6555-231">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-231">Yes</span></span>                         | <span data-ttu-id="d6555-232">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-232">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="d6555-233">Exemple :</span><span class="sxs-lookup"><span data-stu-id="d6555-233">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="d6555-234">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-234">Yes</span></span>                             | <span data-ttu-id="d6555-235">Non</span><span class="sxs-lookup"><span data-stu-id="d6555-235">No</span></span>                          | <span data-ttu-id="d6555-236">Non</span><span class="sxs-lookup"><span data-stu-id="d6555-236">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="d6555-237">Exemples :</span><span class="sxs-lookup"><span data-stu-id="d6555-237">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="d6555-238">Non</span><span class="sxs-lookup"><span data-stu-id="d6555-238">No</span></span>                              | <span data-ttu-id="d6555-239">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-239">Yes</span></span>                         | <span data-ttu-id="d6555-240">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-240">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="d6555-241">Exemples :</span><span class="sxs-lookup"><span data-stu-id="d6555-241">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="d6555-242">Non</span><span class="sxs-lookup"><span data-stu-id="d6555-242">No</span></span>                              | <span data-ttu-id="d6555-243">Non</span><span class="sxs-lookup"><span data-stu-id="d6555-243">No</span></span>                          | <span data-ttu-id="d6555-244">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-244">Yes</span></span>       |

<span data-ttu-id="d6555-245">Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="d6555-245">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="d6555-246">Il est courant d’utiliser plusieurs implémentations lorsqu’il s’agit [de simuler des types à des fins de test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="d6555-246">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="d6555-247">L’inscription d’un service avec un seul type d’implémentation équivaut à inscrire ce service avec le même type de service et l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="d6555-247">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="d6555-248">C’est pourquoi plusieurs implémentations d’un service ne peuvent pas être inscrites à l’aide des méthodes qui n’acceptent pas un type de service explicite.</span><span class="sxs-lookup"><span data-stu-id="d6555-248">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="d6555-249">Ces méthodes peuvent inscrire plusieurs _instances \* d’un service, mais elles auront toutes le même type d' *implémentation* .</span><span class="sxs-lookup"><span data-stu-id="d6555-249">These methods can register multiple _instances\* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="d6555-250">L’une des méthodes d’inscription de service ci-dessus peut être utilisée pour inscrire plusieurs instances de service du même type de service.</span><span class="sxs-lookup"><span data-stu-id="d6555-250">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="d6555-251">Dans l’exemple suivant, `AddSingleton` est appelé deux fois avec `IMyDependency` comme type de service.</span><span class="sxs-lookup"><span data-stu-id="d6555-251">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="d6555-252">Le deuxième appel à `AddSingleton` remplace le précédent lorsqu’il est résolu en tant que `IMyDependency` et ajoute au précédent lorsque plusieurs services sont résolus via `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="d6555-252">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="d6555-253">Les services s’affichent dans l’ordre dans lequel ils ont été inscrits lorsqu’ils sont résolus via `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="d6555-253">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="d6555-254">Le Framework fournit également des `TryAdd{LIFETIME}` méthodes d’extension, qui inscrivent le service uniquement si aucune implémentation n’est déjà inscrite.</span><span class="sxs-lookup"><span data-stu-id="d6555-254">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="d6555-255">Dans l’exemple suivant, l’appel à `AddSingleton` s’inscrit `MyDependency` en tant qu’implémentation de `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d6555-255">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="d6555-256">L’appel à n' `TryAddSingleton` a aucun effet, car `IMyDependency` a déjà une implémentation inscrite.</span><span class="sxs-lookup"><span data-stu-id="d6555-256">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="d6555-257">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="d6555-257">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="d6555-258">Les méthodes [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) inscrivent le service uniquement s’il n’existe pas déjà une implémentation *du même type* .</span><span class="sxs-lookup"><span data-stu-id="d6555-258">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type* .</span></span> <span data-ttu-id="d6555-259">Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="d6555-259">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="d6555-260">Lors de l’inscription des services, le développeur doit ajouter une instance si l’un des mêmes types n’a pas déjà été ajouté.</span><span class="sxs-lookup"><span data-stu-id="d6555-260">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="d6555-261">En général, les auteurs de bibliothèque utilisent `TryAddEnumerable` pour éviter d’inscrire plusieurs copies d’une implémentation dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="d6555-261">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="d6555-262">Dans l’exemple suivant, le premier appel à `TryAddEnumerable` s’inscrit `MyDependency` en tant qu’implémentation pour `IMyDependency1` .</span><span class="sxs-lookup"><span data-stu-id="d6555-262">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="d6555-263">Le deuxième appel s’inscrit `MyDependency` pour `IMyDependency2` .</span><span class="sxs-lookup"><span data-stu-id="d6555-263">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="d6555-264">Le troisième appel n’a aucun effet, car `IMyDependency1` a déjà une implémentation inscrite de `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="d6555-264">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="d6555-265">L’inscription de service est généralement indépendante de l’ordre, sauf lors de l’inscription de plusieurs implémentations du même type.</span><span class="sxs-lookup"><span data-stu-id="d6555-265">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="d6555-266">`IServiceCollection` est une collection d' <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objets.</span><span class="sxs-lookup"><span data-stu-id="d6555-266">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="d6555-267">L’exemple suivant montre comment inscrire un service en créant et en ajoutant un `ServiceDescriptor` :</span><span class="sxs-lookup"><span data-stu-id="d6555-267">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="d6555-268">Les `Add{LIFETIME}` méthodes intégrées utilisent la même approche.</span><span class="sxs-lookup"><span data-stu-id="d6555-268">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="d6555-269">Par exemple, consultez le [code source AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="d6555-269">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="d6555-270">Comportement d’injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="d6555-270">Constructor injection behavior</span></span>

<span data-ttu-id="d6555-271">Les services peuvent être résolus à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="d6555-271">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="d6555-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="d6555-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="d6555-273">Crée des objets qui ne sont pas inscrits dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="d6555-273">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="d6555-274">Utilisé avec les fonctionnalités d’infrastructure, telles que les [balises d’assistance](xref:mvc/views/tag-helpers/intro), les contrôleurs MVC et les [classeurs de modèles](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="d6555-274">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="d6555-275">Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="d6555-275">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="d6555-276">Lorsque les services sont résolus par `IServiceProvider` ou `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .</span><span class="sxs-lookup"><span data-stu-id="d6555-276">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="d6555-277">Lorsque les services sont résolus par `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable.</span><span class="sxs-lookup"><span data-stu-id="d6555-277">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="d6555-278">Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="d6555-278">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="d6555-279">Contextes Entity Framework</span><span class="sxs-lookup"><span data-stu-id="d6555-279">Entity Framework contexts</span></span>

<span data-ttu-id="d6555-280">Par défaut, Entity Framework contextes sont ajoutés au conteneur de service à l’aide de la [durée de vie limitée](#service-lifetimes) , car les opérations de base de données d’application Web sont normalement étendues à la demande du client.</span><span class="sxs-lookup"><span data-stu-id="d6555-280">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="d6555-281">Pour utiliser une durée de vie différente, spécifiez la durée de vie à l’aide d’une <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> surcharge.</span><span class="sxs-lookup"><span data-stu-id="d6555-281">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="d6555-282">Les services d’une durée de vie donnée ne doivent pas utiliser un contexte de base de données dont la durée de vie est inférieure à la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="d6555-282">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="d6555-283">Options de durée de vie et d’inscription</span><span class="sxs-lookup"><span data-stu-id="d6555-283">Lifetime and registration options</span></span>

<span data-ttu-id="d6555-284">Pour illustrer la différence entre les durées de vie de service et leurs options d’inscription, considérez les interfaces suivantes qui représentent une tâche comme une opération avec un identificateur, `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="d6555-284">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="d6555-285">Selon la configuration de la durée de vie du service d’une opération pour les interfaces suivantes, le conteneur fournit des instances identiques ou différentes du service lorsqu’il est demandé par une classe :</span><span class="sxs-lookup"><span data-stu-id="d6555-285">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="d6555-286">La `Operation` classe suivante implémente toutes les interfaces précédentes.</span><span class="sxs-lookup"><span data-stu-id="d6555-286">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="d6555-287">Le `Operation` constructeur génère un GUID et stocke les 4 derniers caractères dans la `OperationId` propriété :</span><span class="sxs-lookup"><span data-stu-id="d6555-287">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="d6555-288">La `Startup.ConfigureServices` méthode crée plusieurs inscriptions de la `Operation` classe en fonction des durées de vie nommées :</span><span class="sxs-lookup"><span data-stu-id="d6555-288">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="d6555-289">L’exemple d’application montre les durées de vie des objets dans et entre les demandes.</span><span class="sxs-lookup"><span data-stu-id="d6555-289">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="d6555-290">Le `IndexModel` et l’intergiciel (middleware) demandent chaque genre de `IOperation` type et journalisent `OperationId` pour chacun d’entre eux :</span><span class="sxs-lookup"><span data-stu-id="d6555-290">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="d6555-291">À l’instar de `IndexModel` , l’intergiciel (middleware) résout les mêmes services :</span><span class="sxs-lookup"><span data-stu-id="d6555-291">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="d6555-292">Les services délimités doivent être résolus dans la `InvokeAsync` méthode :</span><span class="sxs-lookup"><span data-stu-id="d6555-292">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="d6555-293">La sortie de l’enregistreur d’événements affiche :</span><span class="sxs-lookup"><span data-stu-id="d6555-293">The logger output shows:</span></span>

* <span data-ttu-id="d6555-294">Les objets *Transient* sont toujours différents.</span><span class="sxs-lookup"><span data-stu-id="d6555-294">*Transient* objects are always different.</span></span> <span data-ttu-id="d6555-295">La `OperationId` valeur transitoire est différente dans `IndexModel` et dans l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="d6555-295">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="d6555-296">Les objets *délimités* sont les mêmes pour chaque demande, mais différents dans chaque demande.</span><span class="sxs-lookup"><span data-stu-id="d6555-296">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="d6555-297">Les objets *Singleton* sont les mêmes pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="d6555-297">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="d6555-298">Pour réduire la sortie de journalisation, définissez « journalisation : LogLevel : Microsoft : erreur » dans le *appsettings.Development.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="d6555-298">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="d6555-299">Appeler des services à partir de Main</span><span class="sxs-lookup"><span data-stu-id="d6555-299">Call services from main</span></span>

<span data-ttu-id="d6555-300">Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) pour résoudre un service délimité dans l’étendue de l’application.</span><span class="sxs-lookup"><span data-stu-id="d6555-300">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="d6555-301">Cette approche est pratique pour accéder à un service Scoped au démarrage pour exécuter des tâches d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="d6555-301">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="d6555-302">L’exemple suivant montre comment accéder au `IMyDependency` service étendu et appeler sa `WriteMessage` méthode dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="d6555-302">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="d6555-303">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="d6555-303">Scope validation</span></span>

<span data-ttu-id="d6555-304">Lorsque l’application s’exécute dans l' [environnement de développement](xref:fundamentals/environments) et appelle [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) pour générer l’hôte, le fournisseur de services par défaut effectue des vérifications pour vérifier les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="d6555-304">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d6555-305">Les services délimités ne sont pas résolus à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="d6555-305">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="d6555-306">Les services délimités ne sont pas injectés dans les singletons.</span><span class="sxs-lookup"><span data-stu-id="d6555-306">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="d6555-307">Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> est appelé.</span><span class="sxs-lookup"><span data-stu-id="d6555-307">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="d6555-308">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application lorsque le fournisseur démarre avec l’application et est supprimée lorsque l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d6555-308">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d6555-309">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="d6555-309">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d6555-310">Si un service étendu est créé dans le conteneur racine, la durée de vie du service est effectivement promue en Singleton, car il est supprimé uniquement par le conteneur racine lorsque l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d6555-310">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="d6555-311">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="d6555-311">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d6555-312">Pour plus d’informations, consultez [Validation de l’étendue](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="d6555-312">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="d6555-313">Services de requête</span><span class="sxs-lookup"><span data-stu-id="d6555-313">Request Services</span></span>

<span data-ttu-id="d6555-314">Les services disponibles dans une demande de ASP.NET Core sont exposés via la collection [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="d6555-314">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="d6555-315">Lorsque des services sont demandés à l’intérieur d’une demande, les services et leurs dépendances sont résolus à partir de la `RequestServices` collection.</span><span class="sxs-lookup"><span data-stu-id="d6555-315">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="d6555-316">L’infrastructure crée une portée par demande et `RequestServices` expose le fournisseur de services étendus.</span><span class="sxs-lookup"><span data-stu-id="d6555-316">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="d6555-317">Tous les services délimités sont valides tant que la demande est active.</span><span class="sxs-lookup"><span data-stu-id="d6555-317">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="d6555-318">Préférez demander des dépendances en tant que paramètres de constructeur pour résoudre les services à partir de la `RequestServices` collection.</span><span class="sxs-lookup"><span data-stu-id="d6555-318">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="d6555-319">Il en résulte des classes qui sont plus faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="d6555-319">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="d6555-320">Conception de services pour l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="d6555-320">Design services for dependency injection</span></span>

<span data-ttu-id="d6555-321">Lors de la conception de services pour l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="d6555-321">When designing services for dependency injection:</span></span>

* <span data-ttu-id="d6555-322">Évitez les classes et les membres avec état, static.</span><span class="sxs-lookup"><span data-stu-id="d6555-322">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="d6555-323">Évitez de créer un état global en concevant des applications qui utilisent des services Singleton à la place.</span><span class="sxs-lookup"><span data-stu-id="d6555-323">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="d6555-324">Éviter une instanciation directe de classes dépendantes au sein de services.</span><span class="sxs-lookup"><span data-stu-id="d6555-324">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="d6555-325">L’instanciation directe associe le code à une implémentation particulière.</span><span class="sxs-lookup"><span data-stu-id="d6555-325">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="d6555-326">Rendez les services petits, bien factorisés et faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="d6555-326">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="d6555-327">Si une classe possède un grand nombre de dépendances injectées, il est possible qu’il s’agit d’un signe que la classe a trop de responsabilités et viole le [principe de responsabilité unique (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="d6555-327">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="d6555-328">Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans de nouvelles classes.</span><span class="sxs-lookup"><span data-stu-id="d6555-328">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="d6555-329">Gardez à l’esprit que :::no-loc(Razor)::: les classes de modèle de page de pages et les classes de contrôleur MVC doivent se concentrer sur les préoccupations de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d6555-329">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="d6555-330">Suppression des services</span><span class="sxs-lookup"><span data-stu-id="d6555-330">Disposal of services</span></span>

<span data-ttu-id="d6555-331">Le conteneur appelle <xref:System.IDisposable.Dispose%2A> pour les types <xref:System.IDisposable> qu’il crée.</span><span class="sxs-lookup"><span data-stu-id="d6555-331">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="d6555-332">Les services résolus à partir du conteneur ne doivent jamais être supprimés par le développeur.</span><span class="sxs-lookup"><span data-stu-id="d6555-332">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="d6555-333">Si un type ou une fabrique est inscrit en tant que singleton, le conteneur supprime automatiquement le singleton.</span><span class="sxs-lookup"><span data-stu-id="d6555-333">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="d6555-334">Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="d6555-334">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="d6555-335">La console de débogage affiche la sortie suivante après chaque actualisation de la page d’index :</span><span class="sxs-lookup"><span data-stu-id="d6555-335">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="d6555-336">Services non créés par le conteneur de service</span><span class="sxs-lookup"><span data-stu-id="d6555-336">Services not created by the service container</span></span>

<span data-ttu-id="d6555-337">Considérez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="d6555-337">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="d6555-338">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="d6555-338">In the preceding code:</span></span>

* <span data-ttu-id="d6555-339">Les instances de service ne sont pas créées par le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="d6555-339">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="d6555-340">L’infrastructure ne supprime pas automatiquement les services.</span><span class="sxs-lookup"><span data-stu-id="d6555-340">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="d6555-341">Le développeur est responsable de la suppression des services.</span><span class="sxs-lookup"><span data-stu-id="d6555-341">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="d6555-342">Recommandations IDisposable pour les instances temporaires et partagées</span><span class="sxs-lookup"><span data-stu-id="d6555-342">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="d6555-343">Transitoire, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="d6555-343">Transient, limited lifetime</span></span>

<span data-ttu-id="d6555-344">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="d6555-344">**Scenario**</span></span>

<span data-ttu-id="d6555-345">L’application requiert une <xref:System.IDisposable> instance avec une durée de vie transitoire pour l’un des scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="d6555-345">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="d6555-346">L’instance est résolue dans l’étendue racine (conteneur racine).</span><span class="sxs-lookup"><span data-stu-id="d6555-346">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="d6555-347">L’instance doit être supprimée avant la fin de l’étendue.</span><span class="sxs-lookup"><span data-stu-id="d6555-347">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="d6555-348">**Solution**</span><span class="sxs-lookup"><span data-stu-id="d6555-348">**Solution**</span></span>

<span data-ttu-id="d6555-349">Utilisez le modèle de fabrique pour créer une instance en dehors de l’étendue parente.</span><span class="sxs-lookup"><span data-stu-id="d6555-349">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="d6555-350">Dans ce cas, l’application a généralement une `Create` méthode qui appelle directement le constructeur du type final.</span><span class="sxs-lookup"><span data-stu-id="d6555-350">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="d6555-351">Si le type final a d’autres dépendances, la fabrique peut :</span><span class="sxs-lookup"><span data-stu-id="d6555-351">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="d6555-352">Recevoir un <xref:System.IServiceProvider> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="d6555-352">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="d6555-353">Utilisez <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> pour instancier l’instance en dehors du conteneur, tout en utilisant le conteneur pour ses dépendances.</span><span class="sxs-lookup"><span data-stu-id="d6555-353">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="d6555-354">Instance partagée, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="d6555-354">Shared instance, limited lifetime</span></span>

<span data-ttu-id="d6555-355">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="d6555-355">**Scenario**</span></span>

<span data-ttu-id="d6555-356">L’application requiert une <xref:System.IDisposable> instance partagée sur plusieurs services, mais l' <xref:System.IDisposable> instance doit avoir une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="d6555-356">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="d6555-357">**Solution**</span><span class="sxs-lookup"><span data-stu-id="d6555-357">**Solution**</span></span>

<span data-ttu-id="d6555-358">Inscrivez l’instance avec une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="d6555-358">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="d6555-359">Utilisez <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> pour créer un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="d6555-359">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="d6555-360">Utilisez les étendues <xref:System.IServiceProvider> pour accéder aux services requis.</span><span class="sxs-lookup"><span data-stu-id="d6555-360">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="d6555-361">Supprimez l’étendue lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="d6555-361">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="d6555-362">Instructions IDisposable générales</span><span class="sxs-lookup"><span data-stu-id="d6555-362">General IDisposable guidelines</span></span>

* <span data-ttu-id="d6555-363">N’inscrivez pas <xref:System.IDisposable> les instances avec une durée de vie transitoire.</span><span class="sxs-lookup"><span data-stu-id="d6555-363">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="d6555-364">Utilisez à la place le modèle de fabrique.</span><span class="sxs-lookup"><span data-stu-id="d6555-364">Use the factory pattern instead.</span></span>
* <span data-ttu-id="d6555-365">Ne résolvez pas <xref:System.IDisposable> les instances avec une durée de vie transitoire ou limitée dans l’étendue racine.</span><span class="sxs-lookup"><span data-stu-id="d6555-365">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="d6555-366">La seule exception à cela est que l’application crée/recrée et supprime <xref:System.IServiceProvider> , mais ce n’est pas un modèle idéal.</span><span class="sxs-lookup"><span data-stu-id="d6555-366">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="d6555-367">La réception d’une <xref:System.IDisposable> dépendance via l’injection de dépendances ne nécessite pas que le récepteur s’implémente <xref:System.IDisposable> lui-même.</span><span class="sxs-lookup"><span data-stu-id="d6555-367">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="d6555-368">Le récepteur de la <xref:System.IDisposable> dépendance ne doit pas appeler <xref:System.IDisposable.Dispose%2A> sur cette dépendance.</span><span class="sxs-lookup"><span data-stu-id="d6555-368">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="d6555-369">Utilisez des étendues pour contrôler les durées de vie des services.</span><span class="sxs-lookup"><span data-stu-id="d6555-369">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="d6555-370">Les étendues ne sont pas hiérarchiques, et il n’existe pas de connexion spéciale entre les étendues.</span><span class="sxs-lookup"><span data-stu-id="d6555-370">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="d6555-371">Remplacement de conteneur de services par défaut</span><span class="sxs-lookup"><span data-stu-id="d6555-371">Default service container replacement</span></span>

<span data-ttu-id="d6555-372">Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public.</span><span class="sxs-lookup"><span data-stu-id="d6555-372">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="d6555-373">Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique qu’il ne prend pas en charge, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d6555-373">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="d6555-374">Injection de propriétés</span><span class="sxs-lookup"><span data-stu-id="d6555-374">Property injection</span></span>
* <span data-ttu-id="d6555-375">Injection en fonction du nom</span><span class="sxs-lookup"><span data-stu-id="d6555-375">Injection based on name</span></span>
* <span data-ttu-id="d6555-376">Conteneurs enfants</span><span class="sxs-lookup"><span data-stu-id="d6555-376">Child containers</span></span>
* <span data-ttu-id="d6555-377">Gestion personnalisée de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="d6555-377">Custom lifetime management</span></span>
* <span data-ttu-id="d6555-378">`Func<T>` prend en charge l’initialisation tardive</span><span class="sxs-lookup"><span data-stu-id="d6555-378">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="d6555-379">Inscription basée sur une convention</span><span class="sxs-lookup"><span data-stu-id="d6555-379">Convention-based registration</span></span>

<span data-ttu-id="d6555-380">Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="d6555-380">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="d6555-381">Autofac</span><span class="sxs-lookup"><span data-stu-id="d6555-381">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="d6555-382">DryIoc</span><span class="sxs-lookup"><span data-stu-id="d6555-382">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="d6555-383">Grace</span><span class="sxs-lookup"><span data-stu-id="d6555-383">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="d6555-384">LightInject</span><span class="sxs-lookup"><span data-stu-id="d6555-384">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="d6555-385">Lamar</span><span class="sxs-lookup"><span data-stu-id="d6555-385">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="d6555-386">Stashbox</span><span class="sxs-lookup"><span data-stu-id="d6555-386">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="d6555-387">Unity</span><span class="sxs-lookup"><span data-stu-id="d6555-387">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="d6555-388">Sécurité des threads</span><span class="sxs-lookup"><span data-stu-id="d6555-388">Thread safety</span></span>

<span data-ttu-id="d6555-389">Créez des services singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d6555-389">Create thread-safe singleton services.</span></span> <span data-ttu-id="d6555-390">Si un service Singleton a une dépendance vis-à-vis d’un service temporaire, le service temporaire peut également nécessiter la sécurité des threads en fonction de la façon dont il est utilisé par le singleton.</span><span class="sxs-lookup"><span data-stu-id="d6555-390">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="d6555-391">La méthode de fabrique d’un service unique, telle que le deuxième argument de [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), n’a pas besoin d’être thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d6555-391">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="d6555-392">À l’instar d’un constructeur de type ( `static` ), il est garanti qu’il soit appelé une seule fois par un seul thread.</span><span class="sxs-lookup"><span data-stu-id="d6555-392">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d6555-393">Recommandations</span><span class="sxs-lookup"><span data-stu-id="d6555-393">Recommendations</span></span>

* <span data-ttu-id="d6555-394">`async/await` et la `Task` résolution de service basée sur n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="d6555-394">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="d6555-395">Étant donné que C# ne prend pas en charge les constructeurs asynchrones, utilisez des méthodes asynchrones après la résolution synchrone du service.</span><span class="sxs-lookup"><span data-stu-id="d6555-395">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="d6555-396">Évitez de stocker des données et des configurations directement dans le conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="d6555-396">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="d6555-397">Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="d6555-397">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="d6555-398">La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="d6555-398">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d6555-399">De même, évitez les objets « garde-données » qui existent uniquement pour autoriser l’accès à un autre objet.</span><span class="sxs-lookup"><span data-stu-id="d6555-399">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="d6555-400">Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="d6555-400">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="d6555-401">Évitez l’accès statique aux services.</span><span class="sxs-lookup"><span data-stu-id="d6555-401">Avoid static access to services.</span></span> <span data-ttu-id="d6555-402">Par exemple, évitez de capturer [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) en tant que champ ou propriété statique à utiliser ailleurs.</span><span class="sxs-lookup"><span data-stu-id="d6555-402">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="d6555-403">Conservez les fabriques DI en mode rapide et synchrone.</span><span class="sxs-lookup"><span data-stu-id="d6555-403">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="d6555-404">Évitez d’utiliser le *modèle de localisation de service* .</span><span class="sxs-lookup"><span data-stu-id="d6555-404">Avoid using the *service locator pattern* .</span></span> <span data-ttu-id="d6555-405">Par exemple, n’appelez pas <xref:System.IServiceProvider.GetService%2A> pour obtenir une instance de service si vous pouvez utiliser l’injection de dépendance à la place :</span><span class="sxs-lookup"><span data-stu-id="d6555-405">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="d6555-406">**Incorrect :**</span><span class="sxs-lookup"><span data-stu-id="d6555-406">**Incorrect:**</span></span>

    ![Code incorrect](dependency-injection/_static/bad.png)

  <span data-ttu-id="d6555-408">**Correct** :</span><span class="sxs-lookup"><span data-stu-id="d6555-408">**Correct** :</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```
* <span data-ttu-id="d6555-409">Une autre variante du localisateur de service à éviter est l’injection d’une fabrique qui résout les dépendances au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="d6555-409">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="d6555-410">Ces deux pratiques combinent des stratégies [Inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="d6555-410">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="d6555-411">Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="d6555-411">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="d6555-412">Évitez les appels à <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d6555-412">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="d6555-413">L’appel de `BuildServiceProvider` se produit généralement lorsque le développeur souhaite résoudre un service dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d6555-413">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="d6555-414">Par exemple, considérez le cas où le `LoginPath` est chargé à partir de la configuration.</span><span class="sxs-lookup"><span data-stu-id="d6555-414">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="d6555-415">Évitez l’approche suivante :</span><span class="sxs-lookup"><span data-stu-id="d6555-415">Avoid the following approach:</span></span>

  ![code incorrect appelant BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="d6555-417">Dans l’image précédente, la sélection de la ligne ondulée verte sous `services.BuildServiceProvider` affiche l’avertissement ASP0000 suivant :</span><span class="sxs-lookup"><span data-stu-id="d6555-417">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="d6555-418">ASP0000 l’appel de’BuildServiceProvider’à partir du code de l’application entraîne la création d’une copie supplémentaire des services Singleton.</span><span class="sxs-lookup"><span data-stu-id="d6555-418">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="d6555-419">Envisagez des alternatives telles que la dépendance injectant des services en tant que paramètres pour « configurer ».</span><span class="sxs-lookup"><span data-stu-id="d6555-419">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="d6555-420">L’appel `BuildServiceProvider` de crée un deuxième conteneur, qui peut créer des singletons endommagés et provoquer des références aux graphiques d’objets sur plusieurs conteneurs.</span><span class="sxs-lookup"><span data-stu-id="d6555-420">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="d6555-421">Une bonne façon d’obtenir `LoginPath` est d’utiliser la prise en charge intégrée du modèle d’options pour di :</span><span class="sxs-lookup"><span data-stu-id="d6555-421">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="d6555-422">Les services transitoires jetables sont capturés par le conteneur pour la suppression.</span><span class="sxs-lookup"><span data-stu-id="d6555-422">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="d6555-423">Cela peut entraîner une fuite de mémoire si elle est résolue à partir du conteneur de niveau supérieur.</span><span class="sxs-lookup"><span data-stu-id="d6555-423">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="d6555-424">Activez la validation de l’étendue pour vous assurer que l’application n’a pas de singletons qui capturent les services délimités.</span><span class="sxs-lookup"><span data-stu-id="d6555-424">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="d6555-425">Pour plus d’informations, consultez [Validation de l’étendue](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="d6555-425">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="d6555-426">Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation.</span><span class="sxs-lookup"><span data-stu-id="d6555-426">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="d6555-427">Les exceptions sont rares, principalement des cas spéciaux dans l’infrastructure elle-même.</span><span class="sxs-lookup"><span data-stu-id="d6555-427">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="d6555-428">L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux.</span><span class="sxs-lookup"><span data-stu-id="d6555-428">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="d6555-429">Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.</span><span class="sxs-lookup"><span data-stu-id="d6555-429">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="d6555-430">Modèles recommandés pour l’architecture mutualisée dans DI</span><span class="sxs-lookup"><span data-stu-id="d6555-430">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="d6555-431">Le [noyau du verger](https://github.com/OrchardCMS/OrchardCore) est une infrastructure d’application permettant de créer des applications modulaires à plusieurs locataires sur ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="d6555-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="d6555-432">Pour plus d’informations, consultez la documentation sur le [noyau du verger](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="d6555-432">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="d6555-433">Consultez les [exemples de noyaux de verger](https://github.com/OrchardCMS/OrchardCore.Samples) pour obtenir des exemples montrant comment créer des applications modulaires et mutualisées à l’aide de l’infrastructure principale du verger sans aucune de ses fonctionnalités propres à CMS.</span><span class="sxs-lookup"><span data-stu-id="d6555-433">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d6555-434">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="d6555-434">Framework-provided services</span></span>

<span data-ttu-id="d6555-435">La `Startup.ConfigureServices` méthode enregistre les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="d6555-435">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="d6555-436">Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="d6555-436">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="d6555-437">Pour les applications basées sur les modèles de ASP.NET Core, le Framework inscrit plus de 250 services.</span><span class="sxs-lookup"><span data-stu-id="d6555-437">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="d6555-438">Le tableau suivant répertorie un petit exemple de ces services inscrits au Framework :</span><span class="sxs-lookup"><span data-stu-id="d6555-438">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="d6555-439">Type de service</span><span class="sxs-lookup"><span data-stu-id="d6555-439">Service Type</span></span>                                                                                    | <span data-ttu-id="d6555-440">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="d6555-440">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="d6555-441">Temporaire</span><span class="sxs-lookup"><span data-stu-id="d6555-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="d6555-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-442">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="d6555-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-443">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="d6555-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-444">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="d6555-445">Temporaire</span><span class="sxs-lookup"><span data-stu-id="d6555-445">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="d6555-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="d6555-447">Temporaire</span><span class="sxs-lookup"><span data-stu-id="d6555-447">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="d6555-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-448">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="d6555-449">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-449">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="d6555-450">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-450">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="d6555-451">Temporaire</span><span class="sxs-lookup"><span data-stu-id="d6555-451">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="d6555-452">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-452">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="d6555-453">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-453">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="d6555-454">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-454">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="d6555-455">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d6555-455">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="d6555-456">Modèles de conférence norvégiens pour le développement d’applications DI</span><span class="sxs-lookup"><span data-stu-id="d6555-456">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="d6555-457">Quatre méthodes pour supprimer des IDisposable dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6555-457">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="d6555-458">Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)</span><span class="sxs-lookup"><span data-stu-id="d6555-458">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="d6555-459">Principe des dépendances explicites</span><span class="sxs-lookup"><span data-stu-id="d6555-459">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="d6555-460">Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="d6555-460">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="d6555-461">Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6555-461">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d6555-462">Par [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)et [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="d6555-462">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="d6555-463">ASP.NET Core prend en charge le modèle de conception logicielle d’injection de dépendances, technique qui permet d’obtenir une [inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="d6555-463">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="d6555-464">Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d6555-464">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="d6555-465">[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d6555-465">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="d6555-466">Vue d’ensemble de l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="d6555-466">Overview of dependency injection</span></span>

<span data-ttu-id="d6555-467">Une *dépendance* est un objet qui nécessite un autre objet.</span><span class="sxs-lookup"><span data-stu-id="d6555-467">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="d6555-468">Examinez la classe `MyDependency` suivante avec une méthode `WriteMessage` dont dépendent d’autres classes dans une application :</span><span class="sxs-lookup"><span data-stu-id="d6555-468">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="d6555-469">Une instance de la classe `MyDependency` peut être créée pour rendre la méthode `WriteMessage` disponible pour une classe.</span><span class="sxs-lookup"><span data-stu-id="d6555-469">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="d6555-470">La classe `MyDependency` est une dépendance de la classe `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="d6555-470">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="d6555-471">La classe est créee et dépend directement de l’instance `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d6555-471">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="d6555-472">Les dépendances de code (comme l’exemple précédent) posent problème et doivent être évitées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="d6555-472">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="d6555-473">Pour remplacer `MyDependency` par une autre implémentation, la classe doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="d6555-473">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="d6555-474">Si `MyDependency` possède des dépendances, elles doivent être configurées par la classe.</span><span class="sxs-lookup"><span data-stu-id="d6555-474">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="d6555-475">Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.</span><span class="sxs-lookup"><span data-stu-id="d6555-475">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="d6555-476">Cette implémentation complique le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="d6555-476">This implementation is difficult to unit test.</span></span> <span data-ttu-id="d6555-477">L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="d6555-477">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="d6555-478">L’injection de dépendances résout ces problèmes via :</span><span class="sxs-lookup"><span data-stu-id="d6555-478">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="d6555-479">L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.</span><span class="sxs-lookup"><span data-stu-id="d6555-479">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="d6555-480">L’inscription de la dépendance dans un conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="d6555-480">Registration of the dependency in a service container.</span></span> <span data-ttu-id="d6555-481">ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="d6555-481">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d6555-482">Les services sont inscrits dans la méthode `Startup.ConfigureServices` de l’application.</span><span class="sxs-lookup"><span data-stu-id="d6555-482">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d6555-483">*Injection* du service dans le constructeur de la classe où il est utilisé.</span><span class="sxs-lookup"><span data-stu-id="d6555-483">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="d6555-484">Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="d6555-484">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="d6555-485">Dans l’[exemple d’application](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l’interface `IMyDependency` définit une méthode que le service fournit à l’application :</span><span class="sxs-lookup"><span data-stu-id="d6555-485">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="d6555-486">Cette interface est implémentée par un type concret, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="d6555-486">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="d6555-487">`MyDependency` exige un <xref:Microsoft.Extensions.Logging.ILogger`1> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="d6555-487">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="d6555-488">Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée.</span><span class="sxs-lookup"><span data-stu-id="d6555-488">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="d6555-489">Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances.</span><span class="sxs-lookup"><span data-stu-id="d6555-489">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="d6555-490">Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu.</span><span class="sxs-lookup"><span data-stu-id="d6555-490">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="d6555-491">L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances* , *graphique de dépendance* ou *graphique d’objet* .</span><span class="sxs-lookup"><span data-stu-id="d6555-491">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph* .</span></span>

<span data-ttu-id="d6555-492">`IMyDependency` et `ILogger<TCategoryName>` doivent être inscrits dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="d6555-492">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="d6555-493">`IMyDependency` est inscrit dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d6555-493">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d6555-494">`ILogger<TCategoryName>` est inscrit par l’infrastructure d’abstractions de journalisation. Il s’agit donc d’un [service fourni par le framework](#framework-provided-services) et inscrit par défaut par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="d6555-494">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="d6555-495">Le conteneur résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui élimine la nécessité d’inscrire chaque [type construit (générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types) :</span><span class="sxs-lookup"><span data-stu-id="d6555-495">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="d6555-496">Dans l’exemple d’application, le service `IMyDependency` est inscrit avec le type concret `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d6555-496">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="d6555-497">L’inscription ajuste la durée de vie du service à la durée de vie d’une requête unique.</span><span class="sxs-lookup"><span data-stu-id="d6555-497">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="d6555-498">Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="d6555-498">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="d6555-499">Chaque méthode d’extension `services.Add{SERVICE_NAME}` ajoute (et éventuellement configure) des services.</span><span class="sxs-lookup"><span data-stu-id="d6555-499">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="d6555-500">Par exemple, `services.AddMvc()` ajoute les pages de services :::no-loc(Razor)::: et MVC requièrent.</span><span class="sxs-lookup"><span data-stu-id="d6555-500">For example, `services.AddMvc()` adds the services :::no-loc(Razor)::: Pages and MVC require.</span></span> <span data-ttu-id="d6555-501">Il est recommandé que les applications suivent cette convention.</span><span class="sxs-lookup"><span data-stu-id="d6555-501">We recommended that apps follow this convention.</span></span> <span data-ttu-id="d6555-502">Placez les méthodes d’extension dans l’espace de noms [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pour encapsuler des groupes d’inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="d6555-502">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="d6555-503">Si le constructeur du service exige un [type intégré](/dotnet/csharp/language-reference/keywords/built-in-types-table), comme un `string`, le type peut être injecté à l’aide de la [configuration](xref:fundamentals/configuration/index) ou du [modèle d’options](xref:fundamentals/configuration/options) :</span><span class="sxs-lookup"><span data-stu-id="d6555-503">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="d6555-504">Une instance du service est demandée via le constructeur d’une classe dans laquelle le service est utilisé et assigné à un champ privé.</span><span class="sxs-lookup"><span data-stu-id="d6555-504">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="d6555-505">Le champ est utilisé pour accéder au service en fonction des besoins tout au long de la classe.</span><span class="sxs-lookup"><span data-stu-id="d6555-505">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="d6555-506">Dans l’exemple d’application, l’instance `IMyDependency` est demandée et utilisée pour appeler la méthode `WriteMessage` du service :</span><span class="sxs-lookup"><span data-stu-id="d6555-506">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="d6555-507">Services injectés au démarrage</span><span class="sxs-lookup"><span data-stu-id="d6555-507">Services injected into Startup</span></span>

<span data-ttu-id="d6555-508">Seuls les types de service suivants peuvent être injectés dans le `Startup` constructeur lors de l’utilisation de l’hôte générique ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ) :</span><span class="sxs-lookup"><span data-stu-id="d6555-508">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="d6555-509">Les services peuvent être injectés dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d6555-509">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="d6555-510">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d6555-510">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d6555-511">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="d6555-511">Framework-provided services</span></span>

<span data-ttu-id="d6555-512">La `Startup.ConfigureServices` méthode est chargée de définir les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="d6555-512">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="d6555-513">Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="d6555-513">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="d6555-514">Il n’est pas rare qu’une application basée sur un modèle de ASP.NET Core dispose de centaines de services enregistrés par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="d6555-514">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="d6555-515">Un petit exemple de services inscrits au Framework est répertorié dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="d6555-515">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="d6555-516">Type de service</span><span class="sxs-lookup"><span data-stu-id="d6555-516">Service Type</span></span> | <span data-ttu-id="d6555-517">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="d6555-517">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="d6555-518">Temporaire</span><span class="sxs-lookup"><span data-stu-id="d6555-518">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="d6555-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-519">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="d6555-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-520">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="d6555-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-521">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="d6555-522">Temporaire</span><span class="sxs-lookup"><span data-stu-id="d6555-522">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="d6555-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-523">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="d6555-524">Temporaire</span><span class="sxs-lookup"><span data-stu-id="d6555-524">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="d6555-525">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-525">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="d6555-526">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-526">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="d6555-527">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-527">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="d6555-528">Temporaire</span><span class="sxs-lookup"><span data-stu-id="d6555-528">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="d6555-529">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-529">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="d6555-530">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-530">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="d6555-531">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-531">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="d6555-532">Inscrire des services supplémentaires avec les méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="d6555-532">Register additional services with extension methods</span></span>

<span data-ttu-id="d6555-533">Lorsqu’une méthode d’extension de collection de services est disponible pour inscrire un service (et ses services dépendants, si nécessaire), la convention consiste à utiliser une seule méthode d’extension `Add{SERVICE_NAME}` pour inscrire tous les services requis par ce service.</span><span class="sxs-lookup"><span data-stu-id="d6555-533">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="d6555-534">Le code suivant est un exemple de la façon d’ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) et <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*> :</span><span class="sxs-lookup"><span data-stu-id="d6555-534">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="d6555-535">Pour plus d’informations, consultez la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> dans la documentation de l’API.</span><span class="sxs-lookup"><span data-stu-id="d6555-535">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="d6555-536">Durées de service</span><span class="sxs-lookup"><span data-stu-id="d6555-536">Service lifetimes</span></span>

<span data-ttu-id="d6555-537">Choisissez une durée de vie appropriée pour chaque service inscrit.</span><span class="sxs-lookup"><span data-stu-id="d6555-537">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="d6555-538">Vous pouvez configurer les services ASP.NET Core avec les durées de vie suivantes :</span><span class="sxs-lookup"><span data-stu-id="d6555-538">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="d6555-539">Temporaire</span><span class="sxs-lookup"><span data-stu-id="d6555-539">Transient</span></span>

<span data-ttu-id="d6555-540">Des services à durée de vie temporaire (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="d6555-540">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="d6555-541">Cette durée de vie convient parfaitement aux services légers et sans état.</span><span class="sxs-lookup"><span data-stu-id="d6555-541">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="d6555-542">Dans les applications qui traitent les demandes, les services transitoires sont supprimés à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="d6555-542">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="d6555-543">Délimité</span><span class="sxs-lookup"><span data-stu-id="d6555-543">Scoped</span></span>

<span data-ttu-id="d6555-544">Les services à durée de vie délimitée (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) sont créés une seule fois par requête de client (connexion).</span><span class="sxs-lookup"><span data-stu-id="d6555-544">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="d6555-545">Dans les applications qui traitent les requêtes, les services délimités sont supprimés à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="d6555-545">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="d6555-546">Si vous utilisez un service Scoped dans un middleware, injectez le service dans la méthode `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="d6555-546">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="d6555-547">N’injectez pas via l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) , car cela force le service à se comporter comme un singleton.</span><span class="sxs-lookup"><span data-stu-id="d6555-547">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="d6555-548">Pour plus d'informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="d6555-548">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="d6555-549">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6555-549">Singleton</span></span>

<span data-ttu-id="d6555-550">Les services avec une durée de vie singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) sont créés la première fois qu’ils sont demandés (ou quand `Startup.ConfigureServices` est exécuté et qu’une instance est spécifiée avec l’inscription du service).</span><span class="sxs-lookup"><span data-stu-id="d6555-550">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="d6555-551">Chaque requête ultérieure utilise la même instance.</span><span class="sxs-lookup"><span data-stu-id="d6555-551">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="d6555-552">Si l’application exige un comportement singleton, il est recommandé d’autoriser le conteneur de service à gérer la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="d6555-552">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="d6555-553">N’implémentez pas le modèle de conception singleton et fournissez le code utilisateur pour gérer la durée de vie de l’objet dans la classe.</span><span class="sxs-lookup"><span data-stu-id="d6555-553">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="d6555-554">Dans les applications qui traitent les requêtes, les services Singleton sont supprimés lorsque le <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> est supprimé au moment de l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="d6555-554">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="d6555-555">Il est dangereux de résoudre un service délimité depuis un singleton.</span><span class="sxs-lookup"><span data-stu-id="d6555-555">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d6555-556">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="d6555-556">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="d6555-557">Méthodes d’inscription du service</span><span class="sxs-lookup"><span data-stu-id="d6555-557">Service registration methods</span></span>

<span data-ttu-id="d6555-558">Les méthodes d’extension d’inscription de service offrent des surcharges qui sont utiles dans des scénarios spécifiques.</span><span class="sxs-lookup"><span data-stu-id="d6555-558">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="d6555-559">Méthode</span><span class="sxs-lookup"><span data-stu-id="d6555-559">Method</span></span> | <span data-ttu-id="d6555-560">Automatique</span><span class="sxs-lookup"><span data-stu-id="d6555-560">Automatic</span></span><br><span data-ttu-id="d6555-561">object</span><span class="sxs-lookup"><span data-stu-id="d6555-561">object</span></span><br><span data-ttu-id="d6555-562">suppression</span><span class="sxs-lookup"><span data-stu-id="d6555-562">disposal</span></span> | <span data-ttu-id="d6555-563">Multiple</span><span class="sxs-lookup"><span data-stu-id="d6555-563">Multiple</span></span><br><span data-ttu-id="d6555-564">implémentations</span><span class="sxs-lookup"><span data-stu-id="d6555-564">implementations</span></span> | <span data-ttu-id="d6555-565">Passage d’args</span><span class="sxs-lookup"><span data-stu-id="d6555-565">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="d6555-566">Exemple :</span><span class="sxs-lookup"><span data-stu-id="d6555-566">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="d6555-567">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-567">Yes</span></span> | <span data-ttu-id="d6555-568">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-568">Yes</span></span> | <span data-ttu-id="d6555-569">Non</span><span class="sxs-lookup"><span data-stu-id="d6555-569">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="d6555-570">Exemples :</span><span class="sxs-lookup"><span data-stu-id="d6555-570">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="d6555-571">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-571">Yes</span></span> | <span data-ttu-id="d6555-572">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-572">Yes</span></span> | <span data-ttu-id="d6555-573">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-573">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="d6555-574">Exemple :</span><span class="sxs-lookup"><span data-stu-id="d6555-574">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="d6555-575">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-575">Yes</span></span> | <span data-ttu-id="d6555-576">Non</span><span class="sxs-lookup"><span data-stu-id="d6555-576">No</span></span> | <span data-ttu-id="d6555-577">Non</span><span class="sxs-lookup"><span data-stu-id="d6555-577">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="d6555-578">Exemples :</span><span class="sxs-lookup"><span data-stu-id="d6555-578">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="d6555-579">Non</span><span class="sxs-lookup"><span data-stu-id="d6555-579">No</span></span> | <span data-ttu-id="d6555-580">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-580">Yes</span></span> | <span data-ttu-id="d6555-581">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-581">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="d6555-582">Exemples :</span><span class="sxs-lookup"><span data-stu-id="d6555-582">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="d6555-583">Non</span><span class="sxs-lookup"><span data-stu-id="d6555-583">No</span></span> | <span data-ttu-id="d6555-584">Non</span><span class="sxs-lookup"><span data-stu-id="d6555-584">No</span></span> | <span data-ttu-id="d6555-585">Oui</span><span class="sxs-lookup"><span data-stu-id="d6555-585">Yes</span></span> |

<span data-ttu-id="d6555-586">Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="d6555-586">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="d6555-587">Un scénario courant d’implémentations multiples est la [simulation de types à des fins de test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="d6555-587">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="d6555-588">L’inscription d’un service avec un seul type d’implémentation équivaut à inscrire ce service avec le même type de service et l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="d6555-588">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="d6555-589">C’est pourquoi plusieurs implémentations d’un service ne peuvent pas être inscrites à l’aide des méthodes qui n’acceptent pas un type de service explicite.</span><span class="sxs-lookup"><span data-stu-id="d6555-589">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="d6555-590">Ces méthodes peuvent inscrire plusieurs *instances* d’un service, mais elles auront toutes le même type d' *implémentation* .</span><span class="sxs-lookup"><span data-stu-id="d6555-590">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="d6555-591">L’une des méthodes d’inscription de service ci-dessus peut être utilisée pour inscrire plusieurs instances de service du même type de service.</span><span class="sxs-lookup"><span data-stu-id="d6555-591">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="d6555-592">Dans l’exemple suivant, `AddSingleton` est appelé deux fois avec `IMyDependency` comme type de service.</span><span class="sxs-lookup"><span data-stu-id="d6555-592">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="d6555-593">Le deuxième appel à `AddSingleton` remplace le précédent lorsqu’il est résolu en tant que `IMyDependency` et ajoute au précédent lorsque plusieurs services sont résolus via `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="d6555-593">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="d6555-594">Les services s’affichent dans l’ordre dans lequel ils ont été inscrits lorsqu’ils sont résolus via `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="d6555-594">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="d6555-595">Le Framework fournit également des `TryAdd{LIFETIME}` méthodes d’extension, qui inscrivent le service uniquement si aucune implémentation n’est déjà inscrite.</span><span class="sxs-lookup"><span data-stu-id="d6555-595">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="d6555-596">Dans l’exemple suivant, l’appel à `AddSingleton` s’inscrit `MyDependency` en tant qu’implémentation de `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d6555-596">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="d6555-597">L’appel à n' `TryAddSingleton` a aucun effet, car `IMyDependency` a déjà une implémentation inscrite.</span><span class="sxs-lookup"><span data-stu-id="d6555-597">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="d6555-598">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="d6555-598">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="d6555-599">Les méthodes [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) inscrivent uniquement le service en l’absence d’une implémentation *du même type* .</span><span class="sxs-lookup"><span data-stu-id="d6555-599">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type* .</span></span> <span data-ttu-id="d6555-600">Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="d6555-600">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="d6555-601">Lors de l’inscription de services, le développeur ne souhaite ajouter une instance que si une instance du même type n’a pas déjà été ajoutée.</span><span class="sxs-lookup"><span data-stu-id="d6555-601">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="d6555-602">En général, cette méthode est utilisée par les créateurs de bibliothèque pour éviter d’inscrire deux copies d’une instance dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="d6555-602">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="d6555-603">Dans l’exemple suivant, la première ligne inscrit `MyDep` pour `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="d6555-603">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="d6555-604">La deuxième ligne inscrit `MyDep` pour `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="d6555-604">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="d6555-605">La troisième ligne n’a aucun effet car `IMyDep1` a déjà une implémentation inscrite de `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="d6555-605">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="d6555-606">Comportement d’injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="d6555-606">Constructor injection behavior</span></span>

<span data-ttu-id="d6555-607">Les services peuvent être résolus par deux mécanismes :</span><span class="sxs-lookup"><span data-stu-id="d6555-607">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="d6555-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Autorise la création d’objets sans inscription du service dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="d6555-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="d6555-609">`ActivatorUtilities` est utilisé avec les abstractions orientées utilisateur, telles que les Tag Helpers, les contrôleurs MVC et les classeurs de modèles.</span><span class="sxs-lookup"><span data-stu-id="d6555-609">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="d6555-610">Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="d6555-610">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="d6555-611">Lorsque les services sont résolus par `IServiceProvider` ou `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .</span><span class="sxs-lookup"><span data-stu-id="d6555-611">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="d6555-612">Lorsque les services sont résolus par `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable.</span><span class="sxs-lookup"><span data-stu-id="d6555-612">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="d6555-613">Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="d6555-613">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="d6555-614">Contextes Entity Framework</span><span class="sxs-lookup"><span data-stu-id="d6555-614">Entity Framework contexts</span></span>

<span data-ttu-id="d6555-615">Les contextes Entity Framework sont généralement ajoutés au conteneur de service en utilisant la [durée de vie limitée](#service-lifetimes), car la portée des opérations de base de données d’application web est normalement limitée à la requête du client.</span><span class="sxs-lookup"><span data-stu-id="d6555-615">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="d6555-616">La durée de vie par défaut est définie si une durée de vie n’est pas spécifiée par une surcharge [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) lors de l’enregistrement du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="d6555-616">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="d6555-617">Un service d’une durée de vie donnée ne doit pas utiliser un contexte de base de données dont la durée de vie est plus courte que celle du service.</span><span class="sxs-lookup"><span data-stu-id="d6555-617">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="d6555-618">Options de durée de vie et d’inscription</span><span class="sxs-lookup"><span data-stu-id="d6555-618">Lifetime and registration options</span></span>

<span data-ttu-id="d6555-619">Pour illustrer la différence entre les options de durée de vie et d’inscription, considérez les interfaces suivantes qui représentent des tâches en tant qu’opération avec un identificateur unique, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="d6555-619">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="d6555-620">Selon la façon dont la durée de vie d’un service d’opérations est configurée pour les interfaces suivantes, le conteneur fournit la même instance ou une instance différente du service lorsqu’une classe le demande :</span><span class="sxs-lookup"><span data-stu-id="d6555-620">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="d6555-621">Les interfaces sont implémentées dans la classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="d6555-621">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="d6555-622">Le constructeur `Operation` génère un GUID s’il n’est pas fourni :</span><span class="sxs-lookup"><span data-stu-id="d6555-622">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="d6555-623">Un `OperationService` est inscrit, dépendant de chacun des autres types `Operation`.</span><span class="sxs-lookup"><span data-stu-id="d6555-623">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="d6555-624">Lorsque `OperationService` est demandé via l’injection de dépendances, il reçoit une nouvelle instance de chaque service ou une instance existante en fonction de la durée de vie du service dépendant.</span><span class="sxs-lookup"><span data-stu-id="d6555-624">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="d6555-625">Quand des services temporaires sont créés à la demande à partir du conteneur, le `OperationId` du service `IOperationTransient` est différent du `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="d6555-625">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="d6555-626">`OperationService` reçoit une nouvelle instance de la classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="d6555-626">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="d6555-627">La nouvelle instance génère un autre `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="d6555-627">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="d6555-628">Quand des services délimités sont créés pour chaque requête de client, le `OperationId` du `IOperationScoped` service est identique à celui de `OperationService` au sein d’une requête de client.</span><span class="sxs-lookup"><span data-stu-id="d6555-628">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="d6555-629">Entre les requêtes de client, les deux services partagent une valeur `OperationId` différente.</span><span class="sxs-lookup"><span data-stu-id="d6555-629">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="d6555-630">Quand des services singleton et d’instances singleton sont créés une fois et utilisés sur toutes les requêtes de client et tous les services, le `OperationId` est constant entre toutes les requêtes de service.</span><span class="sxs-lookup"><span data-stu-id="d6555-630">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="d6555-631">Dans `Startup.ConfigureServices`, chaque type est ajouté au conteneur en fonction de sa durée de vie nommée :</span><span class="sxs-lookup"><span data-stu-id="d6555-631">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="d6555-632">Le service `IOperationSingletonInstance` utilise une instance spécifique avec un ID connu `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="d6555-632">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="d6555-633">L’utilisation de ce type est facilement identifiable (son GUID n’affiche que des zéros).</span><span class="sxs-lookup"><span data-stu-id="d6555-633">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="d6555-634">L’exemple d’application montre les durées de vie des objets au sein et entre des requêtes individuelles.</span><span class="sxs-lookup"><span data-stu-id="d6555-634">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="d6555-635">L’exemple d’application `IndexModel` demande chaque type `IOperation` et `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="d6555-635">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="d6555-636">La page affiche ensuite l’ensemble de la classe du modèle de page et des valeurs `OperationId` du service via des assignations de propriété :</span><span class="sxs-lookup"><span data-stu-id="d6555-636">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="d6555-637">Les deux sorties suivantes montrent les résultats de deux requêtes :</span><span class="sxs-lookup"><span data-stu-id="d6555-637">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="d6555-638">**Première requête :**</span><span class="sxs-lookup"><span data-stu-id="d6555-638">**First request:**</span></span>

<span data-ttu-id="d6555-639">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="d6555-639">Controller operations:</span></span>

<span data-ttu-id="d6555-640">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="d6555-640">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="d6555-641">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d6555-641">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d6555-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d6555-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d6555-643">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d6555-643">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d6555-644">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="d6555-644">`OperationService` operations:</span></span>

<span data-ttu-id="d6555-645">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="d6555-645">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="d6555-646">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d6555-646">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d6555-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d6555-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d6555-648">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d6555-648">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d6555-649">**Deuxième requête :**</span><span class="sxs-lookup"><span data-stu-id="d6555-649">**Second request:**</span></span>

<span data-ttu-id="d6555-650">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="d6555-650">Controller operations:</span></span>

<span data-ttu-id="d6555-651">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="d6555-651">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="d6555-652">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d6555-652">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d6555-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d6555-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d6555-654">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d6555-654">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d6555-655">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="d6555-655">`OperationService` operations:</span></span>

<span data-ttu-id="d6555-656">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="d6555-656">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="d6555-657">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d6555-657">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d6555-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d6555-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d6555-659">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d6555-659">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d6555-660">Observez les valeurs `OperationId` qui varient au sein d’une requête et entre les requêtes :</span><span class="sxs-lookup"><span data-stu-id="d6555-660">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="d6555-661">Les objets *Transient* sont toujours différents.</span><span class="sxs-lookup"><span data-stu-id="d6555-661">*Transient* objects are always different.</span></span> <span data-ttu-id="d6555-662">Les valeurs `OperationId` transitoires pour la première et la deuxième requêtes de client sont différentes pour les deux opérations `OperationService` et entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="d6555-662">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="d6555-663">Une nouvelle instance est fournie à chaque requête de service et requête de client.</span><span class="sxs-lookup"><span data-stu-id="d6555-663">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="d6555-664">Les objets *Scoped* sont les mêmes au sein d’une requête de client, mais ils diffèrent entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="d6555-664">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="d6555-665">Les objets *Singleton* sont les mêmes pour chaque objet et chaque demande, qu’une `Operation` instance soit fournie ou non dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d6555-665">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="d6555-666">Appeler des services à partir de Main</span><span class="sxs-lookup"><span data-stu-id="d6555-666">Call services from main</span></span>

<span data-ttu-id="d6555-667">Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pour résoudre un service délimité dans l’étendue de l’application.</span><span class="sxs-lookup"><span data-stu-id="d6555-667">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="d6555-668">Cette approche est pratique pour accéder à un service Scoped au démarrage pour exécuter des tâches d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="d6555-668">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="d6555-669">L’exemple suivant montre comment obtenir un contexte pour `MyScopedService` dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="d6555-669">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="d6555-670">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="d6555-670">Scope validation</span></span>

<span data-ttu-id="d6555-671">Quand l’application s’exécute dans l’environnement de développement, le fournisseur de services par défaut effectue des contrôles pour vérifier que :</span><span class="sxs-lookup"><span data-stu-id="d6555-671">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d6555-672">Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="d6555-672">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="d6555-673">Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.</span><span class="sxs-lookup"><span data-stu-id="d6555-673">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="d6555-674">Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> est appelé.</span><span class="sxs-lookup"><span data-stu-id="d6555-674">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="d6555-675">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application/du serveur quand le fournisseur démarre avec l’application et qu’il est supprimé quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d6555-675">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d6555-676">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="d6555-676">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d6555-677">Si un service Scoped est créé dans le conteneur racine, la durée de vie du service est promue en singleton, car elle est supprimée par le conteneur racine seulement quand l’application/le serveur est arrêté.</span><span class="sxs-lookup"><span data-stu-id="d6555-677">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="d6555-678">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="d6555-678">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d6555-679">Pour plus d'informations, consultez <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="d6555-679">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="d6555-680">Services de requête</span><span class="sxs-lookup"><span data-stu-id="d6555-680">Request Services</span></span>

<span data-ttu-id="d6555-681">Les services disponibles au sein d’une requête ASP.NET Core à partir de `HttpContext` sont exposés par le biais de la collection [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="d6555-681">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="d6555-682">Les services de requête représentent les services configurés et demandés dans le cadre de l’application.</span><span class="sxs-lookup"><span data-stu-id="d6555-682">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="d6555-683">Lorsque les objets spécifient des dépendances, ceux-ci sont satisfaits par les types trouvés dans `RequestServices`, pas dans `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="d6555-683">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="d6555-684">En règle générale, l’application ne doit pas utiliser directement ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="d6555-684">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="d6555-685">Demandez plutôt les types nécessaires à la classe via des constructeurs de classe et autorisez le framework à injecter les dépendances.</span><span class="sxs-lookup"><span data-stu-id="d6555-685">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="d6555-686">Cela génère des classes qui sont plus faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="d6555-686">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="d6555-687">Préférez demander des dépendances en tant que paramètres de constructeur plutôt qu’accéder à la collection `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="d6555-687">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="d6555-688">Conception de services pour l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="d6555-688">Design services for dependency injection</span></span>

<span data-ttu-id="d6555-689">Les bonnes pratiques permettent de :</span><span class="sxs-lookup"><span data-stu-id="d6555-689">Best practices are to:</span></span>

* <span data-ttu-id="d6555-690">Concevoir des services afin d’utiliser l’injection de dépendances pour obtenir leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="d6555-690">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="d6555-691">Évitez les classes et les membres avec état, static.</span><span class="sxs-lookup"><span data-stu-id="d6555-691">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="d6555-692">Concevez des applications pour utiliser des services Singleton à la place, ce qui évite de créer un état global.</span><span class="sxs-lookup"><span data-stu-id="d6555-692">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="d6555-693">Éviter une instanciation directe de classes dépendantes au sein de services.</span><span class="sxs-lookup"><span data-stu-id="d6555-693">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="d6555-694">L’instanciation directe associe le code à une implémentation particulière.</span><span class="sxs-lookup"><span data-stu-id="d6555-694">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="d6555-695">Limiter la taille des classes d’application, faire en sorte qu’elles soient bien factorisées et facilement testées.</span><span class="sxs-lookup"><span data-stu-id="d6555-695">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="d6555-696">Si une classe semble avoir trop de dépendances injectées, cela signifie généralement que la classe a trop de responsabilités et viole le [principe de responsabilité unique](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="d6555-696">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="d6555-697">Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans une nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="d6555-697">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="d6555-698">Gardez à l’esprit que :::no-loc(Razor)::: les classes de modèle de page de pages et les classes de contrôleur MVC doivent se concentrer sur les préoccupations de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d6555-698">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="d6555-699">Les règles d’entreprise et les détails d’implémentation de l’accès aux données doivent être conservés dans les classes appropriées à ces [préoccupations distinctes](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="d6555-699">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="d6555-700">Suppression des services</span><span class="sxs-lookup"><span data-stu-id="d6555-700">Disposal of services</span></span>

<span data-ttu-id="d6555-701">Le conteneur appelle <xref:System.IDisposable.Dispose*> pour les types <xref:System.IDisposable> qu’il crée.</span><span class="sxs-lookup"><span data-stu-id="d6555-701">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="d6555-702">Si une instance est ajoutée au conteneur par le code utilisateur, elle n’est pas supprimée automatiquement.</span><span class="sxs-lookup"><span data-stu-id="d6555-702">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="d6555-703">Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="d6555-703">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="d6555-704">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="d6555-704">In the following example:</span></span>

* <span data-ttu-id="d6555-705">Les instances de service ne sont pas créées par le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="d6555-705">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="d6555-706">Les durées de vie de service prévues ne sont pas connues de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="d6555-706">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="d6555-707">L’infrastructure ne supprime pas automatiquement les services.</span><span class="sxs-lookup"><span data-stu-id="d6555-707">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="d6555-708">Si les services ne sont pas explicitement supprimés dans le code du développeur, ils persistent jusqu’à ce que l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d6555-708">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="d6555-709">Recommandations IDisposable pour les instances temporaires et partagées</span><span class="sxs-lookup"><span data-stu-id="d6555-709">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="d6555-710">Transitoire, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="d6555-710">Transient, limited lifetime</span></span>

<span data-ttu-id="d6555-711">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="d6555-711">**Scenario**</span></span>

<span data-ttu-id="d6555-712">L’application requiert une <xref:System.IDisposable> instance avec une durée de vie transitoire pour l’un des scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="d6555-712">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="d6555-713">L’instance est résolue dans l’étendue racine.</span><span class="sxs-lookup"><span data-stu-id="d6555-713">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="d6555-714">L’instance doit être supprimée avant la fin de l’étendue.</span><span class="sxs-lookup"><span data-stu-id="d6555-714">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="d6555-715">**Solution**</span><span class="sxs-lookup"><span data-stu-id="d6555-715">**Solution**</span></span>

<span data-ttu-id="d6555-716">Utilisez le modèle de fabrique pour créer une instance en dehors de l’étendue parente.</span><span class="sxs-lookup"><span data-stu-id="d6555-716">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="d6555-717">Dans ce cas, l’application a généralement une `Create` méthode qui appelle directement le constructeur du type final.</span><span class="sxs-lookup"><span data-stu-id="d6555-717">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="d6555-718">Si le type final a d’autres dépendances, la fabrique peut :</span><span class="sxs-lookup"><span data-stu-id="d6555-718">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="d6555-719">Recevoir un <xref:System.IServiceProvider> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="d6555-719">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="d6555-720">Utilisez <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> pour instancier l’instance en dehors du conteneur, tout en utilisant le conteneur pour ses dépendances.</span><span class="sxs-lookup"><span data-stu-id="d6555-720">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="d6555-721">Instance partagée, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="d6555-721">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="d6555-722">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="d6555-722">**Scenario**</span></span>

<span data-ttu-id="d6555-723">L’application nécessite une <xref:System.IDisposable> instance partagée sur plusieurs services, mais le <xref:System.IDisposable> doit avoir une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="d6555-723">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="d6555-724">**Solution**</span><span class="sxs-lookup"><span data-stu-id="d6555-724">**Solution**</span></span>

<span data-ttu-id="d6555-725">Inscrivez l’instance avec une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="d6555-725">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="d6555-726">Utilisez <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> pour démarrer et créer un nouveau <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="d6555-726">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="d6555-727">Utilisez les étendues <xref:System.IServiceProvider> pour accéder aux services requis.</span><span class="sxs-lookup"><span data-stu-id="d6555-727">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="d6555-728">Supprimez l’étendue lorsque la durée de vie doit se terminer.</span><span class="sxs-lookup"><span data-stu-id="d6555-728">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="d6555-729">Instructions générales</span><span class="sxs-lookup"><span data-stu-id="d6555-729">General Guidelines</span></span>

* <span data-ttu-id="d6555-730">N’inscrivez pas <xref:System.IDisposable> les instances avec une étendue transitoire.</span><span class="sxs-lookup"><span data-stu-id="d6555-730">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="d6555-731">Utilisez à la place le modèle de fabrique.</span><span class="sxs-lookup"><span data-stu-id="d6555-731">Use the factory pattern instead.</span></span>
* <span data-ttu-id="d6555-732">Ne résolvez pas les instances temporaires ou délimitées <xref:System.IDisposable> dans l’étendue racine.</span><span class="sxs-lookup"><span data-stu-id="d6555-732">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="d6555-733">La seule exception générale est lorsque l’application crée/recrée et supprime le <xref:System.IServiceProvider> , qui n’est pas un modèle idéal.</span><span class="sxs-lookup"><span data-stu-id="d6555-733">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="d6555-734">La réception d’une <xref:System.IDisposable> dépendance via l’injection de dépendances ne nécessite pas que le récepteur s’implémente <xref:System.IDisposable> lui-même.</span><span class="sxs-lookup"><span data-stu-id="d6555-734">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="d6555-735">Le récepteur de la <xref:System.IDisposable> dépendance ne doit pas appeler <xref:System.IDisposable.Dispose%2A> sur cette dépendance.</span><span class="sxs-lookup"><span data-stu-id="d6555-735">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="d6555-736">Les portées doivent être utilisées pour contrôler les durées de vie des services.</span><span class="sxs-lookup"><span data-stu-id="d6555-736">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="d6555-737">Les étendues ne sont pas hiérarchiques, et il n’existe pas de connexion spéciale entre les étendues.</span><span class="sxs-lookup"><span data-stu-id="d6555-737">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="d6555-738">Remplacement de conteneur de services par défaut</span><span class="sxs-lookup"><span data-stu-id="d6555-738">Default service container replacement</span></span>

<span data-ttu-id="d6555-739">Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public.</span><span class="sxs-lookup"><span data-stu-id="d6555-739">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="d6555-740">Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique que le conteneur intégré ne prend pas en charge, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d6555-740">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="d6555-741">Injection de propriétés</span><span class="sxs-lookup"><span data-stu-id="d6555-741">Property injection</span></span>
* <span data-ttu-id="d6555-742">Injection en fonction du nom</span><span class="sxs-lookup"><span data-stu-id="d6555-742">Injection based on name</span></span>
* <span data-ttu-id="d6555-743">Conteneurs enfants</span><span class="sxs-lookup"><span data-stu-id="d6555-743">Child containers</span></span>
* <span data-ttu-id="d6555-744">Gestion personnalisée de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="d6555-744">Custom lifetime management</span></span>
* <span data-ttu-id="d6555-745">`Func<T>` prend en charge l’initialisation tardive</span><span class="sxs-lookup"><span data-stu-id="d6555-745">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="d6555-746">Inscription basée sur une convention</span><span class="sxs-lookup"><span data-stu-id="d6555-746">Convention-based registration</span></span>

<span data-ttu-id="d6555-747">Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="d6555-747">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="d6555-748">Autofac</span><span class="sxs-lookup"><span data-stu-id="d6555-748">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="d6555-749">DryIoc</span><span class="sxs-lookup"><span data-stu-id="d6555-749">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="d6555-750">Grace</span><span class="sxs-lookup"><span data-stu-id="d6555-750">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="d6555-751">LightInject</span><span class="sxs-lookup"><span data-stu-id="d6555-751">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="d6555-752">Lamar</span><span class="sxs-lookup"><span data-stu-id="d6555-752">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="d6555-753">Stashbox</span><span class="sxs-lookup"><span data-stu-id="d6555-753">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="d6555-754">Unity</span><span class="sxs-lookup"><span data-stu-id="d6555-754">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="d6555-755">Sécurité des threads</span><span class="sxs-lookup"><span data-stu-id="d6555-755">Thread safety</span></span>

<span data-ttu-id="d6555-756">Créez des services singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d6555-756">Create thread-safe singleton services.</span></span> <span data-ttu-id="d6555-757">Si un service singleton a une dépendance vis-à-vis d’un service Transient, ce dernier peut également nécessiter la cohérence de thread, en fonction de la manière dont il est utilisé par le singleton.</span><span class="sxs-lookup"><span data-stu-id="d6555-757">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="d6555-758">La méthode de fabrique d’un service unique, telle que le deuxième argument de [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), n’a pas besoin d’être thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d6555-758">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="d6555-759">Comme un constructeur de type (`static`), elle est forcément appelée une fois par un seul thread.</span><span class="sxs-lookup"><span data-stu-id="d6555-759">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d6555-760">Recommandations</span><span class="sxs-lookup"><span data-stu-id="d6555-760">Recommendations</span></span>

* <span data-ttu-id="d6555-761">La résolution de service basée sur `async/await` et `Task` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="d6555-761">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="d6555-762">C# ne prend pas en charge les constructeurs asynchrones ; par conséquent, le modèle recommandé consiste à utiliser des méthodes asynchrones après avoir résolu le service de façon synchrone.</span><span class="sxs-lookup"><span data-stu-id="d6555-762">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="d6555-763">Évitez de stocker des données et des configurations directement dans le conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="d6555-763">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="d6555-764">Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="d6555-764">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="d6555-765">La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="d6555-765">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d6555-766">De même, évitez les objets « conteneurs de données » qui n’existent que pour autoriser l’accès à un autre objet.</span><span class="sxs-lookup"><span data-stu-id="d6555-766">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="d6555-767">Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="d6555-767">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="d6555-768">Évitez l’accès statique aux services.</span><span class="sxs-lookup"><span data-stu-id="d6555-768">Avoid static access to services.</span></span> <span data-ttu-id="d6555-769">Par exemple, évitez de taper statiquement [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pour une utilisation ailleurs.</span><span class="sxs-lookup"><span data-stu-id="d6555-769">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="d6555-770">Évitez d’utiliser le *modèle de localisation de service* , qui combine les stratégies [d’inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="d6555-770">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="d6555-771">N’appelez pas <xref:System.IServiceProvider.GetService*> pour obtenir une instance de service lorsque vous pouvez utiliser l’injection de code à la place :</span><span class="sxs-lookup"><span data-stu-id="d6555-771">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="d6555-772">**Incorrect :**</span><span class="sxs-lookup"><span data-stu-id="d6555-772">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="d6555-773">**Correct** :</span><span class="sxs-lookup"><span data-stu-id="d6555-773">**Correct** :</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

* <span data-ttu-id="d6555-774">Évitez d’injecter une fabrique qui résout les dépendances au moment de l’exécution à l’aide de <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="d6555-774">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="d6555-775">Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="d6555-775">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="d6555-776">Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation.</span><span class="sxs-lookup"><span data-stu-id="d6555-776">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="d6555-777">Les exceptions sont rares, principalement des cas spéciaux dans l’infrastructure elle-même.</span><span class="sxs-lookup"><span data-stu-id="d6555-777">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="d6555-778">L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux.</span><span class="sxs-lookup"><span data-stu-id="d6555-778">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="d6555-779">Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.</span><span class="sxs-lookup"><span data-stu-id="d6555-779">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d6555-780">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d6555-780">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="d6555-781">Quatre méthodes pour supprimer des IDisposable dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6555-781">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="d6555-782">Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)</span><span class="sxs-lookup"><span data-stu-id="d6555-782">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="d6555-783">Principe des dépendances explicites</span><span class="sxs-lookup"><span data-stu-id="d6555-783">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="d6555-784">Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="d6555-784">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="d6555-785">Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6555-785">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
