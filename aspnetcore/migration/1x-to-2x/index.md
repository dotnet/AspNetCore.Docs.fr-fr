---
title: Migrer d’ASP.NET Core 1.x vers la version 2.0
author: scottaddie
description: Cet article présente les prérequis et la plupart des étapes courantes nécessaires pour la migration d’un projet ASP.NET Core 1.x vers ASP.NET Core 2.0.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: migration/1x-to-2x/index
ms.openlocfilehash: 6d67924d87cdbe72cb08c5305dfe45c5b22b31bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057113"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a><span data-ttu-id="9bf69-103">Migrer d’ASP.NET Core 1.x vers la version 2.0</span><span class="sxs-lookup"><span data-stu-id="9bf69-103">Migrate from ASP.NET Core 1.x to 2.0</span></span>

<span data-ttu-id="9bf69-104">Par [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="9bf69-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="9bf69-105">Dans cet article, nous vous montrons comment mettre à jour un projet ASP.NET Core 1.x existant vers ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="9bf69-105">In this article, we walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="9bf69-106">La migration de votre application vers ASP.NET Core 2.0 vous permet de tirer parti de [nombreuses nouvelles fonctionnalités et améliorations des performances](xref:aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="9bf69-106">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](xref:aspnetcore-2.0).</span></span>

<span data-ttu-id="9bf69-107">Les applications ASP.NET Core 1.x existantes sont basées sur des modèles de projet propres à la version.</span><span class="sxs-lookup"><span data-stu-id="9bf69-107">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="9bf69-108">Les modèles de projet et le code de démarrage qu’ils contiennent évoluent en même temps que le framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9bf69-108">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="9bf69-109">Outre le framework ASP.NET Core, vous devez aussi mettre à jour le code de votre application.</span><span class="sxs-lookup"><span data-stu-id="9bf69-109">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="9bf69-110">Prérequis</span><span class="sxs-lookup"><span data-stu-id="9bf69-110">Prerequisites</span></span>

<span data-ttu-id="9bf69-111">Consultez [Bien démarrer avec ASP.NET Core](xref:getting-started).</span><span class="sxs-lookup"><span data-stu-id="9bf69-111">See [Get Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="9bf69-112">Mettre à jour le Moniker du Framework cible</span><span class="sxs-lookup"><span data-stu-id="9bf69-112">Update Target Framework Moniker (TFM)</span></span>

<span data-ttu-id="9bf69-113">Les projets ciblant .NET Core doivent utiliser le [Moniker du Framework cible](/dotnet/standard/frameworks) d’une version supérieure ou égale à .NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="9bf69-113">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="9bf69-114">Recherchez le nœud `<TargetFramework>` dans le fichier *.csproj* et remplacez son texte interne par `netcoreapp2.0` :</span><span class="sxs-lookup"><span data-stu-id="9bf69-114">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

<span data-ttu-id="9bf69-115">Les projets ciblant le .NET Framework doivent utiliser le Moniker du Framework cible d’une version supérieure ou égale à .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="9bf69-115">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="9bf69-116">Recherchez le nœud `<TargetFramework>` dans le fichier *.csproj* et remplacez son texte interne par `net461` :</span><span class="sxs-lookup"><span data-stu-id="9bf69-116">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> <span data-ttu-id="9bf69-117">.NET Core 2.0 offre une surface d’exposition beaucoup plus grande que .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="9bf69-117">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="9bf69-118">Si vous ciblez le .NET Framework uniquement car il manque des API dans .NET Core 1.x, le ciblage de .NET Core 2.0 fonctionnera sans doute.</span><span class="sxs-lookup"><span data-stu-id="9bf69-118">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<span data-ttu-id="9bf69-119">Si le fichier projet contient `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, voir [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span><span class="sxs-lookup"><span data-stu-id="9bf69-119">If the project file contains `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="9bf69-120">Mettre à jour la version du SDK .NET Core dans global.json</span><span class="sxs-lookup"><span data-stu-id="9bf69-120">Update .NET Core SDK version in global.json</span></span>

<span data-ttu-id="9bf69-121">Si votre solution s’appuie sur un fichier [global.json](/dotnet/core/tools/global-json) pour cibler une version spécifique du SDK .NET Core, mettez à jour sa propriété `version` de façon à utiliser la version 2.0 installée sur votre ordinateur :</span><span class="sxs-lookup"><span data-stu-id="9bf69-121">If your solution relies upon a [global.json](/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="9bf69-122">Mettre à jour les références de package</span><span class="sxs-lookup"><span data-stu-id="9bf69-122">Update package references</span></span>

<span data-ttu-id="9bf69-123">Le fichier *.csproj* dans un projet 1.x répertorie chaque package NuGet utilisé par le projet.</span><span class="sxs-lookup"><span data-stu-id="9bf69-123">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="9bf69-124">Dans un projet ASP.NET Core 2.0 ciblant .NET Core 2.0, une seule référence de [métapackage](xref:fundamentals/metapackage) dans le fichier *.csproj* remplace la collection de packages :</span><span class="sxs-lookup"><span data-stu-id="9bf69-124">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

<span data-ttu-id="9bf69-125">Toutes les fonctionnalités d’ASP.NET Core 2.0 et d’Entity Framework Core 2.0 sont incluses dans le métapackage.</span><span class="sxs-lookup"><span data-stu-id="9bf69-125">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="9bf69-126">Les projets ASP.NET Core 2.0 ciblant le .NET Framework doivent continuer à référencer des packages NuGet spécifiques.</span><span class="sxs-lookup"><span data-stu-id="9bf69-126">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="9bf69-127">Mettez à jour l’attribut `Version` de chaque nœud `<PackageReference />` vers la version 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="9bf69-127">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="9bf69-128">Par exemple, voici la liste des nœuds `<PackageReference />` utilisés dans un projet ASP.NET Core 2.0 standard qui cible le .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="9bf69-128">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="9bf69-129">Mettre à jour les outils CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="9bf69-129">Update .NET Core CLI tools</span></span>

<span data-ttu-id="9bf69-130">Dans le fichier *.csproj* , mettez à jour l’attribut `Version` de chaque nœud `<DotNetCliToolReference />` vers la version 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="9bf69-130">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="9bf69-131">Par exemple, voici la liste des outils CLI utilisés dans un projet ASP.NET Core 2.0 standard qui cible .NET Core 2.0 :</span><span class="sxs-lookup"><span data-stu-id="9bf69-131">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="9bf69-132">Renommer la propriété PackageTargetFallback</span><span class="sxs-lookup"><span data-stu-id="9bf69-132">Rename Package Target Fallback property</span></span>

<span data-ttu-id="9bf69-133">Le fichier *.csproj* d’un projet 1.x utilise un nœud et une variable `PackageTargetFallback` :</span><span class="sxs-lookup"><span data-stu-id="9bf69-133">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

<span data-ttu-id="9bf69-134">Renommez le nœud et la variable `AssetTargetFallback` :</span><span class="sxs-lookup"><span data-stu-id="9bf69-134">Rename both the node and variable to `AssetTargetFallback`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="9bf69-135">Mettre à jour la méthode Main dans Program.cs</span><span class="sxs-lookup"><span data-stu-id="9bf69-135">Update Main method in Program.cs</span></span>

<span data-ttu-id="9bf69-136">Dans les projets 1.x, la méthode `Main` de *Program.cs* ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="9bf69-136">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

<span data-ttu-id="9bf69-137">Dans les projets 2.0, la méthode `Main` de *Program.cs* a été simplifiée :</span><span class="sxs-lookup"><span data-stu-id="9bf69-137">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

<span data-ttu-id="9bf69-138">L’adoption de ce nouveau modèle 2.0 est vivement recommandée, et même obligatoire pour bénéficier des fonctionnalités de produit telles que [Entity Framework (EF) Core Migrations](xref:data/ef-mvc/migrations).</span><span class="sxs-lookup"><span data-stu-id="9bf69-138">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework (EF) Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="9bf69-139">Par exemple, l’exécution de `Update-Database` à partir de la fenêtre de la Console du Gestionnaire de Package ou de `dotnet ef database update` à partir de la ligne de commande (sur des projets convertis vers ASP.NET Core 2.0) génère l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="9bf69-139">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a><span data-ttu-id="9bf69-140">Ajouter des fournisseurs de configuration</span><span class="sxs-lookup"><span data-stu-id="9bf69-140">Add configuration providers</span></span>

<span data-ttu-id="9bf69-141">Dans les projets 1.x, l’ajout de fournisseurs de configuration à une application a été effectué via le constructeur `Startup`.</span><span class="sxs-lookup"><span data-stu-id="9bf69-141">In 1.x projects, adding configuration providers to an app was accomplished via the `Startup` constructor.</span></span> <span data-ttu-id="9bf69-142">Étapes nécessaires à la création d’une instance de `ConfigurationBuilder`, chargement des fournisseurs applicables (variables d’environnement, paramètres de l’application, etc.) et initialisation d’un membre de `IConfigurationRoot`.</span><span class="sxs-lookup"><span data-stu-id="9bf69-142">The steps involved creating an instance of `ConfigurationBuilder`, loading applicable providers (environment variables, app settings, etc.), and initializing a member of `IConfigurationRoot`.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

<span data-ttu-id="9bf69-143">L’exemple précédent charge le `Configuration` membre avec les paramètres de configuration de *:::no-loc(appsettings.json):::* et tout *appSettings. \<EnvironmentName\> fichier JSON* correspondant à la `IHostingEnvironment.EnvironmentName` propriété.</span><span class="sxs-lookup"><span data-stu-id="9bf69-143">The preceding example loads the `Configuration` member with configuration settings from *:::no-loc(appsettings.json):::* as well as any *appsettings.\<EnvironmentName\>.json* file matching the `IHostingEnvironment.EnvironmentName` property.</span></span> <span data-ttu-id="9bf69-144">L’emplacement de ces fichiers est sur le même chemin que *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="9bf69-144">The location of these files is at the same path as *Startup.cs* .</span></span>

<span data-ttu-id="9bf69-145">Dans les projets 2.0, le code de configuration réutilisable inhérent aux projets 1.x s’exécute en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="9bf69-145">In 2.0 projects, the boilerplate configuration code inherent to 1.x projects runs behind-the-scenes.</span></span> <span data-ttu-id="9bf69-146">Par exemple, les variables d’environnement et les paramètres de l’application sont chargés au démarrage.</span><span class="sxs-lookup"><span data-stu-id="9bf69-146">For example, environment variables and app settings are loaded at startup.</span></span> <span data-ttu-id="9bf69-147">Le code équivalent *Startup.cs* est réduit à l’initialisation de `IConfiguration` avec l’instance injectée :</span><span class="sxs-lookup"><span data-stu-id="9bf69-147">The equivalent *Startup.cs* code is reduced to `IConfiguration` initialization with the injected instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

<span data-ttu-id="9bf69-148">Pour supprimer les fournisseurs par défaut ajoutés par `WebHostBuilder.CreateDefaultBuilder`, appelez la méthode `Clear` sur la propriété `IConfigurationBuilder.Sources` dans `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="9bf69-148">To remove the default providers added by `WebHostBuilder.CreateDefaultBuilder`, invoke the `Clear` method on the `IConfigurationBuilder.Sources` property inside of `ConfigureAppConfiguration`.</span></span> <span data-ttu-id="9bf69-149">Pour rajouter fournisseurs, utilisez la méthode `ConfigureAppConfiguration` dans *Program.cs*  :</span><span class="sxs-lookup"><span data-stu-id="9bf69-149">To add providers back, utilize the `ConfigureAppConfiguration` method in *Program.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

<span data-ttu-id="9bf69-150">La configuration utilisée par la méthode `CreateDefaultBuilder` dans l’extrait de code précédent peut être consultée [ici](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="9bf69-150">The configuration used by the `CreateDefaultBuilder` method in the preceding code snippet can be seen [here](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span></span>

<span data-ttu-id="9bf69-151">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9bf69-151">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a><span data-ttu-id="9bf69-152">Déplacer le code d’initialisation de base de données</span><span class="sxs-lookup"><span data-stu-id="9bf69-152">Move database initialization code</span></span>

<span data-ttu-id="9bf69-153">Dans les projets 1.x utilisant EF Core 1.x, une commande telle que `dotnet ef migrations add` effectue les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bf69-153">In 1.x projects using EF Core 1.x, a command such as `dotnet ef migrations add` does the following:</span></span>

1. <span data-ttu-id="9bf69-154">Instancie une instance `Startup`</span><span class="sxs-lookup"><span data-stu-id="9bf69-154">Instantiates a `Startup` instance</span></span>
1. <span data-ttu-id="9bf69-155">Appelle la méthode `ConfigureServices` pour inscrire tous les services à l’injection de dépendances (notamment les types `DbContext`)</span><span class="sxs-lookup"><span data-stu-id="9bf69-155">Invokes the `ConfigureServices` method to register all services with dependency injection (including `DbContext` types)</span></span>
1. <span data-ttu-id="9bf69-156">Exécute ses tâches obligatoires</span><span class="sxs-lookup"><span data-stu-id="9bf69-156">Performs its requisite tasks</span></span>

<span data-ttu-id="9bf69-157">Dans les projets 2.0 utilisant EF Core 2.0, `Program.BuildWebHost` est appelé pour obtenir les services d’application.</span><span class="sxs-lookup"><span data-stu-id="9bf69-157">In 2.0 projects using EF Core 2.0, `Program.BuildWebHost` is invoked to obtain the application services.</span></span> <span data-ttu-id="9bf69-158">Contrairement à 1.x, cela a pour autre effet secondaire d’appeler `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9bf69-158">Unlike 1.x, this has the additional side effect of invoking `Startup.Configure`.</span></span> <span data-ttu-id="9bf69-159">Si votre application 1.x a appelé le code d’initialisation de base de données dans sa méthode `Configure`, des problèmes inattendus peuvent se produire.</span><span class="sxs-lookup"><span data-stu-id="9bf69-159">If your 1.x app invoked database initialization code in its `Configure` method, unexpected problems can occur.</span></span> <span data-ttu-id="9bf69-160">Par exemple, si la base de données n’existe pas encore, le code d’amorçage s’exécute avant l’exécution de la commande EF Core Migrations.</span><span class="sxs-lookup"><span data-stu-id="9bf69-160">For example, if the database doesn't yet exist, the seeding code runs before the EF Core Migrations command execution.</span></span> <span data-ttu-id="9bf69-161">Ce problème entraîne l’échec de la commande `dotnet ef migrations list` si la base de données n’existe pas encore.</span><span class="sxs-lookup"><span data-stu-id="9bf69-161">This problem causes a `dotnet ef migrations list` command to fail if the database doesn't yet exist.</span></span>

<span data-ttu-id="9bf69-162">Envisagez le code d’initialisation d’amorçage 1.x suivant dans la méthode `Configure` de *Startup.cs*  :</span><span class="sxs-lookup"><span data-stu-id="9bf69-162">Consider the following 1.x seed initialization code in the `Configure` method of *Startup.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

<span data-ttu-id="9bf69-163">Dans les projets 2.0, déplacez l’appel de `SeedData.Initialize` vers la méthode `Main` de *Program.cs*  :</span><span class="sxs-lookup"><span data-stu-id="9bf69-163">In 2.0 projects, move the `SeedData.Initialize` call to the `Main` method of *Program.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

<span data-ttu-id="9bf69-164">À compter de la version 2.0, il est déconseillé de faire quoi que ce soit dans `BuildWebHost`, sauf de créer et de configurer l’hôte web.</span><span class="sxs-lookup"><span data-stu-id="9bf69-164">As of 2.0, it's bad practice to do anything in `BuildWebHost` except build and configure the web host.</span></span> <span data-ttu-id="9bf69-165">Tout ce qui concerne l’exécution de l’application doit être géré en dehors de `BuildWebHost` &mdash; généralement dans la méthode `Main` de *Program.cs* .</span><span class="sxs-lookup"><span data-stu-id="9bf69-165">Anything that's about running the application should be handled outside of `BuildWebHost` &mdash; typically in the `Main` method of *Program.cs* .</span></span>

<a name="view-compilation"></a>

## <a name="review-no-locrazor-view-compilation-setting"></a><span data-ttu-id="9bf69-166">Vérifier le paramètre de compilation de la :::no-loc(Razor)::: vue</span><span class="sxs-lookup"><span data-stu-id="9bf69-166">Review :::no-loc(Razor)::: view compilation setting</span></span>

<span data-ttu-id="9bf69-167">La vitesse de démarrage d’application et la taille des bundles publiés ont une importance capitale pour vous.</span><span class="sxs-lookup"><span data-stu-id="9bf69-167">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="9bf69-168">Pour ces raisons, la compilation de l' [ :::no-loc(Razor)::: affichage](xref:mvc/views/view-compilation) est activée par défaut dans ASP.net Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="9bf69-168">For these reasons, [:::no-loc(Razor)::: view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="9bf69-169">L’affectation de la valeur true à la propriété `Mvc:::no-loc(Razor):::CompileOnPublish` n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="9bf69-169">Setting the `Mvc:::no-loc(Razor):::CompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="9bf69-170">Vous pouvez supprimer la propriété du fichier *.csproj* , sauf si vous désactivez la compilation des vues.</span><span class="sxs-lookup"><span data-stu-id="9bf69-170">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="9bf69-171">Quand vous ciblez .NET Framework, vous devez toujours faire référence explicitement à [Microsoft. AspNetCore. Mvc. :::no-loc(Razor):::](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.ViewCompilation) Package NuGet ViewCompilation dans votre fichier *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="9bf69-171">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="9bf69-172">S’appuyer sur les fonctionnalités de mise en évidence d’Application Insights</span><span class="sxs-lookup"><span data-stu-id="9bf69-172">Rely on Application Insights "light-up" features</span></span>

<span data-ttu-id="9bf69-173">La configuration sans effort de l’instrumentation des performances d’application est importante.</span><span class="sxs-lookup"><span data-stu-id="9bf69-173">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="9bf69-174">Vous pouvez maintenant vous appuyer sur les nouvelles fonctionnalités de mise en évidence [d’Application Insights](/azure/application-insights/app-insights-overview) disponibles dans les outils Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="9bf69-174">You can now rely on the new [Application Insights](/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="9bf69-175">Par défaut, les projets ASP.NET Core 1.1 créés dans Visual Studio 2017 ajoutaient Application Insights.</span><span class="sxs-lookup"><span data-stu-id="9bf69-175">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="9bf69-176">Si vous n’utilisez pas le SDK Application Insights directement, en dehors de *Program.cs* et *Startup.cs* , effectuez les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bf69-176">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs* , follow these steps:</span></span>

1. <span data-ttu-id="9bf69-177">Si vous ciblez .NET Core, supprimez le nœud `<PackageReference />` suivant dans le fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="9bf69-177">If targeting .NET Core, remove the following `<PackageReference />` node from the *.csproj* file:</span></span>

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. <span data-ttu-id="9bf69-178">Si vous ciblez .NET Core, supprimez l’appel de méthode d’extension `UseApplicationInsights` de *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="9bf69-178">If targeting .NET Core, remove the `UseApplicationInsights` extension method invocation from *Program.cs* :</span></span>

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. <span data-ttu-id="9bf69-179">Supprimez l’appel d’API côté client Application Insights du fichier *_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9bf69-179">Remove the Application Insights client-side API call from *_Layout.cshtml* .</span></span> <span data-ttu-id="9bf69-180">Il comprend les deux lignes de code suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bf69-180">It comprises the following two lines of code:</span></span>

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

<span data-ttu-id="9bf69-181">Si vous utilisez le SDK Application Insights directement, continuez à le faire.</span><span class="sxs-lookup"><span data-stu-id="9bf69-181">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="9bf69-182">La version 2.0 du [métapackage](xref:fundamentals/metapackage) inclut la dernière version d’Application Insights. Par conséquent, une erreur de passage à une version antérieure de package s’affiche si vous référencez une version antérieure.</span><span class="sxs-lookup"><span data-stu-id="9bf69-182">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationno-locidentity-improvements"></a><span data-ttu-id="9bf69-183">Adopter l’authentification/les :::no-loc(Identity)::: améliorations</span><span class="sxs-lookup"><span data-stu-id="9bf69-183">Adopt authentication/:::no-loc(Identity)::: improvements</span></span>

<span data-ttu-id="9bf69-184">ASP.NET Core 2,0 dispose d’un nouveau modèle d’authentification et d’un certain nombre de modifications significatives apportées à :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="9bf69-184">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="9bf69-185">Si vous avez créé votre projet avec des comptes d’utilisateur individuels activés, ou si vous avez ajouté manuellement l’authentification ou :::no-loc(Identity)::: , consultez [migrer l’authentification et :::no-loc(Identity)::: vers ASP.net Core 2,0](xref:migration/1x-to-2x/identity-2x).</span><span class="sxs-lookup"><span data-stu-id="9bf69-185">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or :::no-loc(Identity):::, see [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9bf69-186">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9bf69-186">Additional resources</span></span>

* [<span data-ttu-id="9bf69-187">Changements importants dans ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="9bf69-187">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
