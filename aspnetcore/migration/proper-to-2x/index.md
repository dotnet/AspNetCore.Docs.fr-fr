---
title: Migrer d’ASP.NET vers ASP.NET Core
author: isaac2004
description: Recevoir des conseils de migration d’applications ASP.NET MVC ou Web API existantes vers ASP.NET Core.web
ms.author: scaddie
ms.date: 10/18/2019
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
uid: migration/proper-to-2x/index
ms.openlocfilehash: 059ddc18d0c531efaba8aab916ddbb27b42b5e2c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053551"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="8430f-103">Migrer d’ASP.NET vers ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8430f-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="8430f-104">De [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="8430f-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="8430f-105">Cet article sert de guide de référence pour la migration d’applications ASP.NET vers ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8430f-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8430f-106">Prérequis</span><span class="sxs-lookup"><span data-stu-id="8430f-106">Prerequisites</span></span>

[<span data-ttu-id="8430f-107">Kit SDK .NET Core 2,2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="8430f-107">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a><span data-ttu-id="8430f-108">Versions cibles de .NET Framework</span><span class="sxs-lookup"><span data-stu-id="8430f-108">Target frameworks</span></span>

<span data-ttu-id="8430f-109">Les projets ASP.NET Core permettent aux développeurs de cibler .NET Core, le .NET Framework ou les deux.</span><span class="sxs-lookup"><span data-stu-id="8430f-109">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="8430f-110">Consultez [Choix entre le .NET Core et le .NET Framework pour les applications serveur](/dotnet/standard/choosing-core-framework-server) afin de déterminer quel est le framework cible le plus approprié.</span><span class="sxs-lookup"><span data-stu-id="8430f-110">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="8430f-111">Quand vous ciblez le .NET Framework, les projets doivent référencer des packages NuGet individuels.</span><span class="sxs-lookup"><span data-stu-id="8430f-111">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="8430f-112">Le ciblage du .NET Core vous permet d’éliminer de nombreuses références de packages explicites, grâce au [métapackage](xref:fundamentals/metapackage-app) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8430f-112">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="8430f-113">Installez le métapackage `Microsoft.AspNetCore.App` dans votre projet :</span><span class="sxs-lookup"><span data-stu-id="8430f-113">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="8430f-114">Quand le métapackage est utilisé, aucun package référencé dans le métapackage n’est déployé avec l’application.</span><span class="sxs-lookup"><span data-stu-id="8430f-114">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="8430f-115">Le magasin de runtimes du .NET Core inclut ces composants. Ceux-ci sont précompilés pour améliorer les performances.</span><span class="sxs-lookup"><span data-stu-id="8430f-115">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="8430f-116">Pour plus d’informations, consultez [Métapackage Microsoft.AspNetCore.App pour ASP.NET Core](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8430f-116">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="8430f-117">Différences de structure de projet</span><span class="sxs-lookup"><span data-stu-id="8430f-117">Project structure differences</span></span>

<span data-ttu-id="8430f-118">Le format de fichier *.csproj* a été simplifié dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8430f-118">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="8430f-119">Voici certains changements notables :</span><span class="sxs-lookup"><span data-stu-id="8430f-119">Some notable changes include:</span></span>

- <span data-ttu-id="8430f-120">Il n’est pas nécessaire d’inclure explicitement les fichiers pour qu’ils soient considérés comme faisant partie du projet.</span><span class="sxs-lookup"><span data-stu-id="8430f-120">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="8430f-121">Cela réduit le risque de conflits de fusion XML quand vous travaillez avec des équipes de grande taille.</span><span class="sxs-lookup"><span data-stu-id="8430f-121">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="8430f-122">Il n’existe aucune référence basée sur un GUID à d’autres projets, ce qui améliore la lisibilité du fichier.</span><span class="sxs-lookup"><span data-stu-id="8430f-122">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="8430f-123">Vous pouvez modifier le fichier sans devoir le décharger dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="8430f-123">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Option de menu contextuel de modification du CSPROJ dans Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="8430f-125">Remplacement du fichier Global.asax</span><span class="sxs-lookup"><span data-stu-id="8430f-125">Global.asax file replacement</span></span>

<span data-ttu-id="8430f-126">ASP.NET Core a introduit un nouveau mécanisme pour le démarrage d’une application.</span><span class="sxs-lookup"><span data-stu-id="8430f-126">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="8430f-127">Le point d’entrée des applications ASP.NET est le fichier *Global.asax* .</span><span class="sxs-lookup"><span data-stu-id="8430f-127">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="8430f-128">Les tâches telles que la configuration de l’itinéraire ou l’inscription des filtres et des zones sont traitées dans le fichier *Global.asax* .</span><span class="sxs-lookup"><span data-stu-id="8430f-128">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="8430f-129">Cette approche couple l’application au serveur sur lequel elle est déployée d’une manière qui interfère avec l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="8430f-129">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="8430f-130">Pour y remédier, [OWIN](https://owin.org/) a donc été introduit afin d’optimiser l’utilisation de plusieurs frameworks à la fois.</span><span class="sxs-lookup"><span data-stu-id="8430f-130">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="8430f-131">OWIN fournit un pipeline qui permet d’ajouter uniquement les modules nécessaires.</span><span class="sxs-lookup"><span data-stu-id="8430f-131">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="8430f-132">L’environnement d’hébergement utilise une fonction [Startup](xref:fundamentals/startup) pour configurer les services et le pipeline de requêtes de l’application.</span><span class="sxs-lookup"><span data-stu-id="8430f-132">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="8430f-133">`Startup` inscrit un ensemble d’intergiciels (middleware) auprès de l’application.</span><span class="sxs-lookup"><span data-stu-id="8430f-133">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="8430f-134">Pour chaque requête, l’application appelle chacun des composants intergiciels (middleware) à l’aide du pointeur d’en-tête d’une liste liée à un ensemble existant de gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="8430f-134">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="8430f-135">Chaque composant intergiciel (middleware) peut ajouter un ou plusieurs gestionnaires au pipeline de traitement des requêtes.</span><span class="sxs-lookup"><span data-stu-id="8430f-135">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="8430f-136">Pour ce faire, une référence doit être retournée au gestionnaire qui représente le nouvel en-tête de la liste.</span><span class="sxs-lookup"><span data-stu-id="8430f-136">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="8430f-137">Chaque gestionnaire doit mémoriser et appeler le prochain gestionnaire de la liste.</span><span class="sxs-lookup"><span data-stu-id="8430f-137">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="8430f-138">Avec ASP.NET Core, comme le point d’entrée d’une application est `Startup`, vous n’avez plus de dépendance relative à *Global.asax* .</span><span class="sxs-lookup"><span data-stu-id="8430f-138">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax* .</span></span> <span data-ttu-id="8430f-139">Quand vous employez OWIN avec le .NET Framework, utilisez l’exemple de code suivant en tant que pipeline :</span><span class="sxs-lookup"><span data-stu-id="8430f-139">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="8430f-140">Cela permet de configurer vos itinéraires par défaut, et de privilégier XmlSerialization à JSON.</span><span class="sxs-lookup"><span data-stu-id="8430f-140">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="8430f-141">Ajoutez d’autres intergiciels (middleware) à ce pipeline selon les besoins (services de chargement, paramètres de configuration, fichiers statiques, etc.).</span><span class="sxs-lookup"><span data-stu-id="8430f-141">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="8430f-142">ASP.NET Core utilise une approche similaire mais n’a pas besoin d’OWIN pour prendre en charge l’entrée.</span><span class="sxs-lookup"><span data-stu-id="8430f-142">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="8430f-143">Au lieu de cela, cette opération est effectuée via la méthode *Program.cs* `Main` (semblable aux applications console) et `Startup` est chargée par l’intermédiaire de là.</span><span class="sxs-lookup"><span data-stu-id="8430f-143">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="8430f-144">`Startup` doit inclure une méthode `Configure`.</span><span class="sxs-lookup"><span data-stu-id="8430f-144">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="8430f-145">Dans `Configure`, ajoutez l’intergiciel (middleware) nécessaire au pipeline.</span><span class="sxs-lookup"><span data-stu-id="8430f-145">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="8430f-146">Dans l’exemple suivant (provenant du modèle de site web par défaut), des méthodes d’extension configurent le pipeline pour une prise en charge des éléments ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="8430f-146">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="8430f-147">Pages d’erreur</span><span class="sxs-lookup"><span data-stu-id="8430f-147">Error pages</span></span>
- <span data-ttu-id="8430f-148">HTTP Strict Transport Security</span><span class="sxs-lookup"><span data-stu-id="8430f-148">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="8430f-149">Redirection HTTP vers HTTPS</span><span class="sxs-lookup"><span data-stu-id="8430f-149">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="8430f-150">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="8430f-150">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="8430f-151">L’hôte et l’application ont été découplés, ce qui permet de passer facilement plus tard à une autre plateforme.</span><span class="sxs-lookup"><span data-stu-id="8430f-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="8430f-152">Pour obtenir des informations de référence plus approfondies sur le démarrage des applications dans ASP.NET Core et sur les intergiciels (middleware), consultez [Démarrage dans ASP.NET Core](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="8430f-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="8430f-153">Stocker les configurations</span><span class="sxs-lookup"><span data-stu-id="8430f-153">Store configurations</span></span>

<span data-ttu-id="8430f-154">ASP.NET prend en charge le stockage des paramètres.</span><span class="sxs-lookup"><span data-stu-id="8430f-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="8430f-155">Ces paramètres sont utilisés, par exemple, pour prendre en charge l’environnement sur lequel les applications ont été déployées.</span><span class="sxs-lookup"><span data-stu-id="8430f-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="8430f-156">Habituellement, toutes les paires clé-valeur personnalisées sont stockées dans la section `<appSettings>` du fichier *Web.config*  :</span><span class="sxs-lookup"><span data-stu-id="8430f-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="8430f-157">Les applications lisent ces paramètres à l’aide de la collection `ConfigurationManager.AppSettings` dans l’espace de noms `System.Configuration` :</span><span class="sxs-lookup"><span data-stu-id="8430f-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="8430f-158">ASP.NET Core peut stocker les données de configuration de l’application dans un fichier et les charger dans le cadre du démarrage d’un intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="8430f-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="8430f-159">Le fichier par défaut utilisé dans les modèles de projet est le *appsettings.json* suivant :</span><span class="sxs-lookup"><span data-stu-id="8430f-159">The default file used in the project templates is *appsettings.json* :</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="8430f-160">Le chargement de ce fichier dans une instance de `IConfiguration` au sein de votre application s’effectue dans *Startup.cs*  :</span><span class="sxs-lookup"><span data-stu-id="8430f-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs* :</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="8430f-161">L’application lit `Configuration` pour obtenir les paramètres :</span><span class="sxs-lookup"><span data-stu-id="8430f-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="8430f-162">Il existe des extensions à cette approche pour rendre le processus plus robuste, par exemple en utilisant l’[injection de dépendances](xref:fundamentals/dependency-injection) pour charger un service avec ces valeurs.</span><span class="sxs-lookup"><span data-stu-id="8430f-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="8430f-163">L’approche par injection de dépendances fournit un ensemble d’objets de configuration fortement typés.</span><span class="sxs-lookup"><span data-stu-id="8430f-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> <span data-ttu-id="8430f-164">Pour obtenir des informations de référence plus approfondies sur la configuration d’ASP.NET Core, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8430f-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="8430f-165">Injection de dépendances native</span><span class="sxs-lookup"><span data-stu-id="8430f-165">Native dependency injection</span></span>

<span data-ttu-id="8430f-166">Quand vous générez des applications majeures et scalables, il est important d’avoir un couplage faible entre les composants et les services.</span><span class="sxs-lookup"><span data-stu-id="8430f-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="8430f-167">L’[injection de dépendances](xref:fundamentals/dependency-injection) est une technique répandue qui permet d’y parvenir. Elle représente un composant natif d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8430f-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="8430f-168">Dans les applications ASP.NET, les développeurs s’appuient sur une bibliothèque tierce pour implémenter l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="8430f-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="8430f-169">L’une de ces bibliothèques, [Unity](https://github.com/unitycontainer/unity), est fournie par Microsoft Patterns & Practices.</span><span class="sxs-lookup"><span data-stu-id="8430f-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="8430f-170">L’implémentation de `IDependencyResolver` qui inclut `UnityContainer` dans un wrapper est un exemple de configuration d’une injection de dépendances avec Unity :</span><span class="sxs-lookup"><span data-stu-id="8430f-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="8430f-171">Créez une instance de `UnityContainer`, inscrivez votre service, puis définissez le programme de résolution de dépendances de `HttpConfiguration` en fonction de la nouvelle instance de `UnityResolver` pour votre conteneur :</span><span class="sxs-lookup"><span data-stu-id="8430f-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="8430f-172">Injectez `IProductRepository` aux emplacements nécessaires :</span><span class="sxs-lookup"><span data-stu-id="8430f-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="8430f-173">Dans la mesure où l’injection de dépendances fait partie d’ASP.NET Core, vous pouvez ajouter votre service à la méthode `ConfigureServices` de *Startup.cs*  :</span><span class="sxs-lookup"><span data-stu-id="8430f-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs* :</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="8430f-174">Vous pouvez injecter le dépôt à l’emplacement de votre choix, comme c’était le cas avec Unity.</span><span class="sxs-lookup"><span data-stu-id="8430f-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="8430f-175">Pour plus d’informations sur l’injection de dépendances, consultez [Injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8430f-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="8430f-176">Délivrer des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="8430f-176">Serve static files</span></span>

<span data-ttu-id="8430f-177">Une partie importante du développement web réside dans la capacité de traitement des composants statiques, côté client.</span><span class="sxs-lookup"><span data-stu-id="8430f-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="8430f-178">Les fichiers HTML, CSS, JavaScript et image sont les exemples les plus courants de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="8430f-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="8430f-179">Ces fichiers doivent être enregistrés à l’emplacement publié de l’application (ou CDN) et référencés pour pouvoir être chargés par une requête.</span><span class="sxs-lookup"><span data-stu-id="8430f-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="8430f-180">Ce processus a changé avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8430f-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="8430f-181">Avec ASP.NET, les fichiers statiques sont stockés dans différents répertoires et référencés dans des vues.</span><span class="sxs-lookup"><span data-stu-id="8430f-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="8430f-182">Dans ASP.NET Core, les fichiers statiques sont stockés dans la « racine Web » ( *&lt; racine du contenu &gt; /wwwroot* ), sauf si configuré dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="8430f-182">In ASP.NET Core, static files are stored in the "web root" ( *&lt;content root&gt;/wwwroot* ), unless configured otherwise.</span></span> <span data-ttu-id="8430f-183">Les fichiers sont chargés dans le pipeline de requêtes via l’appel de la méthode d’extension `UseStaticFiles` à partir de `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="8430f-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="8430f-184">Si vous ciblez le .NET Framework, installez le package NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="8430f-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="8430f-185">Par exemple, un composant image dans le dossier *wwwroot/images* est accessible au navigateur à un emplacement tel que `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="8430f-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="8430f-186">Pour obtenir des informations de référence plus approfondies sur le traitement des fichiers statiques dans ASP.NET Core, consultez [Fichiers statiques](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="8430f-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="multi-value-no-loccookies"></a><span data-ttu-id="8430f-187">cookieValeurs multiples</span><span class="sxs-lookup"><span data-stu-id="8430f-187">Multi-value cookies</span></span>

<span data-ttu-id="8430f-188">[Les valeurs à cookie valeurs multiples](xref:System.Web.HttpCookie.Values) ne sont pas prises en charge dans ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="8430f-188">[Multi-value cookies](xref:System.Web.HttpCookie.Values) aren't supported in ASP.NET Core.</span></span> <span data-ttu-id="8430f-189">Créez un cookie par valeur.</span><span class="sxs-lookup"><span data-stu-id="8430f-189">Create one cookie per value.</span></span>

## <a name="partial-app-migration"></a><span data-ttu-id="8430f-190">Migration d’application partielle</span><span class="sxs-lookup"><span data-stu-id="8430f-190">Partial app migration</span></span>

<span data-ttu-id="8430f-191">L’une des approches de la migration partielle d’applications consiste à créer une sous-application IIS et à déplacer uniquement certains itinéraires de ASP.NET 4. x vers ASP.NET Core tout en conservant la structure de l’URL de l’application.</span><span class="sxs-lookup"><span data-stu-id="8430f-191">One approach to partial app migration is to create an IIS sub-application and only move certain routes from ASP.NET 4.x to ASP.NET Core while preserving the URL structure the app.</span></span> <span data-ttu-id="8430f-192">Par exemple, considérez la structure de l’URL de l’application à partir du fichier *applicationHost.config* :</span><span class="sxs-lookup"><span data-stu-id="8430f-192">For example, consider the URL structure of the app from the *applicationHost.config* file:</span></span>

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

<span data-ttu-id="8430f-193">Structure de répertoire :</span><span class="sxs-lookup"><span data-stu-id="8430f-193">Directory structure:</span></span>

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="bind-and-input-formatters"></a><span data-ttu-id="8430f-194">[BIND] et formateurs d’entrée</span><span class="sxs-lookup"><span data-stu-id="8430f-194">[BIND] and Input Formatters</span></span>

<span data-ttu-id="8430f-195">Les [versions précédentes de ASP.net](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view) utilisaient l' `[Bind]` attribut pour vous protéger contre les attaques de survalidation.</span><span class="sxs-lookup"><span data-stu-id="8430f-195">[Previous versions of ASP.NET](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view) used the `[Bind]` attribute to protect against overposting attacks.</span></span> <span data-ttu-id="8430f-196">Les [formateurs d’entrée](xref:mvc/models/model-binding#input-formatters) fonctionnent différemment dans ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="8430f-196">[Input formatters](xref:mvc/models/model-binding#input-formatters) work differently in ASP.NET Core.</span></span> <span data-ttu-id="8430f-197">L' `[Bind]` attribut n’est plus conçu pour empêcher la survalidation lorsqu’il est utilisé avec des formateurs d’entrée pour analyser JSON ou XML.</span><span class="sxs-lookup"><span data-stu-id="8430f-197">The `[Bind]` attribute is no longer designed to prevent overposting when used with input formatters to parse JSON or XML.</span></span> <span data-ttu-id="8430f-198">Ces attributs affectent la liaison de modèle lorsque la source de données est une donnée de formulaire publiée avec le `x-www-form-urlencoded` type de contenu.</span><span class="sxs-lookup"><span data-stu-id="8430f-198">These attributes affect model binding when the source of data is form data posted with the `x-www-form-urlencoded` content type.</span></span>

<span data-ttu-id="8430f-199">Pour les applications qui publient des informations JSON sur les contrôleurs et utilisent des formateurs d’entrée JSON pour analyser les données, nous vous recommandons de remplacer l' `[Bind]` attribut par un modèle de vue qui correspond aux propriétés définies par l' `[Bind]` attribut.</span><span class="sxs-lookup"><span data-stu-id="8430f-199">For apps that post JSON information to controllers and use JSON Input Formatters to parse the data, we recommend replacing the `[Bind]` attribute with a view model that matches the properties defined by the `[Bind]` attribute.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8430f-200">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8430f-200">Additional resources</span></span>

- [<span data-ttu-id="8430f-201">Portage des bibliothèques vers le .NET Core</span><span class="sxs-lookup"><span data-stu-id="8430f-201">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
