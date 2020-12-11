---
title: ASP.NET Core Blazor Server avec Entity Framework Core (EFCore)
author: JeremyLikness
description: Aide sur l’utilisation de EF Core dans les Blazor Server applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 10fc0afe84065f2c226d1e9c2f4314142369613a
ms.sourcegitcommit: c321518bfe367280ef262aecaada287f17fe1bc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97011882"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="9f3e0-103">ASP.NET Core Blazor Server avec Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="9f3e0-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="9f3e0-104">Par : [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="9f3e0-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9f3e0-105">Blazor Server est une infrastructure d’application avec état.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="9f3e0-106">L’application maintient une connexion permanente au serveur et l’état de l’utilisateur est conservé dans la mémoire du serveur dans un *circuit*.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="9f3e0-107">Voici un exemple d’état utilisateur : données conservées dans des instances de service d' [injection de dépendance (di)](xref:fundamentals/dependency-injection) qui sont étendues au circuit.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="9f3e0-108">Le modèle d’application unique Blazor Server fourni par requiert une approche spéciale pour utiliser Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="9f3e0-109">Cet article traite de EF Core dans les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="9f3e0-110">Blazor WebAssembly les applications s’exécutent dans un bac à sable (sandbox) webassembly qui empêche la plupart des connexions directes.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="9f3e0-111">L’exécution de EF Core dans Blazor WebAssembly dépasse le cadre de cet article.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="9f3e0-112">Exemple d'application</span><span class="sxs-lookup"><span data-stu-id="9f3e0-112">Sample app</span></span></h2>

<span data-ttu-id="9f3e0-113">L’exemple d’application a été créé en tant que référence pour les Blazor Server applications qui utilisent EF Core.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="9f3e0-114">L’exemple d’application comprend une grille avec des opérations de tri et de filtrage, de suppression, d’ajout et de mise à jour.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="9f3e0-115">L’exemple illustre l’utilisation de EF Core pour gérer l’accès concurrentiel optimiste.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="9f3e0-116">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f3e0-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9f3e0-117">L’exemple utilise une base de données [SQLite](https://www.sqlite.org/index.html) locale pour qu’elle puisse être utilisée sur n’importe quelle plateforme.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="9f3e0-118">L’exemple configure également la journalisation de la base de données pour afficher les requêtes SQL qui sont générées.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="9f3e0-119">Cette configuration est configurée dans `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="9f3e0-120">Les composants de grille, d’ajout et de vue utilisent le modèle « contexte par opération », où un contexte est créé pour chaque opération.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="9f3e0-121">Le composant Edit utilise le modèle « contexte par composant », où un contexte est créé pour chaque composant.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="9f3e0-122">Certains des exemples de code de cette rubrique requièrent des espaces de noms et des services qui ne sont pas affichés.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="9f3e0-123">Pour inspecter le code entièrement opérationnel, y compris [`@using`](xref:mvc/views/razor#using) les [`@inject`](xref:mvc/views/razor#inject) directives et requises pour obtenir des Razor exemples, consultez l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="9f3e0-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="9f3e0-124">Accès à la base de données</span><span class="sxs-lookup"><span data-stu-id="9f3e0-124">Database access</span></span></h2>

<span data-ttu-id="9f3e0-125">EF Core s’appuie sur un <xref:Microsoft.EntityFrameworkCore.DbContext> comme moyen de configurer l’accès à la [base de données](/ef/core/miscellaneous/configuring-dbcontext) et d’agir en tant qu' [*unité de travail*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="9f3e0-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="9f3e0-126">EF Core fournit l' <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension pour les applications ASP.net core qui inscrit le contexte en tant que service *étendu* par défaut.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="9f3e0-127">Dans Blazor Server les applications, les inscriptions de service étendues peuvent être problématiques, car l’instance est partagée entre les composants dans le circuit de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="9f3e0-128"><xref:Microsoft.EntityFrameworkCore.DbContext> n’est pas thread-safe et n’est pas conçu pour une utilisation simultanée.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="9f3e0-129">Les durées de vie existantes sont inappropriées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="9f3e0-130">**Singleton** partage l’État sur tous les utilisateurs de l’application et provoque une utilisation simultanée inappropriée.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="9f3e0-131">**Étendue** (valeur par défaut) pose un problème similaire entre les composants pour le même utilisateur.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="9f3e0-132">Les résultats **transitoires** génèrent une nouvelle instance par demande ; mais comme les composants peuvent être de longue durée de vie, cela se traduit par un contexte plus long que prévu.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="9f3e0-133">Les recommandations suivantes sont conçues pour fournir une approche cohérente de l’utilisation de EF Core dans les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="9f3e0-134">Par défaut, envisagez d’utiliser un contexte par opération.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="9f3e0-135">Le contexte est conçu pour une instanciation rapide et à faible surcharge :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="9f3e0-136">Utilisez un indicateur pour empêcher plusieurs opérations simultanées :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-136">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="9f3e0-137">Placez les opérations après la `Loading = true;` ligne dans le `try` bloc.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="9f3e0-138">Pour les opérations à long terme qui tirent parti du [suivi des modifications](/ef/core/querying/tracking) ou du [contrôle d’accès concurrentiel](/ef/core/saving/concurrency)de EF Core, limitez [le contexte à la durée de vie du composant](#scope-to-the-component-lifetime-5x).</span><span class="sxs-lookup"><span data-stu-id="9f3e0-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="9f3e0-139">Nouvelles instances de DbContext</span><span class="sxs-lookup"><span data-stu-id="9f3e0-139">New DbContext instances</span></span></h3>

<span data-ttu-id="9f3e0-140">Le moyen le plus rapide de créer une nouvelle <xref:Microsoft.EntityFrameworkCore.DbContext> instance consiste à utiliser `new` pour créer une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="9f3e0-141">Toutefois, il existe plusieurs scénarios qui peuvent nécessiter la résolution de dépendances supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="9f3e0-142">Par exemple, vous souhaiterez peut-être utiliser [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) pour configurer le contexte.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="9f3e0-143">La solution recommandée pour créer un nouveau <xref:Microsoft.EntityFrameworkCore.DbContext> avec des dépendances consiste à utiliser une fabrique.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="9f3e0-144">EF Core 5,0 ou version ultérieure fournit une fabrique intégrée pour la création de nouveaux contextes.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="9f3e0-145">L’exemple suivant configure [SQLite](https://www.sqlite.org/index.html) et active la journalisation des données.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="9f3e0-146">Le code utilise une [méthode d’extension ( `AddDbContextFactory` )](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) pour configurer la fabrique de base de données pour di et fournir les options par défaut :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-146">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="9f3e0-147">La fabrique est injectée dans des composants et utilisée pour créer de nouvelles instances.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="9f3e0-148">Par exemple, dans `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="9f3e0-149">`Wrapper` est une [référence de composant](xref:blazor/components/index#capture-references-to-components) au `GridWrapper` composant.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="9f3e0-150">Consultez le `Index` composant ( `Pages/Index.razor` ) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="9f3e0-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="9f3e0-151">De nouvelles <xref:Microsoft.EntityFrameworkCore.DbContext> instances peuvent être créées avec une fabrique qui vous permet de configurer la chaîne de connexion par `DbContext` , par exemple lorsque vous utilisez le [ Identity modèle de ASP.net Core](xref:security/authentication/customize_identity_model):</span><span class="sxs-lookup"><span data-stu-id="9f3e0-151">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model](xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="9f3e0-152">Portée à la durée de vie du composant</span><span class="sxs-lookup"><span data-stu-id="9f3e0-152">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="9f3e0-153">Vous pouvez créer un <xref:Microsoft.EntityFrameworkCore.DbContext> qui existe pour la durée de vie d’un composant.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-153">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="9f3e0-154">Cela vous permet de l’utiliser comme une [unité de travail](https://martinfowler.com/eaaCatalog/unitOfWork.html) et de tirer parti des fonctionnalités intégrées, telles que le suivi des modifications et la résolution de l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-154">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="9f3e0-155">Vous pouvez utiliser la fabrique pour créer un contexte et effectuer le suivi de la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-155">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="9f3e0-156">Tout d’abord, implémentez <xref:System.IDisposable> et injectez la fabrique comme indiqué dans `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-156">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="9f3e0-157">L’exemple d’application s’assure que le contexte est supprimé lorsque le composant est supprimé :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-157">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="9f3e0-158">Enfin, [`OnInitializedAsync`](xref:blazor/components/lifecycle) est substitué pour créer un nouveau contexte.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-158">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="9f3e0-159">Dans l’exemple d’application, [`OnInitializedAsync`](xref:blazor/components/lifecycle) charge le contact dans la même méthode :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-159">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="9f3e0-160">Activer la journalisation des données sensibles</span><span class="sxs-lookup"><span data-stu-id="9f3e0-160">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="9f3e0-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> comprend les données d’application dans les messages d’exception et la journalisation du Framework.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="9f3e0-162">Les données journalisées peuvent inclure les valeurs affectées aux propriétés des instances d’entité et les valeurs de paramètre pour les commandes envoyées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-162">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="9f3e0-163">La journalisation des données avec <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> présente un **risque de sécurité**, car elle peut exposer des mots de passe et d’autres informations d’identification personnelle (PII) lorsqu’elle enregistre des instructions SQL exécutées sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-163">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="9f3e0-164">Nous vous recommandons d’activer uniquement <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> pour le développement et le test :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-164">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="9f3e0-165">Blazor Server est une infrastructure d’application avec état.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-165">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="9f3e0-166">L’application maintient une connexion permanente au serveur et l’état de l’utilisateur est conservé dans la mémoire du serveur dans un *circuit*.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-166">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="9f3e0-167">Voici un exemple d’état utilisateur : données conservées dans des instances de service d' [injection de dépendance (di)](xref:fundamentals/dependency-injection) qui sont étendues au circuit.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-167">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="9f3e0-168">Le modèle d’application unique Blazor Server fourni par requiert une approche spéciale pour utiliser Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-168">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="9f3e0-169">Cet article traite de EF Core dans les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-169">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="9f3e0-170">Blazor WebAssembly les applications s’exécutent dans un bac à sable (sandbox) webassembly qui empêche la plupart des connexions directes.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-170">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="9f3e0-171">L’exécution de EF Core dans Blazor WebAssembly dépasse le cadre de cet article.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-171">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="9f3e0-172">Exemple d'application</span><span class="sxs-lookup"><span data-stu-id="9f3e0-172">Sample app</span></span></h2>

<span data-ttu-id="9f3e0-173">L’exemple d’application a été créé en tant que référence pour les Blazor Server applications qui utilisent EF Core.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-173">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="9f3e0-174">L’exemple d’application comprend une grille avec des opérations de tri et de filtrage, de suppression, d’ajout et de mise à jour.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-174">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="9f3e0-175">L’exemple illustre l’utilisation de EF Core pour gérer l’accès concurrentiel optimiste.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-175">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="9f3e0-176">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f3e0-176">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9f3e0-177">L’exemple utilise une base de données [SQLite](https://www.sqlite.org/index.html) locale pour qu’elle puisse être utilisée sur n’importe quelle plateforme.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-177">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="9f3e0-178">L’exemple configure également la journalisation de la base de données pour afficher les requêtes SQL qui sont générées.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-178">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="9f3e0-179">Cette configuration est configurée dans `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-179">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="9f3e0-180">Les composants de grille, d’ajout et de vue utilisent le modèle « contexte par opération », où un contexte est créé pour chaque opération.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-180">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="9f3e0-181">Le composant Edit utilise le modèle « contexte par composant », où un contexte est créé pour chaque composant.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-181">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="9f3e0-182">Certains des exemples de code de cette rubrique requièrent des espaces de noms et des services qui ne sont pas affichés.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-182">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="9f3e0-183">Pour inspecter le code entièrement opérationnel, y compris [`@using`](xref:mvc/views/razor#using) les [`@inject`](xref:mvc/views/razor#inject) directives et requises pour obtenir des Razor exemples, consultez l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="9f3e0-183">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="9f3e0-184">Accès à la base de données</span><span class="sxs-lookup"><span data-stu-id="9f3e0-184">Database access</span></span></h2>

<span data-ttu-id="9f3e0-185">EF Core s’appuie sur un <xref:Microsoft.EntityFrameworkCore.DbContext> comme moyen de configurer l’accès à la [base de données](/ef/core/miscellaneous/configuring-dbcontext) et d’agir en tant qu' [*unité de travail*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="9f3e0-185">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="9f3e0-186">EF Core fournit l' <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension pour les applications ASP.net core qui inscrit le contexte en tant que service *étendu* par défaut.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-186">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="9f3e0-187">Dans Blazor Server les applications, cela peut être problématique, car l’instance est partagée par plusieurs composants dans le circuit de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-187">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="9f3e0-188"><xref:Microsoft.EntityFrameworkCore.DbContext> n’est pas thread-safe et n’est pas conçu pour une utilisation simultanée.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-188"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="9f3e0-189">Les durées de vie existantes sont inappropriées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-189">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="9f3e0-190">**Singleton** partage l’État sur tous les utilisateurs de l’application et provoque une utilisation simultanée inappropriée.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-190">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="9f3e0-191">**Étendue** (valeur par défaut) pose un problème similaire entre les composants pour le même utilisateur.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-191">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="9f3e0-192">Les résultats **transitoires** génèrent une nouvelle instance par demande ; mais comme les composants peuvent être de longue durée de vie, cela se traduit par un contexte plus long que prévu.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-192">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="9f3e0-193">Les recommandations suivantes sont conçues pour fournir une approche cohérente de l’utilisation de EF Core dans les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-193">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="9f3e0-194">Par défaut, envisagez d’utiliser un contexte par opération.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-194">By default, consider using one context per operation.</span></span> <span data-ttu-id="9f3e0-195">Le contexte est conçu pour une instanciation rapide et à faible surcharge :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-195">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="9f3e0-196">Utilisez un indicateur pour empêcher plusieurs opérations simultanées :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-196">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="9f3e0-197">Placez les opérations après la `Loading = true;` ligne dans le `try` bloc.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-197">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="9f3e0-198">Pour les opérations à long terme qui tirent parti du [suivi des modifications](/ef/core/querying/tracking) ou du [contrôle d’accès concurrentiel](/ef/core/saving/concurrency)de EF Core, limitez [le contexte à la durée de vie du composant](#scope-to-the-component-lifetime-3x).</span><span class="sxs-lookup"><span data-stu-id="9f3e0-198">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="9f3e0-199">Nouvelles instances de DbContext</span><span class="sxs-lookup"><span data-stu-id="9f3e0-199">New DbContext instances</span></span></h3>

<span data-ttu-id="9f3e0-200">Le moyen le plus rapide de créer une nouvelle <xref:Microsoft.EntityFrameworkCore.DbContext> instance consiste à utiliser `new` pour créer une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-200">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="9f3e0-201">Toutefois, il existe plusieurs scénarios qui peuvent nécessiter la résolution de dépendances supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-201">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="9f3e0-202">Par exemple, vous souhaiterez peut-être utiliser [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) pour configurer le contexte.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-202">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="9f3e0-203">La solution recommandée pour créer un nouveau <xref:Microsoft.EntityFrameworkCore.DbContext> avec des dépendances consiste à utiliser une fabrique.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-203">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="9f3e0-204">L’exemple d’application implémente sa propre fabrique dans `Data/DbContextFactory.cs` .</span><span class="sxs-lookup"><span data-stu-id="9f3e0-204">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="9f3e0-205">Dans la fabrique précédente :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-205">In the preceding factory:</span></span>

* <span data-ttu-id="9f3e0-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfait toutes les dépendances via le fournisseur de services.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="9f3e0-207">`IDbContextFactory` est disponible dans EF Core ASP.NET Core 5,0 ou version ultérieure. l’interface est donc [implémentée dans l’exemple d’application pour ASP.net Core 3. x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="9f3e0-207">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="9f3e0-208">L’exemple suivant configure [SQLite](https://www.sqlite.org/index.html) et active la journalisation des données.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-208">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="9f3e0-209">Le code utilise une méthode d’extension pour configurer la fabrique de base de données pour DI et fournir les options par défaut :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-209">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="9f3e0-210">La fabrique est injectée dans des composants et utilisée pour créer de nouvelles instances.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-210">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="9f3e0-211">Par exemple, dans `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-211">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="9f3e0-212">`Wrapper` est une [référence de composant](xref:blazor/components/index#capture-references-to-components) au `GridWrapper` composant.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-212">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="9f3e0-213">Consultez le `Index` composant ( `Pages/Index.razor` ) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="9f3e0-213">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="9f3e0-214">De nouvelles <xref:Microsoft.EntityFrameworkCore.DbContext> instances peuvent être créées avec une fabrique qui vous permet de configurer la chaîne de connexion par `DbContext` , par exemple lorsque vous utilisez [ Identity modèle de ASP.net core]) (XREF : Security/authentication/customize_identity_model) :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-214">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="9f3e0-215">Portée à la durée de vie du composant</span><span class="sxs-lookup"><span data-stu-id="9f3e0-215">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="9f3e0-216">Vous pouvez créer un <xref:Microsoft.EntityFrameworkCore.DbContext> qui existe pour la durée de vie d’un composant.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-216">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="9f3e0-217">Cela vous permet de l’utiliser comme une [unité de travail](https://martinfowler.com/eaaCatalog/unitOfWork.html) et de tirer parti des fonctionnalités intégrées, telles que le suivi des modifications et la résolution de l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-217">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="9f3e0-218">Vous pouvez utiliser la fabrique pour créer un contexte et effectuer le suivi de la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-218">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="9f3e0-219">Tout d’abord, implémentez <xref:System.IDisposable> et injectez la fabrique comme indiqué dans `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-219">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="9f3e0-220">L’exemple d’application s’assure que le contexte est supprimé lorsque le composant est supprimé :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-220">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="9f3e0-221">Enfin, [`OnInitializedAsync`](xref:blazor/components/lifecycle) est substitué pour créer un nouveau contexte.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-221">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="9f3e0-222">Dans l’exemple d’application, [`OnInitializedAsync`](xref:blazor/components/lifecycle) charge le contact dans la même méthode :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-222">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="9f3e0-223">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-223">In the preceding example:</span></span>

* <span data-ttu-id="9f3e0-224">Lorsque `Busy` a la valeur `true` , les opérations asynchrones peuvent commencer.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-224">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="9f3e0-225">Lorsque `Busy` a la valeur `false` , les opérations asynchrones doivent être terminées.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-225">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="9f3e0-226">Placez une logique de gestion des erreurs supplémentaire dans un `catch` bloc.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-226">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="9f3e0-227">Activer la journalisation des données sensibles</span><span class="sxs-lookup"><span data-stu-id="9f3e0-227">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="9f3e0-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> comprend les données d’application dans les messages d’exception et la journalisation du Framework.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="9f3e0-229">Les données journalisées peuvent inclure les valeurs affectées aux propriétés des instances d’entité et les valeurs de paramètre pour les commandes envoyées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-229">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="9f3e0-230">La journalisation des données avec <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> présente un **risque de sécurité**, car elle peut exposer des mots de passe et d’autres informations d’identification personnelle (PII) lorsqu’elle enregistre des instructions SQL exécutées sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="9f3e0-230">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="9f3e0-231">Nous vous recommandons d’activer uniquement <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> pour le développement et le test :</span><span class="sxs-lookup"><span data-stu-id="9f3e0-231">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9f3e0-232">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9f3e0-232">Additional resources</span></span>

* [<span data-ttu-id="9f3e0-233">Documentation EF Core</span><span class="sxs-lookup"><span data-stu-id="9f3e0-233">EF Core documentation</span></span>](/ef/)
