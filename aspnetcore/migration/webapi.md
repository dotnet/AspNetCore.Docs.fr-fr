---
title: Migrer de API Web ASP.NET vers ASP.NET Core
author: ardalis
description: Découvrez comment migrer une implémentation d’API Web à partir de l’API Web ASP.NET 4. x vers ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: 320805c0d40bf06cee384e6d98caea5c420d45bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061468"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="8a5d6-103">Migrer de API Web ASP.NET vers ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a5d6-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="8a5d6-104">Par [Scott Addie](https://twitter.com/scott_addie) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8a5d6-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8a5d6-105">Une API Web ASP.NET 4. x est un service HTTP qui atteint un large éventail de clients, y compris des navigateurs et des appareils mobiles.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="8a5d6-106">ASP.NET Core associe des modèles d’application MVC et API Web ASP.NET 4. x dans un modèle de programmation unique appelé ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-106">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="8a5d6-107">Cet article décrit les étapes nécessaires à la migration de l’API Web ASP.NET 4. x vers ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="8a5d6-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a5d6-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="8a5d6-109">Prérequis</span><span class="sxs-lookup"><span data-stu-id="8a5d6-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="8a5d6-110">Examiner le projet d’API Web ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="8a5d6-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="8a5d6-111">Cet article utilise le projet *ProductsApp* créé dans [prise en main avec API Web ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="8a5d6-111">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="8a5d6-112">Dans ce projet, un projet d’API Web ASP.NET 4. x de base est configuré comme suit.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-112">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="8a5d6-113">Dans *global.asax.cs* , un appel est effectué à `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-113">In *Global.asax.cs* , a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="8a5d6-114">La `WebApiConfig` classe se trouve dans le dossier *App_Start* et a une `Register` méthode statique :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="8a5d6-115">La classe précédente :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-115">The preceding class:</span></span>

* <span data-ttu-id="8a5d6-116">Configure le [routage des attributs](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), bien qu’il ne soit pas réellement utilisé.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-116">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="8a5d6-117">Configure la table de routage.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-117">Configures the routing table.</span></span>
<span data-ttu-id="8a5d6-118">L’exemple de code s’attend à ce que les URL correspondent au format `/api/{controller}/{id}` , avec `{id}` facultatif.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-118">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="8a5d6-119">Les sections suivantes montrent comment migrer le projet d’API Web vers ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-119">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="8a5d6-120">Créer le projet de destination</span><span class="sxs-lookup"><span data-stu-id="8a5d6-120">Create the destination project</span></span>

<span data-ttu-id="8a5d6-121">Créez une nouvelle solution vide dans Visual Studio et ajoutez le projet d’API Web ASP.NET 4. x à migrer :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-121">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="8a5d6-122">Dans le menu **fichier** , sélectionnez **nouveau** > **projet** .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-122">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="8a5d6-123">Sélectionnez le modèle de **solution vide** , puis cliquez sur **suivant** .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-123">Select the **Blank Solution** template and select **Next** .</span></span>
1. <span data-ttu-id="8a5d6-124">Nommez la solution *WebAPIMigration* .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-124">Name the solution *WebAPIMigration* .</span></span> <span data-ttu-id="8a5d6-125">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="8a5d6-125">Select **Create** .</span></span>
1. <span data-ttu-id="8a5d6-126">Ajoutez le projet *ProductsApp* existant à la solution.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-126">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="8a5d6-127">Ajoutez un nouveau projet d’API à migrer vers :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-127">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="8a5d6-128">Ajoutez un nouveau **ASP.net Core projet d’application Web** à la solution.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="8a5d6-129">Dans la boîte de dialogue **configurer votre nouveau projet** , nommez le projet *ProductsCore* , puis sélectionnez **créer** .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-129">In the **Configure your new project** dialog, Name the project *ProductsCore* , and select **Create** .</span></span>
1. <span data-ttu-id="8a5d6-130">Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 3,1** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="8a5d6-131">Sélectionnez le modèle de projet **API** , puis sélectionnez **Créer** .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-131">Select the **API** project template, and select **Create** .</span></span>
1. <span data-ttu-id="8a5d6-132">Supprimez les fichiers d’exemple *WeatherForecast.cs* et *Controllers/WeatherForecastController. cs* du nouveau projet *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-132">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="8a5d6-133">La solution contient maintenant deux projets.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-133">The solution now contains two projects.</span></span> <span data-ttu-id="8a5d6-134">Les sections suivantes expliquent comment migrer le contenu du projet *ProductsApp* vers le projet *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-134">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="8a5d6-135">Migrer une configuration</span><span class="sxs-lookup"><span data-stu-id="8a5d6-135">Migrate configuration</span></span>

<span data-ttu-id="8a5d6-136">ASP.NET Core n’utilise pas le dossier *App_Start* ou le fichier *global. asax* .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-136">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="8a5d6-137">En outre, le fichier *web.config* est ajouté au moment de la publication.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-137">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="8a5d6-138">La classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-138">The `Startup` class:</span></span>

* <span data-ttu-id="8a5d6-139">Remplace *global. asax* .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-139">Replaces *Global.asax* .</span></span>
* <span data-ttu-id="8a5d6-140">Gère toutes les tâches de démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="8a5d6-141">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-141">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="8a5d6-142">Migrer les modèles et les contrôleurs</span><span class="sxs-lookup"><span data-stu-id="8a5d6-142">Migrate models and controllers</span></span>

<span data-ttu-id="8a5d6-143">Le code suivant montre le `ProductsController` à mettre à jour pour ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-143">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="8a5d6-144">Mettez à jour le `ProductsController` pour ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-144">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="8a5d6-145">Copiez les *contrôleurs/ProductsController. cs* et le dossier *modèles* du projet d’origine vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-145">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="8a5d6-146">Remplacez l’espace de noms racine des fichiers copiés par `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-146">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="8a5d6-147">Mettez à jour l' `using ProductsApp.Models;` instruction avec la valeur `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-147">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="8a5d6-148">Les composants suivants n’existent pas dans ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-148">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="8a5d6-149">Classe `ApiController`</span><span class="sxs-lookup"><span data-stu-id="8a5d6-149">`ApiController` class</span></span>
* <span data-ttu-id="8a5d6-150">Espace de noms `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="8a5d6-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="8a5d6-151">Interface `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="8a5d6-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="8a5d6-152">Effectuez les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-152">Make the following changes:</span></span>

1. <span data-ttu-id="8a5d6-153">Remplacez `ApiController` par <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="8a5d6-154">Ajoutez `using Microsoft.AspNetCore.Mvc;` pour résoudre la `ControllerBase` référence.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="8a5d6-155">Supprimez `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="8a5d6-156">Remplacez le `GetProduct` type de retour de l’action par `IHttpActionResult` `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="8a5d6-157">Simplifiez l’instruction de l' `GetProduct` action comme `return` suit :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="8a5d6-158">Configurer le routage</span><span class="sxs-lookup"><span data-stu-id="8a5d6-158">Configure routing</span></span>

<span data-ttu-id="8a5d6-159">Le modèle de projet d' *API* ASP.net core comprend la configuration du routage de point de terminaison dans le code généré.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-159">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="8a5d6-160">Les éléments suivants <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> et <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> appellent :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-160">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="8a5d6-161">Inscrire la correspondance d’itinéraire et l’exécution du point de terminaison dans le pipeline du [middleware](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-161">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="8a5d6-162">Remplacez le fichier *App_Start/webapiconfig.cs* du projet *ProductsApp* .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-162">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="8a5d6-163">Configurez le routage comme suit :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-163">Configure routing as follows:</span></span>

1. <span data-ttu-id="8a5d6-164">Marquez la `ProductsController` classe avec les attributs suivants :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-164">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="8a5d6-165">L' [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribut précédent configure le modèle de routage des attributs du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-165">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="8a5d6-166">L' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut rend l’acheminement des attributs obligatoire pour toutes les actions dans ce contrôleur.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-166">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="8a5d6-167">Le routage d’attributs prend en charge les jetons, tels que `[controller]` et `[action]` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-167">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="8a5d6-168">Lors de l’exécution, chaque jeton est remplacé par le nom du contrôleur ou de l’action, respectivement, auquel l’attribut a été appliqué.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-168">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="8a5d6-169">Les jetons :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-169">The tokens:</span></span>
    * <span data-ttu-id="8a5d6-170">Réduisez le nombre de chaînes magiques dans le projet.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-170">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="8a5d6-171">Assurez-vous que les itinéraires restent synchronisés avec les contrôleurs et actions correspondants lorsque les refactorisations de renommage automatique sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-171">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="8a5d6-172">Activez les demandes HTTP d’extraction aux `ProductController` actions :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-172">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="8a5d6-173">Appliquez l' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut à l' `GetAllProducts` action.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-173">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="8a5d6-174">Appliquez l' `[HttpGet("{id}")]` attribut à l' `GetProduct` action.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-174">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="8a5d6-175">Exécutez le projet migré et accédez à `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="8a5d6-176">La liste complète des trois produits s’affiche.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-176">A full list of three products appears.</span></span> <span data-ttu-id="8a5d6-177">Accédez à `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="8a5d6-178">Le premier produit s’affiche.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-178">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a5d6-179">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8a5d6-179">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="8a5d6-180">Prérequis</span><span class="sxs-lookup"><span data-stu-id="8a5d6-180">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="8a5d6-181">Examiner le projet d’API Web ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="8a5d6-181">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="8a5d6-182">Cet article utilise le projet *ProductsApp* créé dans [prise en main avec API Web ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="8a5d6-182">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="8a5d6-183">Dans ce projet, un projet d’API Web ASP.NET 4. x de base est configuré comme suit.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-183">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="8a5d6-184">Dans *global.asax.cs* , un appel est effectué à `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-184">In *Global.asax.cs* , a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="8a5d6-185">La `WebApiConfig` classe se trouve dans le dossier *App_Start* et a une `Register` méthode statique :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-185">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="8a5d6-186">Cette classe configure le [routage des attributs](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), bien qu’elle ne soit pas réellement utilisée dans le projet.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-186">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="8a5d6-187">Elle configure également la table de routage, qui est utilisée par API Web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-187">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="8a5d6-188">Dans ce cas, l’API Web ASP.NET 4. x s’attend à ce que les URL correspondent au format `/api/{controller}/{id}` , avec `{id}` facultatif.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-188">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="8a5d6-189">Les sections suivantes montrent comment migrer le projet d’API Web vers ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-189">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="8a5d6-190">Créer le projet de destination</span><span class="sxs-lookup"><span data-stu-id="8a5d6-190">Create the destination project</span></span>

<span data-ttu-id="8a5d6-191">Effectuez les étapes suivantes dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-191">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="8a5d6-192">Accédez à **fichier**  >  **nouveau**  >  **projet**  >  **autres types de projets**  >  **Visual Studio solutions** .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-192">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions** .</span></span> <span data-ttu-id="8a5d6-193">Sélectionnez **solution vide** et nommez la solution *WebAPIMigration* .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-193">Select **Blank Solution** , and name the solution *WebAPIMigration* .</span></span> <span data-ttu-id="8a5d6-194">Cliquez sur le bouton **OK** .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-194">Click the **OK** button.</span></span>
* <span data-ttu-id="8a5d6-195">Ajoutez le projet *ProductsApp* existant à la solution.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-195">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="8a5d6-196">Ajoutez un nouveau **ASP.net Core projet d’application Web** à la solution.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-196">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="8a5d6-197">Sélectionnez le Framework cible **.net Core** dans la liste déroulante, puis sélectionnez le modèle de projet **API** .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-197">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="8a5d6-198">Nommez le projet *ProductsCore* , puis cliquez sur le bouton **OK** .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-198">Name the project *ProductsCore* , and click the **OK** button.</span></span>

<span data-ttu-id="8a5d6-199">La solution contient maintenant deux projets.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-199">The solution now contains two projects.</span></span> <span data-ttu-id="8a5d6-200">Les sections suivantes expliquent comment migrer le contenu du projet *ProductsApp* vers le projet *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-200">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="8a5d6-201">Migrer une configuration</span><span class="sxs-lookup"><span data-stu-id="8a5d6-201">Migrate configuration</span></span>

<span data-ttu-id="8a5d6-202">ASP.NET Core n’utilise pas :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-202">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="8a5d6-203">*App_Start* dossier ou le fichier *global. asax*</span><span class="sxs-lookup"><span data-stu-id="8a5d6-203">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="8a5d6-204">*web.config* fichier est ajouté au moment de la publication.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-204">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="8a5d6-205">La classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-205">The `Startup` class:</span></span>

* <span data-ttu-id="8a5d6-206">Remplace *global. asax* .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-206">Replaces *Global.asax* .</span></span>
* <span data-ttu-id="8a5d6-207">Gère toutes les tâches de démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-207">Handles all app startup tasks.</span></span>

<span data-ttu-id="8a5d6-208">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-208">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="8a5d6-209">Dans ASP.NET Core MVC, le routage des attributs est inclus par défaut quand <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> est appelé dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-209">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="8a5d6-210">L' `UseMvc` appel suivant remplace le fichier *App_Start/webapiconfig.cs* du projet *ProductsApp* :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-210">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="8a5d6-211">Migrer les modèles et les contrôleurs</span><span class="sxs-lookup"><span data-stu-id="8a5d6-211">Migrate models and controllers</span></span>

<span data-ttu-id="8a5d6-212">Le code suivant illustre la `ProductsController` mise à jour de ASP.net Core : [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="8a5d6-212">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="8a5d6-213">Mettez à jour le `ProductsController` pour ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-213">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="8a5d6-214">Copiez les *contrôleurs/ProductsController. cs* du projet d’origine vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-214">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="8a5d6-215">Copiez le dossier *Models* du projet d’origine vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-215">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="8a5d6-216">Remplacez l’espace de noms racine des fichiers copiés par `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-216">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="8a5d6-217">Mettez à jour l' `using ProductsApp.Models;` instruction avec la valeur `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-217">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="8a5d6-218">Les composants suivants n’existent pas dans ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-218">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="8a5d6-219">Classe `ApiController`</span><span class="sxs-lookup"><span data-stu-id="8a5d6-219">`ApiController` class</span></span>
* <span data-ttu-id="8a5d6-220">Espace de noms `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="8a5d6-220">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="8a5d6-221">Interface `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="8a5d6-221">`IHttpActionResult` interface</span></span>

<span data-ttu-id="8a5d6-222">Effectuez les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-222">Make the following changes:</span></span>

1. <span data-ttu-id="8a5d6-223">Remplacez `ApiController` par <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-223">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="8a5d6-224">Ajoutez `using Microsoft.AspNetCore.Mvc;` pour résoudre la `ControllerBase` référence.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-224">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="8a5d6-225">Supprimez `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-225">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="8a5d6-226">Remplacez le `GetProduct` type de retour de l’action par `IHttpActionResult` `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-226">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="8a5d6-227">Simplifiez l’instruction de l' `GetProduct` action comme `return` suit :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-227">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="8a5d6-228">Configurer le routage</span><span class="sxs-lookup"><span data-stu-id="8a5d6-228">Configure routing</span></span>

<span data-ttu-id="8a5d6-229">Configurez le routage comme suit :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-229">Configure routing as follows:</span></span>

1. <span data-ttu-id="8a5d6-230">Marquez la `ProductsController` classe avec les attributs suivants :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-230">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="8a5d6-231">L' [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribut précédent configure le modèle de routage des attributs du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-231">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="8a5d6-232">L' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut rend l’acheminement des attributs obligatoire pour toutes les actions dans ce contrôleur.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-232">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="8a5d6-233">Le routage d’attributs prend en charge les jetons, tels que `[controller]` et `[action]` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-233">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="8a5d6-234">Lors de l’exécution, chaque jeton est remplacé par le nom du contrôleur ou de l’action, respectivement, auquel l’attribut a été appliqué.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-234">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="8a5d6-235">Les jetons réduisent le nombre de chaînes magiques dans le projet.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-235">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="8a5d6-236">Les jetons garantissent également que les itinéraires restent synchronisés avec les contrôleurs et actions correspondants lorsque les refactorisations de renommage automatique sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-236">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="8a5d6-237">Définissez le mode de compatibilité du projet sur ASP.NET Core 2,2 :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-237">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="8a5d6-238">Modification précédente :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-238">The preceding change:</span></span>

    * <span data-ttu-id="8a5d6-239">Est requis pour utiliser l' `[ApiController]` attribut au niveau du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-239">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="8a5d6-240">Opte pour l’interruption potentielle des comportements introduits dans ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-240">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="8a5d6-241">Activez les demandes HTTP d’extraction aux `ProductController` actions :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-241">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="8a5d6-242">Appliquez l' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut à l' `GetAllProducts` action.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-242">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="8a5d6-243">Appliquez l' `[HttpGet("{id}")]` attribut à l' `GetProduct` action.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-243">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="8a5d6-244">Exécutez le projet migré et accédez à `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-244">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="8a5d6-245">La liste complète des trois produits s’affiche.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-245">A full list of three products appears.</span></span> <span data-ttu-id="8a5d6-246">Accédez à `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-246">Browse to `/api/products/1`.</span></span> <span data-ttu-id="8a5d6-247">Le premier produit s’affiche.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-247">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="8a5d6-248">Shim de compatibilité</span><span class="sxs-lookup"><span data-stu-id="8a5d6-248">Compatibility shim</span></span>

<span data-ttu-id="8a5d6-249">La bibliothèque [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) fournit un shim de compatibilité pour déplacer des projets d’API Web ASP.net 4. x vers ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-249">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="8a5d6-250">Le shim de compatibilité étend ASP.NET Core pour prendre en charge un certain nombre de conventions de l’API Web 2 de ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-250">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="8a5d6-251">L’exemple porté précédemment dans ce document est suffisamment basique pour que le shim de compatibilité soit inutile.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-251">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="8a5d6-252">Pour les projets de grande taille, l’utilisation du shim de compatibilité peut être utile pour combler temporairement le fossé entre les API ASP.NET Core et ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-252">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="8a5d6-253">Le shim de compatibilité de l’API Web est destiné à être utilisé comme mesure temporaire pour prendre en charge la migration de grands projets d’API Web ASP.NET 4. x vers ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-253">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="8a5d6-254">Au fil du temps, les projets doivent être mis à jour pour utiliser des modèles de ASP.NET Core au lieu de s’appuyer sur le shim de compatibilité.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-254">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="8a5d6-255">Les fonctionnalités de compatibilité incluses dans `Microsoft.AspNetCore.Mvc.WebApiCompatShim` sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-255">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="8a5d6-256">Ajoute un `ApiController` type afin que les types de base des contrôleurs n’aient pas besoin d’être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-256">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="8a5d6-257">Active la liaison de modèle de style API Web.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-257">Enables Web API-style model binding.</span></span> <span data-ttu-id="8a5d6-258">ASP.NET Core liaison de modèle MVC fonctionne de façon similaire à celle de ASP.NET 4. x MVC 5, par défaut.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-258">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="8a5d6-259">Le shim de compatibilité modifie la liaison de modèle pour qu’elle soit plus semblable aux conventions de liaison de modèle d’API Web 2 ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-259">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="8a5d6-260">Par exemple, les types complexes sont automatiquement liés à partir du corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="8a5d6-260">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="8a5d6-261">Étend la liaison de modèle afin que les actions du contrôleur puissent prendre des paramètres de type `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-261">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="8a5d6-262">Ajoute des formateurs de message permettant aux actions de retourner des résultats de type `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-262">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="8a5d6-263">Ajoute des méthodes de réponse supplémentaires que les actions de l’API Web 2 peuvent avoir utilisées pour répondre aux réponses :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-263">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="8a5d6-264">`HttpResponseMessage` générateurs</span><span class="sxs-lookup"><span data-stu-id="8a5d6-264">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="8a5d6-265">Méthodes de résultat d’action :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-265">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="8a5d6-266">Ajoute une instance de `IContentNegotiator` au conteneur d’injection de dépendances de l’application et rend disponibles les types liés à la négociation de contenu à partir de [Microsoft. Aspnet. WebApi. client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="8a5d6-266">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="8a5d6-267">Voici quelques exemples de ces types : `DefaultContentNegotiator` et `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-267">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="8a5d6-268">Pour utiliser le shim de compatibilité :</span><span class="sxs-lookup"><span data-stu-id="8a5d6-268">To use the compatibility shim:</span></span>

1. <span data-ttu-id="8a5d6-269">Installez le package NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-269">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="8a5d6-270">Enregistrez les services du shim de compatibilité avec le conteneur DI de l’application en appelant `services.AddMvc().AddWebApiConventions()` dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-270">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="8a5d6-271">Définissez des itinéraires spécifiques à l’API Web à l’aide `MapWebApiRoute` de sur le `IRouteBuilder` dans l’appel de l’application `IApplicationBuilder.UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="8a5d6-271">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a5d6-272">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8a5d6-272">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
