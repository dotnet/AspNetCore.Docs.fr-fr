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
ms.openlocfilehash: 53d276db996304852d69566584e43d47aa73f921
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586551"
---
# <a name="aspnet-core-blazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="6a4d2-103">ASP.NET Core Blazor Server avec Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="6a4d2-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6a4d2-104">Blazor Server est une infrastructure d’application avec état.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-104">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="6a4d2-105">L’application maintient une connexion permanente au serveur et l’état de l’utilisateur est conservé dans la mémoire du serveur dans un *circuit*.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-105">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="6a4d2-106">Voici un exemple d’état utilisateur : données conservées dans des instances de service d' [injection de dépendance (di)](xref:fundamentals/dependency-injection) qui sont étendues au circuit.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-106">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="6a4d2-107">Le modèle d’application unique Blazor Server fourni par requiert une approche spéciale pour utiliser Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-107">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="6a4d2-108">Cet article traite de EF Core dans les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-108">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="6a4d2-109">Blazor WebAssembly les applications s’exécutent dans un bac à sable (sandbox) webassembly qui empêche la plupart des connexions directes.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-109">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="6a4d2-110">L’exécution de EF Core dans Blazor WebAssembly dépasse le cadre de cet article.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-110">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="6a4d2-111">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="6a4d2-111">Sample app</span></span></h2>

<span data-ttu-id="6a4d2-112">L’exemple d’application a été créé en tant que référence pour les Blazor Server applications qui utilisent EF Core.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-112">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="6a4d2-113">L’exemple d’application comprend une grille avec des opérations de tri et de filtrage, de suppression, d’ajout et de mise à jour.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-113">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="6a4d2-114">L’exemple illustre l’utilisation de EF Core pour gérer l’accès concurrentiel optimiste.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-114">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="6a4d2-115">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6a4d2-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6a4d2-116">L’exemple utilise une base de données [SQLite](https://www.sqlite.org/index.html) locale pour qu’elle puisse être utilisée sur n’importe quelle plateforme.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-116">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="6a4d2-117">L’exemple configure également la journalisation de la base de données pour afficher les requêtes SQL qui sont générées.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-117">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="6a4d2-118">Cette configuration est configurée dans `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-118">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="6a4d2-119">Les composants de grille, d’ajout et de vue utilisent le modèle « contexte par opération », où un contexte est créé pour chaque opération.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-119">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="6a4d2-120">Le composant Edit utilise le modèle « contexte par composant », où un contexte est créé pour chaque composant.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-120">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="6a4d2-121">Certains des exemples de code de cette rubrique requièrent des espaces de noms et des services qui ne sont pas affichés.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-121">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="6a4d2-122">Pour inspecter le code entièrement opérationnel, y compris [`@using`](xref:mvc/views/razor#using) les [`@inject`](xref:mvc/views/razor#inject) directives et requises pour obtenir des Razor exemples, consultez l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="6a4d2-122">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="6a4d2-123">Accès à la base de données</span><span class="sxs-lookup"><span data-stu-id="6a4d2-123">Database access</span></span></h2>

<span data-ttu-id="6a4d2-124">EF Core s’appuie sur un <xref:Microsoft.EntityFrameworkCore.DbContext> comme moyen de configurer l’accès à la [base de données](/ef/core/miscellaneous/configuring-dbcontext) et d’agir en tant qu' [*unité de travail*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="6a4d2-124">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="6a4d2-125">EF Core fournit l' <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension pour les applications ASP.net core qui inscrit le contexte en tant que service *étendu* par défaut.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-125">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="6a4d2-126">Dans Blazor Server les applications, les inscriptions de service étendues peuvent être problématiques, car l’instance est partagée entre les composants dans le circuit de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-126">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="6a4d2-127"><xref:Microsoft.EntityFrameworkCore.DbContext> n’est pas thread-safe et n’est pas conçu pour une utilisation simultanée.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-127"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="6a4d2-128">Les durées de vie existantes sont inappropriées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-128">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="6a4d2-129">**Singleton** partage l’État sur tous les utilisateurs de l’application et provoque une utilisation simultanée inappropriée.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-129">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="6a4d2-130">**Étendue** (valeur par défaut) pose un problème similaire entre les composants pour le même utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-130">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="6a4d2-131">Les résultats **transitoires** génèrent une nouvelle instance par demande ; mais comme les composants peuvent être de longue durée de vie, cela se traduit par un contexte plus long que prévu.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-131">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="6a4d2-132">Les recommandations suivantes sont conçues pour fournir une approche cohérente de l’utilisation de EF Core dans les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-132">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="6a4d2-133">Par défaut, envisagez d’utiliser un contexte par opération.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-133">By default, consider using one context per operation.</span></span> <span data-ttu-id="6a4d2-134">Le contexte est conçu pour une instanciation rapide et à faible surcharge :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-134">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="6a4d2-135">Utilisez un indicateur pour empêcher plusieurs opérations simultanées :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-135">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="6a4d2-136">Placez les opérations après la `Loading = true;` ligne dans le `try` bloc.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-136">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="6a4d2-137">Pour les opérations à long terme qui tirent parti du [suivi des modifications](/ef/core/querying/tracking) ou du [contrôle d’accès concurrentiel](/ef/core/saving/concurrency)de EF Core, limitez [le contexte à la durée de vie du composant](#scope-to-the-component-lifetime-5x).</span><span class="sxs-lookup"><span data-stu-id="6a4d2-137">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="6a4d2-138">Nouvelles instances de DbContext</span><span class="sxs-lookup"><span data-stu-id="6a4d2-138">New DbContext instances</span></span></h3>

<span data-ttu-id="6a4d2-139">Le moyen le plus rapide de créer une nouvelle <xref:Microsoft.EntityFrameworkCore.DbContext> instance consiste à utiliser `new` pour créer une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-139">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="6a4d2-140">Toutefois, il existe plusieurs scénarios qui peuvent nécessiter la résolution de dépendances supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-140">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="6a4d2-141">Par exemple, vous souhaiterez peut-être utiliser [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) pour configurer le contexte.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-141">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="6a4d2-142">La solution recommandée pour créer un nouveau <xref:Microsoft.EntityFrameworkCore.DbContext> avec des dépendances consiste à utiliser une fabrique.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-142">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="6a4d2-143">EF Core 5,0 ou version ultérieure fournit une fabrique intégrée pour la création de nouveaux contextes.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-143">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="6a4d2-144">L’exemple suivant configure [SQLite](https://www.sqlite.org/index.html) et active la journalisation des données.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-144">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="6a4d2-145">Le code utilise une [méthode d’extension ( `AddDbContextFactory` )](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) pour configurer la fabrique de base de données pour di et fournir les options par défaut :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-145">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="6a4d2-146">La fabrique est injectée dans des composants et utilisée pour créer de nouvelles instances.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-146">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="6a4d2-147">Par exemple, dans `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-147">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="6a4d2-148">`Wrapper` est une [référence de composant](xref:blazor/components/index#capture-references-to-components) au `GridWrapper` composant.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-148">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="6a4d2-149">Consultez le `Index` composant ( `Pages/Index.razor` ) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="6a4d2-149">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="6a4d2-150">De nouvelles <xref:Microsoft.EntityFrameworkCore.DbContext> instances peuvent être créées avec une fabrique qui vous permet de configurer la chaîne de connexion par `DbContext` , par exemple lorsque vous utilisez le [ Identity modèle de ASP.net Core](xref:security/authentication/customize_identity_model):</span><span class="sxs-lookup"><span data-stu-id="6a4d2-150">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model](xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="6a4d2-151">Portée à la durée de vie du composant</span><span class="sxs-lookup"><span data-stu-id="6a4d2-151">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="6a4d2-152">Vous pouvez créer un <xref:Microsoft.EntityFrameworkCore.DbContext> qui existe pour la durée de vie d’un composant.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="6a4d2-153">Cela vous permet de l’utiliser comme une [unité de travail](https://martinfowler.com/eaaCatalog/unitOfWork.html) et de tirer parti des fonctionnalités intégrées, telles que le suivi des modifications et la résolution de l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="6a4d2-154">Vous pouvez utiliser la fabrique pour créer un contexte et effectuer le suivi de la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="6a4d2-155">Tout d’abord, implémentez <xref:System.IDisposable> et injectez la fabrique comme indiqué dans `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="6a4d2-156">L’exemple d’application s’assure que le contexte est supprimé lorsque le composant est supprimé :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="6a4d2-157">Enfin, [`OnInitializedAsync`](xref:blazor/components/lifecycle) est substitué pour créer un nouveau contexte.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="6a4d2-158">Dans l’exemple d’application, [`OnInitializedAsync`](xref:blazor/components/lifecycle) charge le contact dans la même méthode :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="6a4d2-159">Activer la journalisation des données sensibles</span><span class="sxs-lookup"><span data-stu-id="6a4d2-159">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="6a4d2-160"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> comprend les données d’application dans les messages d’exception et la journalisation du Framework.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-160"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="6a4d2-161">Les données journalisées peuvent inclure les valeurs affectées aux propriétés des instances d’entité et les valeurs de paramètre pour les commandes envoyées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-161">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="6a4d2-162">La journalisation des données avec <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> présente un **risque de sécurité**, car elle peut exposer des mots de passe et d’autres informations d’identification personnelle (PII) lorsqu’elle enregistre des instructions SQL exécutées sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-162">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="6a4d2-163">Nous vous recommandons d’activer uniquement <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> pour le développement et le test :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-163">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

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

<span data-ttu-id="6a4d2-164">Blazor Server est une infrastructure d’application avec état.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-164">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="6a4d2-165">L’application maintient une connexion permanente au serveur et l’état de l’utilisateur est conservé dans la mémoire du serveur dans un *circuit*.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-165">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="6a4d2-166">Voici un exemple d’état utilisateur : données conservées dans des instances de service d' [injection de dépendance (di)](xref:fundamentals/dependency-injection) qui sont étendues au circuit.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-166">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="6a4d2-167">Le modèle d’application unique Blazor Server fourni par requiert une approche spéciale pour utiliser Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-167">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="6a4d2-168">Cet article traite de EF Core dans les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-168">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="6a4d2-169">Blazor WebAssembly les applications s’exécutent dans un bac à sable (sandbox) webassembly qui empêche la plupart des connexions directes.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-169">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="6a4d2-170">L’exécution de EF Core dans Blazor WebAssembly dépasse le cadre de cet article.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-170">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="6a4d2-171">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="6a4d2-171">Sample app</span></span></h2>

<span data-ttu-id="6a4d2-172">L’exemple d’application a été créé en tant que référence pour les Blazor Server applications qui utilisent EF Core.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-172">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="6a4d2-173">L’exemple d’application comprend une grille avec des opérations de tri et de filtrage, de suppression, d’ajout et de mise à jour.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-173">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="6a4d2-174">L’exemple illustre l’utilisation de EF Core pour gérer l’accès concurrentiel optimiste.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-174">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="6a4d2-175">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6a4d2-175">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6a4d2-176">L’exemple utilise une base de données [SQLite](https://www.sqlite.org/index.html) locale pour qu’elle puisse être utilisée sur n’importe quelle plateforme.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-176">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="6a4d2-177">L’exemple configure également la journalisation de la base de données pour afficher les requêtes SQL qui sont générées.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-177">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="6a4d2-178">Cette configuration est configurée dans `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-178">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="6a4d2-179">Les composants de grille, d’ajout et de vue utilisent le modèle « contexte par opération », où un contexte est créé pour chaque opération.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-179">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="6a4d2-180">Le composant Edit utilise le modèle « contexte par composant », où un contexte est créé pour chaque composant.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-180">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="6a4d2-181">Certains des exemples de code de cette rubrique requièrent des espaces de noms et des services qui ne sont pas affichés.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-181">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="6a4d2-182">Pour inspecter le code entièrement opérationnel, y compris [`@using`](xref:mvc/views/razor#using) les [`@inject`](xref:mvc/views/razor#inject) directives et requises pour obtenir des Razor exemples, consultez l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="6a4d2-182">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="6a4d2-183">Accès à la base de données</span><span class="sxs-lookup"><span data-stu-id="6a4d2-183">Database access</span></span></h2>

<span data-ttu-id="6a4d2-184">EF Core s’appuie sur un <xref:Microsoft.EntityFrameworkCore.DbContext> comme moyen de configurer l’accès à la [base de données](/ef/core/miscellaneous/configuring-dbcontext) et d’agir en tant qu' [*unité de travail*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="6a4d2-184">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="6a4d2-185">EF Core fournit l' <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension pour les applications ASP.net core qui inscrit le contexte en tant que service *étendu* par défaut.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-185">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="6a4d2-186">Dans Blazor Server les applications, cela peut être problématique, car l’instance est partagée par plusieurs composants dans le circuit de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-186">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="6a4d2-187"><xref:Microsoft.EntityFrameworkCore.DbContext> n’est pas thread-safe et n’est pas conçu pour une utilisation simultanée.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-187"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="6a4d2-188">Les durées de vie existantes sont inappropriées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-188">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="6a4d2-189">**Singleton** partage l’État sur tous les utilisateurs de l’application et provoque une utilisation simultanée inappropriée.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-189">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="6a4d2-190">**Étendue** (valeur par défaut) pose un problème similaire entre les composants pour le même utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-190">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="6a4d2-191">Les résultats **transitoires** génèrent une nouvelle instance par demande ; mais comme les composants peuvent être de longue durée de vie, cela se traduit par un contexte plus long que prévu.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-191">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="6a4d2-192">Les recommandations suivantes sont conçues pour fournir une approche cohérente de l’utilisation de EF Core dans les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-192">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="6a4d2-193">Par défaut, envisagez d’utiliser un contexte par opération.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-193">By default, consider using one context per operation.</span></span> <span data-ttu-id="6a4d2-194">Le contexte est conçu pour une instanciation rapide et à faible surcharge :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-194">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="6a4d2-195">Utilisez un indicateur pour empêcher plusieurs opérations simultanées :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-195">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="6a4d2-196">Placez les opérations après la `Loading = true;` ligne dans le `try` bloc.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-196">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="6a4d2-197">Pour les opérations à long terme qui tirent parti du [suivi des modifications](/ef/core/querying/tracking) ou du [contrôle d’accès concurrentiel](/ef/core/saving/concurrency)de EF Core, limitez [le contexte à la durée de vie du composant](#scope-to-the-component-lifetime-3x).</span><span class="sxs-lookup"><span data-stu-id="6a4d2-197">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="6a4d2-198">Nouvelles instances de DbContext</span><span class="sxs-lookup"><span data-stu-id="6a4d2-198">New DbContext instances</span></span></h3>

<span data-ttu-id="6a4d2-199">Le moyen le plus rapide de créer une nouvelle <xref:Microsoft.EntityFrameworkCore.DbContext> instance consiste à utiliser `new` pour créer une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-199">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="6a4d2-200">Toutefois, il existe plusieurs scénarios qui peuvent nécessiter la résolution de dépendances supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-200">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="6a4d2-201">Par exemple, vous souhaiterez peut-être utiliser [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) pour configurer le contexte.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-201">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="6a4d2-202">La solution recommandée pour créer un nouveau <xref:Microsoft.EntityFrameworkCore.DbContext> avec des dépendances consiste à utiliser une fabrique.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-202">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="6a4d2-203">L’exemple d’application implémente sa propre fabrique dans `Data/DbContextFactory.cs` .</span><span class="sxs-lookup"><span data-stu-id="6a4d2-203">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="6a4d2-204">Dans la fabrique précédente :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-204">In the preceding factory:</span></span>

* <span data-ttu-id="6a4d2-205"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfait toutes les dépendances via le fournisseur de services.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-205"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="6a4d2-206">`IDbContextFactory` est disponible dans EF Core ASP.NET Core 5,0 ou version ultérieure. l’interface est donc [implémentée dans l’exemple d’application pour ASP.net Core 3. x](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="6a4d2-206">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="6a4d2-207">L’exemple suivant configure [SQLite](https://www.sqlite.org/index.html) et active la journalisation des données.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-207">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="6a4d2-208">Le code utilise une méthode d’extension pour configurer la fabrique de base de données pour DI et fournir les options par défaut :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-208">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="6a4d2-209">La fabrique est injectée dans des composants et utilisée pour créer de nouvelles instances.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-209">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="6a4d2-210">Par exemple, dans `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-210">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="6a4d2-211">`Wrapper` est une [référence de composant](xref:blazor/components/index#capture-references-to-components) au `GridWrapper` composant.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-211">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="6a4d2-212">Consultez le `Index` composant ( `Pages/Index.razor` ) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="6a4d2-212">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="6a4d2-213">De nouvelles <xref:Microsoft.EntityFrameworkCore.DbContext> instances peuvent être créées avec une fabrique qui vous permet de configurer la chaîne de connexion par `DbContext` , par exemple lorsque vous utilisez [ Identity modèle de ASP.net core]) (XREF : Security/authentication/customize_identity_model) :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-213">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="6a4d2-214">Portée à la durée de vie du composant</span><span class="sxs-lookup"><span data-stu-id="6a4d2-214">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="6a4d2-215">Vous pouvez créer un <xref:Microsoft.EntityFrameworkCore.DbContext> qui existe pour la durée de vie d’un composant.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-215">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="6a4d2-216">Cela vous permet de l’utiliser comme une [unité de travail](https://martinfowler.com/eaaCatalog/unitOfWork.html) et de tirer parti des fonctionnalités intégrées, telles que le suivi des modifications et la résolution de l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-216">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="6a4d2-217">Vous pouvez utiliser la fabrique pour créer un contexte et effectuer le suivi de la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-217">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="6a4d2-218">Tout d’abord, implémentez <xref:System.IDisposable> et injectez la fabrique comme indiqué dans `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-218">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="6a4d2-219">L’exemple d’application s’assure que le contexte est supprimé lorsque le composant est supprimé :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-219">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="6a4d2-220">Enfin, [`OnInitializedAsync`](xref:blazor/components/lifecycle) est substitué pour créer un nouveau contexte.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-220">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="6a4d2-221">Dans l’exemple d’application, [`OnInitializedAsync`](xref:blazor/components/lifecycle) charge le contact dans la même méthode :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-221">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="6a4d2-222">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-222">In the preceding example:</span></span>

* <span data-ttu-id="6a4d2-223">Lorsque `Busy` a la valeur `true` , les opérations asynchrones peuvent commencer.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-223">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="6a4d2-224">Lorsque `Busy` a la valeur `false` , les opérations asynchrones doivent être terminées.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-224">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="6a4d2-225">Placez une logique de gestion des erreurs supplémentaire dans un `catch` bloc.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-225">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="6a4d2-226">Activer la journalisation des données sensibles</span><span class="sxs-lookup"><span data-stu-id="6a4d2-226">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="6a4d2-227"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> comprend les données d’application dans les messages d’exception et la journalisation du Framework.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-227"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="6a4d2-228">Les données journalisées peuvent inclure les valeurs affectées aux propriétés des instances d’entité et les valeurs de paramètre pour les commandes envoyées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-228">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="6a4d2-229">La journalisation des données avec <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> présente un **risque de sécurité**, car elle peut exposer des mots de passe et d’autres informations d’identification personnelle (PII) lorsqu’elle enregistre des instructions SQL exécutées sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="6a4d2-229">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="6a4d2-230">Nous vous recommandons d’activer uniquement <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> pour le développement et le test :</span><span class="sxs-lookup"><span data-stu-id="6a4d2-230">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="6a4d2-231">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6a4d2-231">Additional resources</span></span>

* [<span data-ttu-id="6a4d2-232">Documentation EF Core</span><span class="sxs-lookup"><span data-stu-id="6a4d2-232">EF Core documentation</span></span>](/ef/)
