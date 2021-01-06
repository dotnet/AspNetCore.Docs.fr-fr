---
title: Bien démarrer avec Swashbuckle et ASP.NET Core
author: zuckerthoben
description: Découvrez comment ajouter Swashbuckle à votre projet d’API web ASP.NET Core pour intégrer l’IU Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/26/2020
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
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 9fbd4bc5e91d8f05afc99a4f04c706399f83a518
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057607"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="db0a3-103">Bien démarrer avec Swashbuckle et ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db0a3-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="db0a3-104">De [Shayne Boyer](https://twitter.com/spboyer) et [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="db0a3-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="db0a3-105">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db0a3-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="db0a3-106">Swashbuckle repose sur trois composants principaux :</span><span class="sxs-lookup"><span data-stu-id="db0a3-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="db0a3-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/) : modèle objet Swagger et intergiciel (middleware) pour exposer des objets `SwaggerDocument` sous forme de points de terminaison JSON.</span><span class="sxs-lookup"><span data-stu-id="db0a3-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="db0a3-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/) : générateur Swagger qui crée des objets `SwaggerDocument` directement à partir de vos routes, contrôleurs et modèles.</span><span class="sxs-lookup"><span data-stu-id="db0a3-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="db0a3-109">Ce composant est généralement associé à l’intergiciel du point de terminaison Swagger pour exposer automatiquement un fichier JSON Swagger.</span><span class="sxs-lookup"><span data-stu-id="db0a3-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="db0a3-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): version incorporée de l’outil IU Swagger.</span><span class="sxs-lookup"><span data-stu-id="db0a3-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="db0a3-111">Il interprète le fichier JSON Swagger afin de créer une expérience enrichie et personnalisable permettant de décrire les fonctionnalités de l’API web.</span><span class="sxs-lookup"><span data-stu-id="db0a3-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="db0a3-112">Il intègre des ateliers de test pour les méthodes publiques.</span><span class="sxs-lookup"><span data-stu-id="db0a3-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="db0a3-113">Installation de package</span><span class="sxs-lookup"><span data-stu-id="db0a3-113">Package installation</span></span>

<span data-ttu-id="db0a3-114">Vous pouvez ajouter Swashbuckle en adoptant l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="db0a3-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="db0a3-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db0a3-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="db0a3-116">À partir de la fenêtre **Console du Gestionnaire de package** :</span><span class="sxs-lookup"><span data-stu-id="db0a3-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="db0a3-117">Accéder à **la**  >  console du gestionnaire de  >  **package** Windows</span><span class="sxs-lookup"><span data-stu-id="db0a3-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="db0a3-118">Accédez au répertoire où se trouve le fichier *TodoApi.csproj*.</span><span class="sxs-lookup"><span data-stu-id="db0a3-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="db0a3-119">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="db0a3-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.5.0
    ```

* <span data-ttu-id="db0a3-120">À partir de la boîte de dialogue **Gérer les packages NuGet** :</span><span class="sxs-lookup"><span data-stu-id="db0a3-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="db0a3-121">Cliquez avec le bouton droit sur le projet dans **Explorateur de solutions**  >  **gérer les packages NuGet**</span><span class="sxs-lookup"><span data-stu-id="db0a3-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="db0a3-122">Affectez la valeur « nuget.org » à **Source du package**.</span><span class="sxs-lookup"><span data-stu-id="db0a3-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="db0a3-123">Vérifiez que l’option « Inclure la version préliminaire » est activée</span><span class="sxs-lookup"><span data-stu-id="db0a3-123">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="db0a3-124">Entrez « Swashbuckle.AspNetCore » dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="db0a3-124">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="db0a3-125">Sélectionnez le package « Swashbuckle.AspNetCore » le plus récent sous l’onglet **Parcourir** et cliquez sur **Installer**.</span><span class="sxs-lookup"><span data-stu-id="db0a3-125">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="db0a3-126">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="db0a3-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="db0a3-127">Cliquez avec le bouton droit sur le dossier *Packages* dans **Panneau Solutions** > **Ajouter des packages**.</span><span class="sxs-lookup"><span data-stu-id="db0a3-127">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="db0a3-128">Dans la fenêtre **Ajouter des packages**, sélectionnez « nuget.org » dans la liste déroulante **Source**.</span><span class="sxs-lookup"><span data-stu-id="db0a3-128">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="db0a3-129">Vérifiez que l’option « Afficher les packages en version préliminaire » est activée.</span><span class="sxs-lookup"><span data-stu-id="db0a3-129">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="db0a3-130">Entrez « Swashbuckle.AspNetCore » dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="db0a3-130">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="db0a3-131">Sélectionnez le package « Swashbuckle.AspNetCore » le plus récent dans le volet de résultats, puis cliquez sur **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="db0a3-131">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="db0a3-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="db0a3-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="db0a3-133">Exécutez la commande suivante à partir du **Terminal intégré** :</span><span class="sxs-lookup"><span data-stu-id="db0a3-133">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

### <a name="net-core-cli"></a>[<span data-ttu-id="db0a3-134">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="db0a3-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="db0a3-135">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="db0a3-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="db0a3-136">Ajouter et configurer l’intergiciel (middleware) Swagger</span><span class="sxs-lookup"><span data-stu-id="db0a3-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="db0a3-137">Ajoutez le générateur Swagger à la collection de services dans la méthode `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="db0a3-137">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

<span data-ttu-id="db0a3-138">Dans la méthode `Startup.Configure`, activez l’intergiciel pour traiter le document JSON généré et l’IU Swagger :</span><span class="sxs-lookup"><span data-stu-id="db0a3-138">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="db0a3-139">Swashbuckle s’appuie sur MVC <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> pour découvrir les itinéraires et les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="db0a3-139">Swashbuckle relies on MVC's <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> to discover the routes and endpoints.</span></span> <span data-ttu-id="db0a3-140">Si le projet appelle <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A> , les itinéraires et les points de terminaison sont découverts automatiquement.</span><span class="sxs-lookup"><span data-stu-id="db0a3-140">If the project calls <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A>, routes and endpoints are discovered automatically.</span></span> <span data-ttu-id="db0a3-141">Lors <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A> de l’appel de, la <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> méthode doit être appelée explicitement.</span><span class="sxs-lookup"><span data-stu-id="db0a3-141">When calling <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A>, the <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> method must be explicitly called.</span></span> <span data-ttu-id="db0a3-142">Pour plus d’informations, consultez [Swashbuckle, ApiExplorer et routage](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).</span><span class="sxs-lookup"><span data-stu-id="db0a3-142">For more information, see [Swashbuckle, ApiExplorer, and Routing](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).</span></span>

<span data-ttu-id="db0a3-143">L’appel de méthode `UseSwaggerUI` précédent active le [middleware (intergiciel) des fichiers statiques](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="db0a3-143">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="db0a3-144">Si vous ciblez .NET Framework ou .NET Core 1. x, ajoutez le package NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) au projet.</span><span class="sxs-lookup"><span data-stu-id="db0a3-144">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="db0a3-145">Lancez l’application et accédez à `http://localhost:<port>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="db0a3-145">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="db0a3-146">Le document généré décrivant les points de terminaison s’affiche comme indiqué dans la [spécification openapi (openapi.jssur)](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson).</span><span class="sxs-lookup"><span data-stu-id="db0a3-146">The generated document describing the endpoints appears as shown in [OpenAPI specification (openapi.json)](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson).</span></span>

<span data-ttu-id="db0a3-147">L’interface utilisateur Swagger se trouve à l’adresse `http://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="db0a3-147">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="db0a3-148">Explorez l’API via l’interface utilisateur Swagger et incorporez-la dans d’autres programmes.</span><span class="sxs-lookup"><span data-stu-id="db0a3-148">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="db0a3-149">Pour utiliser l’IU Swagger à la racine de l’application (`http://localhost:<port>/`), définissez la propriété `RoutePrefix` sur une chaîne vide :</span><span class="sxs-lookup"><span data-stu-id="db0a3-149">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="db0a3-150">Si vous utilisez des répertoires avec IIS ou un proxy inverse, définissez le point de terminaison Swagger sur un chemin relatif avec le préfixe `./`.</span><span class="sxs-lookup"><span data-stu-id="db0a3-150">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="db0a3-151">Par exemple : `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="db0a3-151">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="db0a3-152">L’utilisation de `/swagger/v1/swagger.json` indique à l’application de rechercher le fichier JSON à la racine réelle de l’URL (plus le préfixe de la route s’il est utilisé).</span><span class="sxs-lookup"><span data-stu-id="db0a3-152">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="db0a3-153">Par exemple, utilisez `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` au lieu de `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="db0a3-153">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

> [!NOTE]
> <span data-ttu-id="db0a3-154">Par défaut, Swashbuckle génère et expose Swagger JSON dans la version 3,0 de la spécification &mdash; officiellement appelée spécification openapi.</span><span class="sxs-lookup"><span data-stu-id="db0a3-154">By default, Swashbuckle generates and exposes Swagger JSON in version 3.0 of the specification&mdash;officially called the OpenAPI Specification.</span></span> <span data-ttu-id="db0a3-155">Pour prendre en charge la compatibilité descendante, vous pouvez choisir d’exposer JSON au format 2,0 à la place.</span><span class="sxs-lookup"><span data-stu-id="db0a3-155">To support backwards compatibility, you can opt into exposing JSON in the 2.0 format instead.</span></span> <span data-ttu-id="db0a3-156">Ce format 2,0 est important pour les intégrations telles que Microsoft Power Apps et Microsoft Flow qui prennent actuellement en charge OpenAPI version 2,0.</span><span class="sxs-lookup"><span data-stu-id="db0a3-156">This 2.0 format is important for integrations such as Microsoft Power Apps and Microsoft Flow that currently support OpenAPI version 2.0.</span></span> <span data-ttu-id="db0a3-157">Pour choisir le format 2,0, définissez la `SerializeAsV2` propriété dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="db0a3-157">To opt into the 2.0 format, set the `SerializeAsV2` property in `Startup.Configure`:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_Configure&highlight=4-7)]

## <a name="customize-and-extend"></a><span data-ttu-id="db0a3-158">Personnaliser et étendre</span><span class="sxs-lookup"><span data-stu-id="db0a3-158">Customize and extend</span></span>

<span data-ttu-id="db0a3-159">Swagger fournit des options pour documenter le modèle objet et personnaliser l’interface utilisateur en fonction de votre thème.</span><span class="sxs-lookup"><span data-stu-id="db0a3-159">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="db0a3-160">Dans la classe `Startup`, ajoutez les espaces de noms suivants :</span><span class="sxs-lookup"><span data-stu-id="db0a3-160">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="db0a3-161">Informations sur l’API et description</span><span class="sxs-lookup"><span data-stu-id="db0a3-161">API info and description</span></span>

<span data-ttu-id="db0a3-162">L’action de configuration transmise à la méthode `AddSwaggerGen` ajoute des informations comme l’auteur, la license et la description :</span><span class="sxs-lookup"><span data-stu-id="db0a3-162">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

<span data-ttu-id="db0a3-163">Dans la classe `Startup`, importez l’espace de noms suivant pour utiliser la classe `OpenApiInfo` :</span><span class="sxs-lookup"><span data-stu-id="db0a3-163">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="db0a3-164">À l’aide de la `OpenApiInfo` classe, modifiez les informations affichées dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="db0a3-164">Using the `OpenApiInfo` class, modify the information displayed in the UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="db0a3-165">L’IU Swagger affiche les informations de la version :</span><span class="sxs-lookup"><span data-stu-id="db0a3-165">The Swagger UI displays the version's information:</span></span>

![Interface utilisateur de Swagger avec les informations de version : description, auteur et lien pour en savoir plus](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="db0a3-167">Commentaires XML</span><span class="sxs-lookup"><span data-stu-id="db0a3-167">XML comments</span></span>

<span data-ttu-id="db0a3-168">Vous pouvez activer les commentaires XML en adoptant l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="db0a3-168">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="db0a3-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db0a3-169">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="db0a3-170">Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis sélectionnez **Modifier <nom_projet>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="db0a3-170">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="db0a3-171">Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="db0a3-171">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="db0a3-172">Dans **Explorateur de solutions** , cliquez avec le bouton droit sur le projet, puis sélectionnez **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="db0a3-172">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="db0a3-173">Cochez la case **fichier de documentation XML** sous la section **sortie** de l’onglet **générer** .</span><span class="sxs-lookup"><span data-stu-id="db0a3-173">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="db0a3-174">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="db0a3-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="db0a3-175">Dans le *Panneau Solutions*, appuyez sur **contrôle** et cliquez sur le nom du projet.</span><span class="sxs-lookup"><span data-stu-id="db0a3-175">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="db0a3-176">Accédez à **Outils**  >  **modifier le fichier**.</span><span class="sxs-lookup"><span data-stu-id="db0a3-176">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="db0a3-177">Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="db0a3-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="db0a3-178">Ouvrez la boîte de dialogue **Options du projet** > **Générer** > **Compilateur**</span><span class="sxs-lookup"><span data-stu-id="db0a3-178">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="db0a3-179">Cochez la case **générer la documentation XML** sous la section **Options générales** .</span><span class="sxs-lookup"><span data-stu-id="db0a3-179">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="db0a3-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="db0a3-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="db0a3-181">Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="db0a3-181">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="db0a3-182">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="db0a3-182">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="db0a3-183">Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="db0a3-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="db0a3-184">L’activation de commentaires XML fournit des informations de débogage sur les membres et types publics non documentés.</span><span class="sxs-lookup"><span data-stu-id="db0a3-184">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="db0a3-185">Les membres et types non documentés sont indiqués par le message d’avertissement.</span><span class="sxs-lookup"><span data-stu-id="db0a3-185">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="db0a3-186">Par exemple, le message suivant indique une violation du code d’avertissement 1591 :</span><span class="sxs-lookup"><span data-stu-id="db0a3-186">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="db0a3-187">Pour supprimer des avertissements à l’échelle d’un projet, définissez une liste séparée par des points-virgules de codes d’avertissement à ignorer dans le fichier de projet.</span><span class="sxs-lookup"><span data-stu-id="db0a3-187">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="db0a3-188">L’ajout des codes d’avertissement à `$(NoWarn);` applique aussi les [valeurs par défaut C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).</span><span class="sxs-lookup"><span data-stu-id="db0a3-188">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="db0a3-189">Pour supprimer des avertissements uniquement pour des membres spécifiques, placez le code dans les directives de préprocesseur [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning).</span><span class="sxs-lookup"><span data-stu-id="db0a3-189">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="db0a3-190">Cette approche est utile pour le code qui ne doit pas être exposé via les docs de l’API. Dans l’exemple suivant, le code d’avertissement CS1591 est ignoré pour la `Program` classe entière.</span><span class="sxs-lookup"><span data-stu-id="db0a3-190">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="db0a3-191">La mise en œuvre de code d’avertissement est restaurée à la fin de la définition de classe.</span><span class="sxs-lookup"><span data-stu-id="db0a3-191">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="db0a3-192">Spécifier plusieurs codes d’avertissement avec une liste délimitée par des virgules.</span><span class="sxs-lookup"><span data-stu-id="db0a3-192">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="db0a3-193">Configurez Swagger de façon à utiliser le fichier XML généré avec les instructions précédentes.</span><span class="sxs-lookup"><span data-stu-id="db0a3-193">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="db0a3-194">Pour les systèmes d’exploitation Linux ou non-Windows, les chemins et les noms de fichiers peuvent respecter la casse.</span><span class="sxs-lookup"><span data-stu-id="db0a3-194">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="db0a3-195">Par exemple, un fichier *ToDoApi.XML* est valide sur Windows, mais pas sur CentOS.</span><span class="sxs-lookup"><span data-stu-id="db0a3-195">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="db0a3-196">Dans le code précédent, la [réflexion](/dotnet/csharp/programming-guide/concepts/reflection) est utilisée pour générer un nom de fichier XML correspondant à celui du projet d’API Web.</span><span class="sxs-lookup"><span data-stu-id="db0a3-196">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="db0a3-197">La propriété [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) est utilisée pour construire le chemin du fichier XML.</span><span class="sxs-lookup"><span data-stu-id="db0a3-197">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="db0a3-198">Certaines fonctionnalités de Swagger (par exemple, les schémas de paramètres d’entrée ou les méthodes HTTP et les codes de réponse issus des attributs respectifs) fonctionnent sans fichier de documentation XML.</span><span class="sxs-lookup"><span data-stu-id="db0a3-198">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="db0a3-199">Pour la plupart des fonctionnalités cependant, à savoir les résumés de méthode et les descriptions des paramètres et des codes de réponse, l’utilisation d’un fichier XML est obligatoire.</span><span class="sxs-lookup"><span data-stu-id="db0a3-199">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="db0a3-200">Quand vous ajoutez des commentaires précédés de trois barres obliques à une action, Swagger UI ajoute la description à l’en-tête de la section.</span><span class="sxs-lookup"><span data-stu-id="db0a3-200">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="db0a3-201">Ajoutez un [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) élément au-dessus de l' `Delete` action :</span><span class="sxs-lookup"><span data-stu-id="db0a3-201">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="db0a3-202">L’IU Swagger affiche le texte interne de l’élément `<summary>` du code précédent :</span><span class="sxs-lookup"><span data-stu-id="db0a3-202">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Interface utilisateur de Swagger affichant le commentaire XML « Deletes a specific TodoItem. »](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="db0a3-205">L’IU est définie par le schéma JSON généré :</span><span class="sxs-lookup"><span data-stu-id="db0a3-205">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="db0a3-206">Ajoutez un [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) élément à la `Create` documentation de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="db0a3-206">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="db0a3-207">Il complète les informations spécifiées dans l’élément `<summary>` et fournit une interface utilisateur Swagger plus robuste.</span><span class="sxs-lookup"><span data-stu-id="db0a3-207">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="db0a3-208">Le contenu de l’élément `<remarks>` peut être du texte, du code JSON ou du code XML.</span><span class="sxs-lookup"><span data-stu-id="db0a3-208">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="db0a3-209">Notez les améliorations de l’IU avec ces commentaires supplémentaires :</span><span class="sxs-lookup"><span data-stu-id="db0a3-209">Notice the UI enhancements with these additional comments:</span></span>

![Interface utilisateur de Swagger avec des commentaires supplémentaires affichés](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="db0a3-211">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="db0a3-211">Data annotations</span></span>

<span data-ttu-id="db0a3-212">Marquez le modèle avec des attributs, qui se trouvent dans l’espace de noms [System. ComponentModel. DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) , pour aider à piloter les composants de l’interface utilisateur Swagger.</span><span class="sxs-lookup"><span data-stu-id="db0a3-212">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="db0a3-213">Ajoutez l’attribut `[Required]` à la propriété `Name` de la classe `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="db0a3-213">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="db0a3-214">La présence de cet attribut change le comportement de l’interface utilisateur et modifie le schéma JSON sous-jacent :</span><span class="sxs-lookup"><span data-stu-id="db0a3-214">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="db0a3-215">Ajoutez l’attribut `[Produces("application/json")]` au contrôleur d’API.</span><span class="sxs-lookup"><span data-stu-id="db0a3-215">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="db0a3-216">Son objectif est de déclarer que les actions du contrôleur prennent en charge une réponse dont le type de contenu est *application/json* :</span><span class="sxs-lookup"><span data-stu-id="db0a3-216">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="db0a3-217">La liste déroulante **type de contenu** de la réponse sélectionne ce type de contenu comme valeur par défaut pour les actions obtenir du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="db0a3-217">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Interface utilisateur de Swagger avec le type de contenu de réponse par défaut](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="db0a3-219">À mesure que l’utilisation des annotations de données dans l’API web augmente, l’interface utilisateur et les pages d’aide de l’API deviennent de plus en plus descriptives et utiles.</span><span class="sxs-lookup"><span data-stu-id="db0a3-219">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="db0a3-220">Décrire des types de réponse</span><span class="sxs-lookup"><span data-stu-id="db0a3-220">Describe response types</span></span>

<span data-ttu-id="db0a3-221">Les développeurs consommant une API web s’intéressent surtout à ce qui est retourné&mdash;, en particulier les types de réponse et les codes d’erreur (s’ils ne sont pas standards).</span><span class="sxs-lookup"><span data-stu-id="db0a3-221">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="db0a3-222">Les types de réponse et les codes d’erreur sont décrits dans les commentaires XML et les annotations de données.</span><span class="sxs-lookup"><span data-stu-id="db0a3-222">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="db0a3-223">L’action `Create` retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="db0a3-223">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="db0a3-224">Un code d’état HTTP 400 est retourné quand le corps de la demande postée est null.</span><span class="sxs-lookup"><span data-stu-id="db0a3-224">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="db0a3-225">Sans documentation appropriée dans l’interface utilisateur de Swagger, le consommateur n’a pas connaissance de ces résultats attendus.</span><span class="sxs-lookup"><span data-stu-id="db0a3-225">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="db0a3-226">Pour résoudre ce problème, ajoutez les lignes en surbrillance de l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="db0a3-226">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="db0a3-227">L’interface utilisateur de Swagger documente maintenant clairement les codes de réponse HTTP attendus :</span><span class="sxs-lookup"><span data-stu-id="db0a3-227">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Interface utilisateur de Swagger affichant la description de la classe de réponse POST « Returns the newly created Todo item » et « 400 - If the item is null » pour le code d’état et la raison sous Response Messages](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="db0a3-229">Dans ASP.NET Core 2.2 ou une version ultérieure, les conventions peuvent être utilisées comme alternatives à la décoration explicites des actions individuelles avec `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="db0a3-229">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="db0a3-230">Pour plus d’informations, consultez <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="db0a3-230">For more information, see <xref:web-api/advanced/conventions>.</span></span>

<span data-ttu-id="db0a3-231">Pour prendre en charge la `[ProducesResponseType]` décoration, le package [Swashbuckle. AspNetCore. Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) propose des extensions permettant d’activer et d’enrichir la réponse, le schéma et les métadonnées de paramètre.</span><span class="sxs-lookup"><span data-stu-id="db0a3-231">To support the `[ProducesResponseType]` decoration, the [Swashbuckle.AspNetCore.Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) package offers extensions to enable and enrich the response, schema, and parameter metadata.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="db0a3-232">Personnaliser l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="db0a3-232">Customize the UI</span></span>

<span data-ttu-id="db0a3-233">L’interface utilisateur par défaut est à la fois fonctionnelle et présente.</span><span class="sxs-lookup"><span data-stu-id="db0a3-233">The default UI is both functional and presentable.</span></span> <span data-ttu-id="db0a3-234">Toutefois, les pages de documentation d’API doivent représenter votre marque ou thème.</span><span class="sxs-lookup"><span data-stu-id="db0a3-234">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="db0a3-235">La personnalisation des composants Swashbuckle nécessite d’ajouter les ressources qui traitent les fichiers statiques et de générer la structure de dossiers pour héberger ces fichiers.</span><span class="sxs-lookup"><span data-stu-id="db0a3-235">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="db0a3-236">Si vous ciblez le .NET Framework ou .NET Core 1.x, ajoutez le package NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) au projet :</span><span class="sxs-lookup"><span data-stu-id="db0a3-236">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="db0a3-237">Le package NuGet précédent est déjà installé si vous ciblez .NET Core 2.x et que vous utilisez le [métapackage](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="db0a3-237">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="db0a3-238">Activez le middleware de fichiers statiques :</span><span class="sxs-lookup"><span data-stu-id="db0a3-238">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="db0a3-239">Pour injecter des feuilles de style CSS supplémentaires, ajoutez-les au dossier *wwwroot* du projet et spécifiez le chemin d’accès relatif dans les options de l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="db0a3-239">To inject additional CSS stylesheets, add them to the project's *wwwroot* folder and specify the relative path in the middleware options:</span></span>

```csharp
app.UseSwaggerUI(c =>
{
     c.InjectStylesheet("/swagger-ui/custom.css");
}
```
