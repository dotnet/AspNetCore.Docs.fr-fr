---
title: Migrer d’ASP.NET vers ASP.NET Core 2.0
author: isaac2004
description: Recevez des conseils pour migrer des applications ASP.NET MVC ou Web API existantes vers ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
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
uid: migration/mvc2
ms.openlocfilehash: cf7d2e3a94c14fb752180d9349536d17b4557e0a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051328"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="c1afe-103">Migrer d’ASP.NET vers ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="c1afe-103">Migrate from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="c1afe-104">De [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="c1afe-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="c1afe-105">Cet article sert de guide de référence pour la migration d’applications ASP.NET vers ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="c1afe-105">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c1afe-106">Prérequis</span><span class="sxs-lookup"><span data-stu-id="c1afe-106">Prerequisites</span></span>

<span data-ttu-id="c1afe-107">Installez l' **un** des éléments suivants à partir de [téléchargements .net : Windows](https://dotnet.microsoft.com/download):</span><span class="sxs-lookup"><span data-stu-id="c1afe-107">Install **one** of the following from [.NET Downloads: Windows](https://dotnet.microsoft.com/download):</span></span>

* <span data-ttu-id="c1afe-108">SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="c1afe-108">.NET Core SDK</span></span>
* <span data-ttu-id="c1afe-109">Visual Studio pour Windows</span><span class="sxs-lookup"><span data-stu-id="c1afe-109">Visual Studio for Windows</span></span>
  * <span data-ttu-id="c1afe-110">Charge de travail **Développement web et ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="c1afe-110">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="c1afe-111">Charge de travail **Développement multiplateforme .NET Core**</span><span class="sxs-lookup"><span data-stu-id="c1afe-111">**.NET Core cross-platform development** workload</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="c1afe-112">Versions cibles de .NET Framework</span><span class="sxs-lookup"><span data-stu-id="c1afe-112">Target frameworks</span></span>

<span data-ttu-id="c1afe-113">Les projets ASP.NET Core 2.0 permettent aux développeurs de cibler le .NET Core, le .NET Framework ou les deux.</span><span class="sxs-lookup"><span data-stu-id="c1afe-113">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="c1afe-114">Consultez [Choix entre le .NET Core et le .NET Framework pour les applications serveur](/dotnet/standard/choosing-core-framework-server) afin de déterminer quel est le framework cible le plus approprié.</span><span class="sxs-lookup"><span data-stu-id="c1afe-114">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="c1afe-115">Quand vous ciblez le .NET Framework, les projets doivent référencer des packages NuGet individuels.</span><span class="sxs-lookup"><span data-stu-id="c1afe-115">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="c1afe-116">Le ciblage du .NET Core vous permet d’éliminer de nombreuses références de packages explicites, grâce au [métapackage](xref:fundamentals/metapackage) ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="c1afe-116">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="c1afe-117">Installez le métapackage `Microsoft.AspNetCore.All` dans votre projet :</span><span class="sxs-lookup"><span data-stu-id="c1afe-117">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

<span data-ttu-id="c1afe-118">Quand le métapackage est utilisé, aucun package référencé dans le métapackage n’est déployé avec l’application.</span><span class="sxs-lookup"><span data-stu-id="c1afe-118">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="c1afe-119">Le magasin de runtimes du .NET Core inclut ces composants. Ceux-ci sont précompilés pour améliorer les performances.</span><span class="sxs-lookup"><span data-stu-id="c1afe-119">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="c1afe-120"><xref:fundamentals/metapackage>Pour plus d’informations, consultez.</span><span class="sxs-lookup"><span data-stu-id="c1afe-120">See <xref:fundamentals/metapackage> for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="c1afe-121">Différences de structure de projet</span><span class="sxs-lookup"><span data-stu-id="c1afe-121">Project structure differences</span></span>

<span data-ttu-id="c1afe-122">Le format de fichier *.csproj* a été simplifié dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1afe-122">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="c1afe-123">Voici certains changements notables :</span><span class="sxs-lookup"><span data-stu-id="c1afe-123">Some notable changes include:</span></span>

* <span data-ttu-id="c1afe-124">Il n’est pas nécessaire d’inclure explicitement les fichiers pour qu’ils soient considérés comme faisant partie du projet.</span><span class="sxs-lookup"><span data-stu-id="c1afe-124">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="c1afe-125">Cela réduit le risque de conflits de fusion XML quand vous travaillez avec des équipes de grande taille.</span><span class="sxs-lookup"><span data-stu-id="c1afe-125">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
* <span data-ttu-id="c1afe-126">Il n’existe aucune référence basée sur un GUID à d’autres projets, ce qui améliore la lisibilité du fichier.</span><span class="sxs-lookup"><span data-stu-id="c1afe-126">There are no GUID-based references to other projects, which improves file readability.</span></span>
* <span data-ttu-id="c1afe-127">Vous pouvez modifier le fichier sans devoir le décharger dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="c1afe-127">The file can be edited without unloading it in Visual Studio:</span></span>

  ![Option de menu contextuel de modification du CSPROJ dans Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="c1afe-129">Remplacement du fichier Global.asax</span><span class="sxs-lookup"><span data-stu-id="c1afe-129">Global.asax file replacement</span></span>

<span data-ttu-id="c1afe-130">ASP.NET Core a introduit un nouveau mécanisme pour le démarrage d’une application.</span><span class="sxs-lookup"><span data-stu-id="c1afe-130">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="c1afe-131">Le point d’entrée des applications ASP.NET est le fichier *Global.asax* .</span><span class="sxs-lookup"><span data-stu-id="c1afe-131">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="c1afe-132">Les tâches telles que la configuration de l’itinéraire ou l’inscription des filtres et des zones sont traitées dans le fichier *Global.asax* .</span><span class="sxs-lookup"><span data-stu-id="c1afe-132">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="c1afe-133">Cette approche couple l’application au serveur sur lequel elle est déployée d’une manière qui interfère avec l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="c1afe-133">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="c1afe-134">Pour y remédier, [OWIN](https://owin.org/) a donc été introduit afin d’optimiser l’utilisation de plusieurs frameworks à la fois.</span><span class="sxs-lookup"><span data-stu-id="c1afe-134">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="c1afe-135">OWIN fournit un pipeline qui permet d’ajouter uniquement les modules nécessaires.</span><span class="sxs-lookup"><span data-stu-id="c1afe-135">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="c1afe-136">L’environnement d’hébergement utilise une fonction [Startup](xref:fundamentals/startup) pour configurer les services et le pipeline de requêtes de l’application.</span><span class="sxs-lookup"><span data-stu-id="c1afe-136">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="c1afe-137">`Startup` inscrit un ensemble d’intergiciels (middleware) auprès de l’application.</span><span class="sxs-lookup"><span data-stu-id="c1afe-137">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="c1afe-138">Pour chaque requête, l’application appelle chacun des composants intergiciels (middleware) à l’aide du pointeur d’en-tête d’une liste liée à un ensemble existant de gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="c1afe-138">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="c1afe-139">Chaque composant intergiciel (middleware) peut ajouter un ou plusieurs gestionnaires au pipeline de traitement des requêtes.</span><span class="sxs-lookup"><span data-stu-id="c1afe-139">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="c1afe-140">Pour ce faire, une référence doit être retournée au gestionnaire qui représente le nouvel en-tête de la liste.</span><span class="sxs-lookup"><span data-stu-id="c1afe-140">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="c1afe-141">Chaque gestionnaire doit mémoriser et appeler le prochain gestionnaire de la liste.</span><span class="sxs-lookup"><span data-stu-id="c1afe-141">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="c1afe-142">Avec ASP.NET Core, comme le point d’entrée d’une application est `Startup`, vous n’avez plus de dépendance relative à *Global.asax* .</span><span class="sxs-lookup"><span data-stu-id="c1afe-142">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax* .</span></span> <span data-ttu-id="c1afe-143">Quand vous employez OWIN avec le .NET Framework, utilisez l’exemple de code suivant en tant que pipeline :</span><span class="sxs-lookup"><span data-stu-id="c1afe-143">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="c1afe-144">Cela permet de configurer vos itinéraires par défaut, et de privilégier XmlSerialization à JSON.</span><span class="sxs-lookup"><span data-stu-id="c1afe-144">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="c1afe-145">Ajoutez d’autres intergiciels (middleware) à ce pipeline selon les besoins (services de chargement, paramètres de configuration, fichiers statiques, etc.).</span><span class="sxs-lookup"><span data-stu-id="c1afe-145">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="c1afe-146">ASP.NET Core utilise une approche similaire mais n’a pas besoin d’OWIN pour prendre en charge l’entrée.</span><span class="sxs-lookup"><span data-stu-id="c1afe-146">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="c1afe-147">Au lieu de cela, cette opération est effectuée via la méthode *Program.cs* `Main` (semblable aux applications console) et `Startup` est chargée par l’intermédiaire de là.</span><span class="sxs-lookup"><span data-stu-id="c1afe-147">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="c1afe-148">`Startup` doit inclure une méthode `Configure`.</span><span class="sxs-lookup"><span data-stu-id="c1afe-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="c1afe-149">Dans `Configure`, ajoutez l’intergiciel (middleware) nécessaire au pipeline.</span><span class="sxs-lookup"><span data-stu-id="c1afe-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="c1afe-150">Dans l’exemple suivant (provenant du modèle de site web par défaut), plusieurs méthodes d’extension permettent de configurer le pipeline pour une prise en charge des éléments ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="c1afe-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="c1afe-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="c1afe-151">BrowserLink</span></span>](https://vswebessentials.com/features/browserlink)
* <span data-ttu-id="c1afe-152">Pages d’erreur</span><span class="sxs-lookup"><span data-stu-id="c1afe-152">Error pages</span></span>
* <span data-ttu-id="c1afe-153">Fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="c1afe-153">Static files</span></span>
* <span data-ttu-id="c1afe-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="c1afe-154">ASP.NET Core MVC</span></span>
* :::no-loc(Identity):::

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="c1afe-155">L’hôte et l’application ont été découplés, ce qui permet de passer facilement plus tard à une autre plateforme.</span><span class="sxs-lookup"><span data-stu-id="c1afe-155">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="c1afe-156">Pour obtenir des informations de référence plus approfondies sur le démarrage et l’intergiciel (middleware) ASP.NET Core, consultez <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="c1afe-156">For a more in-depth reference to ASP.NET Core Startup and Middleware, see <xref:fundamentals/startup>.</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="c1afe-157">Stockage des configurations</span><span class="sxs-lookup"><span data-stu-id="c1afe-157">Storing configurations</span></span>

<span data-ttu-id="c1afe-158">ASP.NET prend en charge le stockage des paramètres.</span><span class="sxs-lookup"><span data-stu-id="c1afe-158">ASP.NET supports storing settings.</span></span> <span data-ttu-id="c1afe-159">Ces paramètres sont utilisés, par exemple, pour prendre en charge l’environnement sur lequel les applications ont été déployées.</span><span class="sxs-lookup"><span data-stu-id="c1afe-159">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="c1afe-160">Habituellement, toutes les paires clé-valeur personnalisées sont stockées dans la section `<appSettings>` du fichier *Web.config*  :</span><span class="sxs-lookup"><span data-stu-id="c1afe-160">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="c1afe-161">Les applications lisent ces paramètres à l’aide de la collection `ConfigurationManager.AppSettings` dans l’espace de noms `System.Configuration` :</span><span class="sxs-lookup"><span data-stu-id="c1afe-161">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="c1afe-162">ASP.NET Core peut stocker les données de configuration de l’application dans un fichier et les charger dans le cadre du démarrage d’un intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="c1afe-162">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="c1afe-163">Le fichier par défaut utilisé dans les modèles de projet est le *:::no-loc(appsettings.json):::* suivant :</span><span class="sxs-lookup"><span data-stu-id="c1afe-163">The default file used in the project templates is *:::no-loc(appsettings.json):::* :</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="c1afe-164">Le chargement de ce fichier dans une instance de `IConfiguration` au sein de votre application s’effectue dans *Startup.cs*  :</span><span class="sxs-lookup"><span data-stu-id="c1afe-164">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs* :</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="c1afe-165">L’application lit `Configuration` pour obtenir les paramètres :</span><span class="sxs-lookup"><span data-stu-id="c1afe-165">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="c1afe-166">Il existe des extensions à cette approche pour rendre le processus plus robuste, par exemple en utilisant l’[injection de dépendances](xref:fundamentals/dependency-injection) pour charger un service avec ces valeurs.</span><span class="sxs-lookup"><span data-stu-id="c1afe-166">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="c1afe-167">L’approche par injection de dépendances fournit un ensemble d’objets de configuration fortement typés.</span><span class="sxs-lookup"><span data-stu-id="c1afe-167">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

<span data-ttu-id="c1afe-168">**Remarque :** Pour obtenir des informations de référence plus approfondies sur la configuration de ASP.NET Core, consultez <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="c1afe-168">**Note:** For a more in-depth reference to ASP.NET Core configuration, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="c1afe-169">Injection de dépendances native</span><span class="sxs-lookup"><span data-stu-id="c1afe-169">Native dependency injection</span></span>

<span data-ttu-id="c1afe-170">Quand vous générez des applications majeures et scalables, il est important d’avoir un couplage faible entre les composants et les services.</span><span class="sxs-lookup"><span data-stu-id="c1afe-170">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="c1afe-171">L' [injection de dépendances](xref:fundamentals/dependency-injection) est une technique courante pour y parvenir, et c’est un composant natif de ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="c1afe-171">[Dependency injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="c1afe-172">Dans les applications ASP.NET, les développeurs s’appuient sur une bibliothèque tierce pour implémenter l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="c1afe-172">In ASP.NET applications, developers rely on a third-party library to implement dependency injection.</span></span> <span data-ttu-id="c1afe-173">L’une de ces bibliothèques, [Unity](https://github.com/unitycontainer/unity), est fournie par Microsoft Patterns & Practices.</span><span class="sxs-lookup"><span data-stu-id="c1afe-173">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="c1afe-174">Un exemple de configuration de l’injection de dépendances avec Unity est l’implémentation de `IDependencyResolver` qui encapsule un `UnityContainer` :</span><span class="sxs-lookup"><span data-stu-id="c1afe-174">An example of setting up dependency injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="c1afe-175">Créez une instance de `UnityContainer`, inscrivez votre service, puis définissez le programme de résolution de dépendances de `HttpConfiguration` en fonction de la nouvelle instance de `UnityResolver` pour votre conteneur :</span><span class="sxs-lookup"><span data-stu-id="c1afe-175">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="c1afe-176">Injectez `IProductRepository` aux emplacements nécessaires :</span><span class="sxs-lookup"><span data-stu-id="c1afe-176">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="c1afe-177">Étant donné que l’injection de dépendances fait partie de ASP.NET Core, vous pouvez ajouter votre service dans le `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c1afe-177">Because dependency injection is part of ASP.NET Core, you can add your service in the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="c1afe-178">Vous pouvez injecter le dépôt à l’emplacement de votre choix, comme c’était le cas avec Unity.</span><span class="sxs-lookup"><span data-stu-id="c1afe-178">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="c1afe-179">Pour plus d’informations sur l’injection de dépendances dans ASP.NET Core, consultez <xref:fundamentals/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="c1afe-179">For more information on dependency injection in ASP.NET Core, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="c1afe-180">Traitement des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="c1afe-180">Serving static files</span></span>

<span data-ttu-id="c1afe-181">Une partie importante du développement web réside dans la capacité de traitement des composants statiques, côté client.</span><span class="sxs-lookup"><span data-stu-id="c1afe-181">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="c1afe-182">Les fichiers HTML, CSS, JavaScript et image sont les exemples les plus courants de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="c1afe-182">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="c1afe-183">Ces fichiers doivent être enregistrés à l’emplacement publié de l’application (ou CDN) et référencés pour pouvoir être chargés par une requête.</span><span class="sxs-lookup"><span data-stu-id="c1afe-183">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="c1afe-184">Ce processus a changé avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1afe-184">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="c1afe-185">Avec ASP.NET, les fichiers statiques sont stockés dans différents répertoires et référencés dans des vues.</span><span class="sxs-lookup"><span data-stu-id="c1afe-185">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="c1afe-186">Dans ASP.NET Core, les fichiers statiques sont stockés dans la « racine Web » ( *&lt; racine du contenu &gt; /wwwroot* ), sauf si configuré dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="c1afe-186">In ASP.NET Core, static files are stored in the "web root" ( *&lt;content root&gt;/wwwroot* ), unless configured otherwise.</span></span> <span data-ttu-id="c1afe-187">Les fichiers sont chargés dans le pipeline de requêtes via l’appel de la méthode d’extension `UseStaticFiles` à partir de `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="c1afe-187">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

<span data-ttu-id="c1afe-188">**Remarque :** Si vous ciblez le .NET Framework, installez le package NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="c1afe-188">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="c1afe-189">Par exemple, un composant image dans le dossier *wwwroot/images* est accessible au navigateur à un emplacement tel que `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="c1afe-189">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="c1afe-190">**Remarque :** Pour obtenir des informations de référence plus approfondies sur le traitement des fichiers statiques dans ASP.NET Core, consultez <xref:fundamentals/static-files> .</span><span class="sxs-lookup"><span data-stu-id="c1afe-190">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see <xref:fundamentals/static-files>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1afe-191">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c1afe-191">Additional resources</span></span>

* [<span data-ttu-id="c1afe-192">Portage des bibliothèques vers le .NET Core</span><span class="sxs-lookup"><span data-stu-id="c1afe-192">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
