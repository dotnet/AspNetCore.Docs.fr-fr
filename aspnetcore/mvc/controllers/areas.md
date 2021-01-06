---
title: Zones dans ASP.NET Core
author: rick-anderson
description: Découvrez les zones, fonctionnalité d’ASP.NET MVC utilisée pour organiser des fonctionnalités connexes dans un groupe sous la forme d’un espace de noms distinct (pour le routage) et d’une structure de dossiers (pour les vues).
ms.author: riande
ms.date: 03/21/2019
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
uid: mvc/controllers/areas
ms.openlocfilehash: f3d76f612e67aeabf1f7fef694199332c732c593
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878410"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="f6697-103">Zones dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f6697-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="f6697-104">Par [dhananjay Kumar](https://twitter.com/debug_mode) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f6697-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f6697-105">Les zones sont une fonctionnalité ASP.NET qui permet d’organiser les fonctionnalités associées dans un groupe séparément :</span><span class="sxs-lookup"><span data-stu-id="f6697-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="f6697-106">Espace de noms pour le routage.</span><span class="sxs-lookup"><span data-stu-id="f6697-106">Namespace for routing.</span></span>
* <span data-ttu-id="f6697-107">Structure de dossiers pour les affichages et les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="f6697-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="f6697-108">L’utilisation de zones crée une hiérarchie à des fins de routage en ajoutant un autre paramètre de route, `area` , à `controller` et à `action` une Razor page `page` .</span><span class="sxs-lookup"><span data-stu-id="f6697-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="f6697-109">Les zones permettent de partitionner une application Web ASP.NET Core en groupes fonctionnels plus petits, chacun avec son propre ensemble de Razor pages, de contrôleurs, de vues et de modèles.</span><span class="sxs-lookup"><span data-stu-id="f6697-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="f6697-110">Une zone est en réalité une structure au sein d’une application.</span><span class="sxs-lookup"><span data-stu-id="f6697-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="f6697-111">Dans un projet web ASP.NET Core, les composants logiques comme Pages, Controller et View sont conservés dans différents dossiers.</span><span class="sxs-lookup"><span data-stu-id="f6697-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="f6697-112">Le runtime ASP.NET Core utilise des conventions de nommage pour créer la relation entre ces composants.</span><span class="sxs-lookup"><span data-stu-id="f6697-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="f6697-113">Pour une application volumineuse, il peut être avantageux de partitionner l’application en différentes zones de fonctionnalités de premier niveau.</span><span class="sxs-lookup"><span data-stu-id="f6697-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="f6697-114">Par exemple, pour une application d’e-commerce à plusieurs entités, il peut s’agir de la caisse, de la facturation et de la recherche.</span><span class="sxs-lookup"><span data-stu-id="f6697-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="f6697-115">Chacune de ces unités a leur propre zone pour contenir des vues, des contrôleurs, des Razor pages et des modèles.</span><span class="sxs-lookup"><span data-stu-id="f6697-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="f6697-116">Envisagez d’utiliser des zones dans un projet quand :</span><span class="sxs-lookup"><span data-stu-id="f6697-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="f6697-117">L’application est constituée de plusieurs composants fonctionnels globaux qui peuvent être logiquement séparés.</span><span class="sxs-lookup"><span data-stu-id="f6697-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="f6697-118">Vous pouvez partitionner l’application de façon à pouvoir travailler indépendamment sur chaque zone fonctionnelle.</span><span class="sxs-lookup"><span data-stu-id="f6697-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="f6697-119">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f6697-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="f6697-120">L’exemple de code téléchargeable fournit une application de base pour tester les zones.</span><span class="sxs-lookup"><span data-stu-id="f6697-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="f6697-121">Si vous utilisez des Razor pages, consultez [zones avec Razor pages](#areas-with-razor-pages) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="f6697-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="f6697-122">Zones pour contrôleurs avec vues</span><span class="sxs-lookup"><span data-stu-id="f6697-122">Areas for controllers with views</span></span>

<span data-ttu-id="f6697-123">Une application web ASP.NET Core type qui utilise des zones, des contrôleurs et des vues est constituée des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="f6697-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="f6697-124">Une [structure de dossiers Zone](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="f6697-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="f6697-125">Contrôleurs avec l' [`[Area]`](#attribute) attribut pour associer le contrôleur à la zone :</span><span class="sxs-lookup"><span data-stu-id="f6697-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="f6697-126">[Itinéraire de zone ajouté au démarrage](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="f6697-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="f6697-127">Structure de dossiers Zone</span><span class="sxs-lookup"><span data-stu-id="f6697-127">Area folder structure</span></span>

<span data-ttu-id="f6697-128">Imaginez une application qui contient deux groupes logiques, *Produits* et *Services*.</span><span class="sxs-lookup"><span data-stu-id="f6697-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="f6697-129">En utilisant des zones, la structure de dossiers se présenterait comme suit :</span><span class="sxs-lookup"><span data-stu-id="f6697-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="f6697-130">Nom du projet</span><span class="sxs-lookup"><span data-stu-id="f6697-130">Project name</span></span>
  * <span data-ttu-id="f6697-131">Régions</span><span class="sxs-lookup"><span data-stu-id="f6697-131">Areas</span></span>
    * <span data-ttu-id="f6697-132">Produits</span><span class="sxs-lookup"><span data-stu-id="f6697-132">Products</span></span>
      * <span data-ttu-id="f6697-133">Controllers</span><span class="sxs-lookup"><span data-stu-id="f6697-133">Controllers</span></span>
        * <span data-ttu-id="f6697-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="f6697-134">HomeController.cs</span></span>
        * <span data-ttu-id="f6697-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="f6697-135">ManageController.cs</span></span>
      * <span data-ttu-id="f6697-136">Les vues</span><span class="sxs-lookup"><span data-stu-id="f6697-136">Views</span></span>
        * <span data-ttu-id="f6697-137">Accueil</span><span class="sxs-lookup"><span data-stu-id="f6697-137">Home</span></span>
          * <span data-ttu-id="f6697-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="f6697-138">Index.cshtml</span></span>
        * <span data-ttu-id="f6697-139">Gérer</span><span class="sxs-lookup"><span data-stu-id="f6697-139">Manage</span></span>
          * <span data-ttu-id="f6697-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="f6697-140">Index.cshtml</span></span>
          * <span data-ttu-id="f6697-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="f6697-141">About.cshtml</span></span>
    * <span data-ttu-id="f6697-142">Services</span><span class="sxs-lookup"><span data-stu-id="f6697-142">Services</span></span>
      * <span data-ttu-id="f6697-143">Controllers</span><span class="sxs-lookup"><span data-stu-id="f6697-143">Controllers</span></span>
        * <span data-ttu-id="f6697-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="f6697-144">HomeController.cs</span></span>
      * <span data-ttu-id="f6697-145">Les vues</span><span class="sxs-lookup"><span data-stu-id="f6697-145">Views</span></span>
        * <span data-ttu-id="f6697-146">Accueil</span><span class="sxs-lookup"><span data-stu-id="f6697-146">Home</span></span>
          * <span data-ttu-id="f6697-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="f6697-147">Index.cshtml</span></span>

<span data-ttu-id="f6697-148">Si la disposition précédente est classique dans les cas où des zones sont utilisées, seuls les fichiers de vues sont nécessaires pour utiliser cette structure de dossiers.</span><span class="sxs-lookup"><span data-stu-id="f6697-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="f6697-149">La détection de vues recherche un fichier de vue de zone correspondant dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="f6697-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="f6697-150">Associer le contrôleur à une zone</span><span class="sxs-lookup"><span data-stu-id="f6697-150">Associate the controller with an Area</span></span>

<span data-ttu-id="f6697-151">Les contrôleurs de zone sont désignés avec l’attribut [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="f6697-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="f6697-152">Ajouter une route de zone</span><span class="sxs-lookup"><span data-stu-id="f6697-152">Add Area route</span></span>

<span data-ttu-id="f6697-153">Les itinéraires de zone utilisent généralement le  [routage conventionnel](xref:mvc/controllers/routing#cr) plutôt que le [routage d’attributs](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="f6697-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="f6697-154">Le routage conventionnel est dépendant de l’ordre.</span><span class="sxs-lookup"><span data-stu-id="f6697-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="f6697-155">En général, les routes avec des zones doivent être placées plus haut dans la table de routage, car elles sont plus spécifiques que les routes sans zone.</span><span class="sxs-lookup"><span data-stu-id="f6697-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="f6697-156">`{area:...}` peut être utilisé comme jeton dans les modèles de route si l’espace d’url est uniforme dans toutes les zones :</span><span class="sxs-lookup"><span data-stu-id="f6697-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="f6697-157">Dans le code précédent, `exists` applique une contrainte qui impose à la route de correspondre à une zone.</span><span class="sxs-lookup"><span data-stu-id="f6697-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="f6697-158">Utilisation `{area:...}` de avec `MapControllerRoute` :</span><span class="sxs-lookup"><span data-stu-id="f6697-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="f6697-159">Est le mécanisme le moins compliqué pour ajouter le routage à des zones.</span><span class="sxs-lookup"><span data-stu-id="f6697-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="f6697-160">Correspond à tous les contrôleurs avec l' `[Area("Area name")]` attribut.</span><span class="sxs-lookup"><span data-stu-id="f6697-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="f6697-161">Le code suivant utilise <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> pour créer deux routes de zone nommées :</span><span class="sxs-lookup"><span data-stu-id="f6697-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="f6697-162">Pour plus d’informations, consultez [Routage de zones](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="f6697-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="f6697-163">Génération de liens avec zones MVC</span><span class="sxs-lookup"><span data-stu-id="f6697-163">Link generation with MVC areas</span></span>

<span data-ttu-id="f6697-164">Le code suivant tiré de l’[exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) illustre une génération de liens avec la zone spécifiée :</span><span class="sxs-lookup"><span data-stu-id="f6697-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="f6697-165">L’exemple de téléchargement inclut une [vue partielle](xref:mvc/views/partial) qui contient les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="f6697-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="f6697-166">Liens précédents.</span><span class="sxs-lookup"><span data-stu-id="f6697-166">The preceding links.</span></span>
* <span data-ttu-id="f6697-167">Les liens similaires à l’exception précédente `area` ne sont pas spécifiés.</span><span class="sxs-lookup"><span data-stu-id="f6697-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="f6697-168">La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés.</span><span class="sxs-lookup"><span data-stu-id="f6697-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="f6697-169">Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone et le même contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f6697-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="f6697-170">Quand la zone ou le contrôleur n’est pas spécifié, le routage dépend des valeurs [ambiantes](xref:mvc/controllers/routing#ambient).</span><span class="sxs-lookup"><span data-stu-id="f6697-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="f6697-171">Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens.</span><span class="sxs-lookup"><span data-stu-id="f6697-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="f6697-172">Dans de nombreux cas pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects avec le balisage qui ne spécifie pas la zone.</span><span class="sxs-lookup"><span data-stu-id="f6697-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="f6697-173">Pour plus d’informations, consultez [Routage vers des actions de contrôleur](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="f6697-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="f6697-174">Disposition partagée pour les zones en utilisant le fichier _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="f6697-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="f6697-175">Pour partager une disposition commune pour l’ensemble de l’application, conservez le *_ViewStart. cshtml* dans le [dossier racine](#arf)de l’application.</span><span class="sxs-lookup"><span data-stu-id="f6697-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="f6697-176">Pour plus d'informations, consultez <xref:mvc/views/layout></span><span class="sxs-lookup"><span data-stu-id="f6697-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="f6697-177">Dossier racine de l’application</span><span class="sxs-lookup"><span data-stu-id="f6697-177">Application root folder</span></span>

<span data-ttu-id="f6697-178">Le dossier racine de l’application est le dossier qui contient *Startup.cs* dans l’application Web créée avec les modèles ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="f6697-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="f6697-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="f6697-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="f6697-180">*/Views/_ViewImports. cshtml*, pour MVC et */pages/_ViewImports. cshtml* pour les Razor pages, n’est pas importé dans les vues dans les zones.</span><span class="sxs-lookup"><span data-stu-id="f6697-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="f6697-181">Utilisez l’une des approches suivantes pour fournir des importations d’affichage à tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="f6697-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="f6697-182">Ajoutez *_ViewImports. cshtml* au [dossier racine](#arf)de l’application.</span><span class="sxs-lookup"><span data-stu-id="f6697-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="f6697-183">Un *_ViewImports. cshtml* dans le dossier racine de l’application s’applique à toutes les vues de l’application.</span><span class="sxs-lookup"><span data-stu-id="f6697-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="f6697-184">Copiez le fichier *_ViewImports. cshtml* dans le dossier d’affichage approprié sous zones.</span><span class="sxs-lookup"><span data-stu-id="f6697-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="f6697-185">Le fichier *_ViewImports. cshtml* [contient généralement](xref:mvc/views/tag-helpers/intro) les instructions Imports, `@using` et `@inject` .</span><span class="sxs-lookup"><span data-stu-id="f6697-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="f6697-186">Pour plus d’informations, consultez [importation de directives partagées](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="f6697-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="f6697-187">Changer le dossier de zone par défaut où sont stockées les vues</span><span class="sxs-lookup"><span data-stu-id="f6697-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="f6697-188">Le code suivant remplace le dossier de zone par défaut `"Areas"` par `"MyAreas"` :</span><span class="sxs-lookup"><span data-stu-id="f6697-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="f6697-189">Zones avec Razor pages</span><span class="sxs-lookup"><span data-stu-id="f6697-189">Areas with Razor Pages</span></span>

<span data-ttu-id="f6697-190">Les zones avec Razor pages requièrent un `Areas/<area name>/Pages` dossier à la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="f6697-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="f6697-191">La structure de dossiers suivante est utilisée avec [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) :</span><span class="sxs-lookup"><span data-stu-id="f6697-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="f6697-192">Nom du projet</span><span class="sxs-lookup"><span data-stu-id="f6697-192">Project name</span></span>
  * <span data-ttu-id="f6697-193">Régions</span><span class="sxs-lookup"><span data-stu-id="f6697-193">Areas</span></span>
    * <span data-ttu-id="f6697-194">Produits</span><span class="sxs-lookup"><span data-stu-id="f6697-194">Products</span></span>
      * <span data-ttu-id="f6697-195">Pages</span><span class="sxs-lookup"><span data-stu-id="f6697-195">Pages</span></span>
        * <span data-ttu-id="f6697-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="f6697-196">_ViewImports</span></span>
        * <span data-ttu-id="f6697-197">À propos de</span><span class="sxs-lookup"><span data-stu-id="f6697-197">About</span></span>
        * <span data-ttu-id="f6697-198">Index</span><span class="sxs-lookup"><span data-stu-id="f6697-198">Index</span></span>
    * <span data-ttu-id="f6697-199">Services</span><span class="sxs-lookup"><span data-stu-id="f6697-199">Services</span></span>
      * <span data-ttu-id="f6697-200">Pages</span><span class="sxs-lookup"><span data-stu-id="f6697-200">Pages</span></span>
        * <span data-ttu-id="f6697-201">Gérer</span><span class="sxs-lookup"><span data-stu-id="f6697-201">Manage</span></span>
          * <span data-ttu-id="f6697-202">À propos de</span><span class="sxs-lookup"><span data-stu-id="f6697-202">About</span></span>
          * <span data-ttu-id="f6697-203">Index</span><span class="sxs-lookup"><span data-stu-id="f6697-203">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="f6697-204">Génération de liens avec des Razor pages et des zones</span><span class="sxs-lookup"><span data-stu-id="f6697-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="f6697-205">Le code suivant tiré de [l’exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) illustre une génération de liens avec la zone spécifiée (par exemple, `asp-area="Products"`) :</span><span class="sxs-lookup"><span data-stu-id="f6697-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="f6697-206">L’exemple de code téléchargeable comprend une [vue partielle](xref:mvc/views/partial) qui contient les liens précédents et les mêmes liens sans spécification de la zone.</span><span class="sxs-lookup"><span data-stu-id="f6697-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="f6697-207">La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés.</span><span class="sxs-lookup"><span data-stu-id="f6697-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="f6697-208">Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone.</span><span class="sxs-lookup"><span data-stu-id="f6697-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="f6697-209">Quand la zone n’est pas spécifiée, le routage dépend des valeurs *ambiantes*.</span><span class="sxs-lookup"><span data-stu-id="f6697-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="f6697-210">Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens.</span><span class="sxs-lookup"><span data-stu-id="f6697-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="f6697-211">Dans de nombreux cas, pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects.</span><span class="sxs-lookup"><span data-stu-id="f6697-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="f6697-212">Prenons l’exemple des liens générés à partir de l’extrait de code suivant :</span><span class="sxs-lookup"><span data-stu-id="f6697-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="f6697-213">Pour le code précédent :</span><span class="sxs-lookup"><span data-stu-id="f6697-213">For the preceding code:</span></span>

* <span data-ttu-id="f6697-214">Le lien généré à partir de `<a asp-page="/Manage/About">` est correct uniquement lorsque la dernière requête concernait une page dans la zone `Services`.</span><span class="sxs-lookup"><span data-stu-id="f6697-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="f6697-215">Par exemple, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="f6697-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="f6697-216">Le lien généré à partir de `<a asp-page="/About">` est correct uniquement lorsque la dernière requête concernait une page dans `/Home`.</span><span class="sxs-lookup"><span data-stu-id="f6697-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="f6697-217">Le code est issu du [téléchargement de l’exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="f6697-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="f6697-218">Importer l’espace de noms et les Tag Helpers avec le fichier _ViewImports</span><span class="sxs-lookup"><span data-stu-id="f6697-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="f6697-219">Un fichier *_ViewImports. cshtml* peut être ajouté à chaque dossier des *pages* de zone pour importer l’espace de noms et les tag helpers sur chaque Razor page du dossier.</span><span class="sxs-lookup"><span data-stu-id="f6697-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="f6697-220">Considérez la zone *Services* de l’exemple de code, qui ne contient pas de fichier *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f6697-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="f6697-221">La balise suivante affiche la page */services/Manage/about* Razor :</span><span class="sxs-lookup"><span data-stu-id="f6697-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="f6697-222">Dans le balisage précédent :</span><span class="sxs-lookup"><span data-stu-id="f6697-222">In the preceding markup:</span></span>

* <span data-ttu-id="f6697-223">Le nom de classe complet doit être utilisé pour spécifier le modèle ( `@model RPareas.Areas.Services.Pages.Manage.AboutModel` ).</span><span class="sxs-lookup"><span data-stu-id="f6697-223">The fully qualified class name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="f6697-224">Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) sont activés par `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="f6697-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="f6697-225">Dans l’exemple de téléchargement, la zone Products contient le fichier *_ViewImports.cshtml* suivant :</span><span class="sxs-lookup"><span data-stu-id="f6697-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="f6697-226">La balise suivante affiche la page */Products/about* Razor :</span><span class="sxs-lookup"><span data-stu-id="f6697-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="f6697-227">Dans le fichier précédent, l’espace de noms et la `@addTagHelper` directive sont importés dans le fichier par le fichier *Areas/Products/Pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f6697-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="f6697-228">Pour plus d’informations, consultez [Gestion de l’étendue des Tag Helpers](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) et [Importation de directives partagées](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="f6697-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="f6697-229">Disposition partagée pour les Razor zones de pages</span><span class="sxs-lookup"><span data-stu-id="f6697-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="f6697-230">Pour partager une disposition commune pour l’ensemble de l’application, déplacez *_ViewStart.cshtml* dans le dossier racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="f6697-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="f6697-231">Zones de publication</span><span class="sxs-lookup"><span data-stu-id="f6697-231">Publishing Areas</span></span>

<span data-ttu-id="f6697-232">Tous les fichiers \*.cshtml et autres fichiers dans le répertoire *wwwroot* sont publiés en sortie quand `<Project Sdk="Microsoft.NET.Sdk.Web">` est inclus dans le fichier \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="f6697-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f6697-233">Les zones sont une fonctionnalité d’ASP.NET qui servent à organiser les fonctionnalités associées dans un groupe sous la forme d’un espace de noms (pour le routage) et d’une structure de dossiers (pour les vues) distincts.</span><span class="sxs-lookup"><span data-stu-id="f6697-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="f6697-234">L’utilisation de zones crée une hiérarchie à des fins de routage en ajoutant un autre paramètre de route, `area` , à `controller` et à `action` une Razor page `page` .</span><span class="sxs-lookup"><span data-stu-id="f6697-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="f6697-235">Les zones permettent de partitionner une application Web ASP.NET Core en groupes fonctionnels plus petits, chacun avec son propre ensemble de Razor pages, de contrôleurs, de vues et de modèles.</span><span class="sxs-lookup"><span data-stu-id="f6697-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="f6697-236">Une zone est en réalité une structure au sein d’une application.</span><span class="sxs-lookup"><span data-stu-id="f6697-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="f6697-237">Dans un projet web ASP.NET Core, les composants logiques comme Pages, Controller et View sont conservés dans différents dossiers.</span><span class="sxs-lookup"><span data-stu-id="f6697-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="f6697-238">Le runtime ASP.NET Core utilise des conventions de nommage pour créer la relation entre ces composants.</span><span class="sxs-lookup"><span data-stu-id="f6697-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="f6697-239">Pour une application volumineuse, il peut être avantageux de partitionner l’application en différentes zones de fonctionnalités de premier niveau.</span><span class="sxs-lookup"><span data-stu-id="f6697-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="f6697-240">Par exemple, pour une application d’e-commerce à plusieurs entités, il peut s’agir de la caisse, de la facturation et de la recherche.</span><span class="sxs-lookup"><span data-stu-id="f6697-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="f6697-241">Chacune de ces unités a leur propre zone pour contenir des vues, des contrôleurs, des Razor pages et des modèles.</span><span class="sxs-lookup"><span data-stu-id="f6697-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="f6697-242">Envisagez d’utiliser des zones dans un projet quand :</span><span class="sxs-lookup"><span data-stu-id="f6697-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="f6697-243">L’application est constituée de plusieurs composants fonctionnels globaux qui peuvent être logiquement séparés.</span><span class="sxs-lookup"><span data-stu-id="f6697-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="f6697-244">Vous pouvez partitionner l’application de façon à pouvoir travailler indépendamment sur chaque zone fonctionnelle.</span><span class="sxs-lookup"><span data-stu-id="f6697-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="f6697-245">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f6697-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="f6697-246">L’exemple de code téléchargeable fournit une application de base pour tester les zones.</span><span class="sxs-lookup"><span data-stu-id="f6697-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="f6697-247">Si vous utilisez des Razor pages, consultez [zones avec Razor pages](#areas-with-razor-pages) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="f6697-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="f6697-248">Zones pour contrôleurs avec vues</span><span class="sxs-lookup"><span data-stu-id="f6697-248">Areas for controllers with views</span></span>

<span data-ttu-id="f6697-249">Une application web ASP.NET Core type qui utilise des zones, des contrôleurs et des vues est constituée des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="f6697-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="f6697-250">Une [structure de dossiers Zone](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="f6697-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="f6697-251">Contrôleurs avec l' [`[Area]`](#attribute) attribut pour associer le contrôleur à la zone :</span><span class="sxs-lookup"><span data-stu-id="f6697-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="f6697-252">[Itinéraire de zone ajouté au démarrage](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="f6697-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="f6697-253">Structure de dossiers Zone</span><span class="sxs-lookup"><span data-stu-id="f6697-253">Area folder structure</span></span>

<span data-ttu-id="f6697-254">Imaginez une application qui contient deux groupes logiques, *Produits* et *Services*.</span><span class="sxs-lookup"><span data-stu-id="f6697-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="f6697-255">En utilisant des zones, la structure de dossiers se présenterait comme suit :</span><span class="sxs-lookup"><span data-stu-id="f6697-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="f6697-256">Nom du projet</span><span class="sxs-lookup"><span data-stu-id="f6697-256">Project name</span></span>
  * <span data-ttu-id="f6697-257">Régions</span><span class="sxs-lookup"><span data-stu-id="f6697-257">Areas</span></span>
    * <span data-ttu-id="f6697-258">Produits</span><span class="sxs-lookup"><span data-stu-id="f6697-258">Products</span></span>
      * <span data-ttu-id="f6697-259">Controllers</span><span class="sxs-lookup"><span data-stu-id="f6697-259">Controllers</span></span>
        * <span data-ttu-id="f6697-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="f6697-260">HomeController.cs</span></span>
        * <span data-ttu-id="f6697-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="f6697-261">ManageController.cs</span></span>
      * <span data-ttu-id="f6697-262">Les vues</span><span class="sxs-lookup"><span data-stu-id="f6697-262">Views</span></span>
        * <span data-ttu-id="f6697-263">Accueil</span><span class="sxs-lookup"><span data-stu-id="f6697-263">Home</span></span>
          * <span data-ttu-id="f6697-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="f6697-264">Index.cshtml</span></span>
        * <span data-ttu-id="f6697-265">Gérer</span><span class="sxs-lookup"><span data-stu-id="f6697-265">Manage</span></span>
          * <span data-ttu-id="f6697-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="f6697-266">Index.cshtml</span></span>
          * <span data-ttu-id="f6697-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="f6697-267">About.cshtml</span></span>
    * <span data-ttu-id="f6697-268">Services</span><span class="sxs-lookup"><span data-stu-id="f6697-268">Services</span></span>
      * <span data-ttu-id="f6697-269">Controllers</span><span class="sxs-lookup"><span data-stu-id="f6697-269">Controllers</span></span>
        * <span data-ttu-id="f6697-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="f6697-270">HomeController.cs</span></span>
      * <span data-ttu-id="f6697-271">Les vues</span><span class="sxs-lookup"><span data-stu-id="f6697-271">Views</span></span>
        * <span data-ttu-id="f6697-272">Accueil</span><span class="sxs-lookup"><span data-stu-id="f6697-272">Home</span></span>
          * <span data-ttu-id="f6697-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="f6697-273">Index.cshtml</span></span>

<span data-ttu-id="f6697-274">Si la disposition précédente est classique dans les cas où des zones sont utilisées, seuls les fichiers de vues sont nécessaires pour utiliser cette structure de dossiers.</span><span class="sxs-lookup"><span data-stu-id="f6697-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="f6697-275">La détection de vues recherche un fichier de vue de zone correspondant dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="f6697-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="f6697-276">Associer le contrôleur à une zone</span><span class="sxs-lookup"><span data-stu-id="f6697-276">Associate the controller with an Area</span></span>

<span data-ttu-id="f6697-277">Les contrôleurs de zone sont désignés avec l’attribut [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="f6697-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="f6697-278">Ajouter une route de zone</span><span class="sxs-lookup"><span data-stu-id="f6697-278">Add Area route</span></span>

<span data-ttu-id="f6697-279">Les routes de zones utilisent généralement un routage conventionnel plutôt qu’un routage par attributs.</span><span class="sxs-lookup"><span data-stu-id="f6697-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="f6697-280">Le routage conventionnel est dépendant de l’ordre.</span><span class="sxs-lookup"><span data-stu-id="f6697-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="f6697-281">En général, les routes avec des zones doivent être placées plus haut dans la table de routage, car elles sont plus spécifiques que les routes sans zone.</span><span class="sxs-lookup"><span data-stu-id="f6697-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="f6697-282">`{area:...}` peut être utilisé comme jeton dans les modèles de route si l’espace d’url est uniforme dans toutes les zones :</span><span class="sxs-lookup"><span data-stu-id="f6697-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="f6697-283">Dans le code précédent, `exists` applique une contrainte qui impose à la route de correspondre à une zone.</span><span class="sxs-lookup"><span data-stu-id="f6697-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="f6697-284">Pour ajouter un routage à des zones, le mécanisme le moins compliqué consiste à utiliser `{area:...}`.</span><span class="sxs-lookup"><span data-stu-id="f6697-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="f6697-285">Le code suivant utilise <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> pour créer deux routes de zone nommées :</span><span class="sxs-lookup"><span data-stu-id="f6697-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="f6697-286">Si vous utilisez `MapAreaRoute` avec ASP.NET Core 2.2, prenez connaissance de [ce problème sur GitHub](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="f6697-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="f6697-287">Pour plus d’informations, consultez [Routage de zones](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="f6697-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="f6697-288">Génération de liens avec zones MVC</span><span class="sxs-lookup"><span data-stu-id="f6697-288">Link generation with MVC areas</span></span>

<span data-ttu-id="f6697-289">Le code suivant tiré de l’[exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) illustre une génération de liens avec la zone spécifiée :</span><span class="sxs-lookup"><span data-stu-id="f6697-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="f6697-290">Les liens générés par le code précédent sont valides où que ce soit dans l’application.</span><span class="sxs-lookup"><span data-stu-id="f6697-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="f6697-291">L’exemple de code téléchargeable comprend une [vue partielle](xref:mvc/views/partial) qui contient les liens précédents et les mêmes liens sans spécification de la zone.</span><span class="sxs-lookup"><span data-stu-id="f6697-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="f6697-292">La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés.</span><span class="sxs-lookup"><span data-stu-id="f6697-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="f6697-293">Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone et le même contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f6697-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="f6697-294">Quand la zone ou le contrôleur n’est pas spécifié, le routage dépend des valeurs *ambiantes*.</span><span class="sxs-lookup"><span data-stu-id="f6697-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="f6697-295">Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens.</span><span class="sxs-lookup"><span data-stu-id="f6697-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="f6697-296">Dans de nombreux cas, pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects.</span><span class="sxs-lookup"><span data-stu-id="f6697-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="f6697-297">Pour plus d’informations, consultez [Routage vers des actions de contrôleur](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="f6697-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="f6697-298">Disposition partagée pour les zones en utilisant le fichier _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="f6697-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="f6697-299">Pour partager une disposition commune pour l’ensemble de l’application, déplacez *_ViewStart.cshtml* dans le dossier racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="f6697-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="f6697-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="f6697-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="f6697-301">Dans son emplacement standard, */Views/_ViewImports.cshtml* ne s’applique pas aux zones.</span><span class="sxs-lookup"><span data-stu-id="f6697-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="f6697-302">Pour utiliser les [balises](xref:mvc/views/tag-helpers/intro)courantes, `@using` ou `@inject` dans votre zone, assurez-vous qu’un fichier *_ViewImports. cshtml* approprié [s’applique à vos vues de zone](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="f6697-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="f6697-303">Si vous souhaitez obtenir le même comportement dans toutes vos vues, déplacez */Views/_ViewImports.cshtml* vers la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="f6697-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="f6697-304">Changer le dossier de zone par défaut où sont stockées les vues</span><span class="sxs-lookup"><span data-stu-id="f6697-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="f6697-305">Le code suivant remplace le dossier de zone par défaut `"Areas"` par `"MyAreas"` :</span><span class="sxs-lookup"><span data-stu-id="f6697-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="f6697-306">Zones avec Razor pages</span><span class="sxs-lookup"><span data-stu-id="f6697-306">Areas with Razor Pages</span></span>

<span data-ttu-id="f6697-307">Les zones avec Razor pages requièrent un `Areas/<area name>/Pages` dossier à la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="f6697-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="f6697-308">La structure de dossiers suivante est utilisée avec [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) :</span><span class="sxs-lookup"><span data-stu-id="f6697-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="f6697-309">Nom du projet</span><span class="sxs-lookup"><span data-stu-id="f6697-309">Project name</span></span>
  * <span data-ttu-id="f6697-310">Régions</span><span class="sxs-lookup"><span data-stu-id="f6697-310">Areas</span></span>
    * <span data-ttu-id="f6697-311">Produits</span><span class="sxs-lookup"><span data-stu-id="f6697-311">Products</span></span>
      * <span data-ttu-id="f6697-312">Pages</span><span class="sxs-lookup"><span data-stu-id="f6697-312">Pages</span></span>
        * <span data-ttu-id="f6697-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="f6697-313">_ViewImports</span></span>
        * <span data-ttu-id="f6697-314">À propos de</span><span class="sxs-lookup"><span data-stu-id="f6697-314">About</span></span>
        * <span data-ttu-id="f6697-315">Index</span><span class="sxs-lookup"><span data-stu-id="f6697-315">Index</span></span>
    * <span data-ttu-id="f6697-316">Services</span><span class="sxs-lookup"><span data-stu-id="f6697-316">Services</span></span>
      * <span data-ttu-id="f6697-317">Pages</span><span class="sxs-lookup"><span data-stu-id="f6697-317">Pages</span></span>
        * <span data-ttu-id="f6697-318">Gérer</span><span class="sxs-lookup"><span data-stu-id="f6697-318">Manage</span></span>
          * <span data-ttu-id="f6697-319">À propos de</span><span class="sxs-lookup"><span data-stu-id="f6697-319">About</span></span>
          * <span data-ttu-id="f6697-320">Index</span><span class="sxs-lookup"><span data-stu-id="f6697-320">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="f6697-321">Génération de liens avec des Razor pages et des zones</span><span class="sxs-lookup"><span data-stu-id="f6697-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="f6697-322">Le code suivant tiré de [l’exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) illustre une génération de liens avec la zone spécifiée (par exemple, `asp-area="Products"`) :</span><span class="sxs-lookup"><span data-stu-id="f6697-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="f6697-323">Les liens générés par le code précédent sont valides où que ce soit dans l’application.</span><span class="sxs-lookup"><span data-stu-id="f6697-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="f6697-324">L’exemple de code téléchargeable comprend une [vue partielle](xref:mvc/views/partial) qui contient les liens précédents et les mêmes liens sans spécification de la zone.</span><span class="sxs-lookup"><span data-stu-id="f6697-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="f6697-325">La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés.</span><span class="sxs-lookup"><span data-stu-id="f6697-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="f6697-326">Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone.</span><span class="sxs-lookup"><span data-stu-id="f6697-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="f6697-327">Quand la zone n’est pas spécifiée, le routage dépend des valeurs *ambiantes*.</span><span class="sxs-lookup"><span data-stu-id="f6697-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="f6697-328">Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens.</span><span class="sxs-lookup"><span data-stu-id="f6697-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="f6697-329">Dans de nombreux cas, pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects.</span><span class="sxs-lookup"><span data-stu-id="f6697-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="f6697-330">Prenons l’exemple des liens générés à partir de l’extrait de code suivant :</span><span class="sxs-lookup"><span data-stu-id="f6697-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="f6697-331">Pour le code précédent :</span><span class="sxs-lookup"><span data-stu-id="f6697-331">For the preceding code:</span></span>

* <span data-ttu-id="f6697-332">Le lien généré à partir de `<a asp-page="/Manage/About">` est correct uniquement lorsque la dernière requête concernait une page dans la zone `Services`.</span><span class="sxs-lookup"><span data-stu-id="f6697-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="f6697-333">Par exemple, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="f6697-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="f6697-334">Le lien généré à partir de `<a asp-page="/About">` est correct uniquement lorsque la dernière requête concernait une page dans `/Home`.</span><span class="sxs-lookup"><span data-stu-id="f6697-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="f6697-335">Le code est issu du [téléchargement de l’exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="f6697-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="f6697-336">Importer l’espace de noms et les Tag Helpers avec le fichier _ViewImports</span><span class="sxs-lookup"><span data-stu-id="f6697-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="f6697-337">Un fichier *_ViewImports. cshtml* peut être ajouté à chaque dossier des *pages* de zone pour importer l’espace de noms et les tag helpers sur chaque Razor page du dossier.</span><span class="sxs-lookup"><span data-stu-id="f6697-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="f6697-338">Considérez la zone *Services* de l’exemple de code, qui ne contient pas de fichier *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f6697-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="f6697-339">La balise suivante affiche la page */services/Manage/about* Razor :</span><span class="sxs-lookup"><span data-stu-id="f6697-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="f6697-340">Dans le balisage précédent :</span><span class="sxs-lookup"><span data-stu-id="f6697-340">In the preceding markup:</span></span>

* <span data-ttu-id="f6697-341">Le nom de domaine complet doit être utilisé pour spécifier le modèle (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="f6697-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="f6697-342">Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) sont activés par `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="f6697-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="f6697-343">Dans l’exemple de téléchargement, la zone Products contient le fichier *_ViewImports.cshtml* suivant :</span><span class="sxs-lookup"><span data-stu-id="f6697-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="f6697-344">La balise suivante affiche la page */Products/about* Razor :</span><span class="sxs-lookup"><span data-stu-id="f6697-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="f6697-345">Dans le fichier précédent, l’espace de noms et la `@addTagHelper` directive sont importés dans le fichier par le fichier *Areas/Products/Pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f6697-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="f6697-346">Pour plus d’informations, consultez [Gestion de l’étendue des Tag Helpers](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) et [Importation de directives partagées](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="f6697-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="f6697-347">Disposition partagée pour les Razor zones de pages</span><span class="sxs-lookup"><span data-stu-id="f6697-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="f6697-348">Pour partager une disposition commune pour l’ensemble de l’application, déplacez *_ViewStart.cshtml* dans le dossier racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="f6697-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="f6697-349">Zones de publication</span><span class="sxs-lookup"><span data-stu-id="f6697-349">Publishing Areas</span></span>

<span data-ttu-id="f6697-350">Tous les fichiers \*.cshtml et autres fichiers dans le répertoire *wwwroot* sont publiés en sortie quand `<Project Sdk="Microsoft.NET.Sdk.Web">` est inclus dans le fichier \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="f6697-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
