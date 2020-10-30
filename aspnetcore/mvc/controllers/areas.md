---
title: Zones dans ASP.NET Core
author: rick-anderson
description: Découvrez les zones, fonctionnalité d’ASP.NET MVC utilisée pour organiser des fonctionnalités connexes dans un groupe sous la forme d’un espace de noms distinct (pour le routage) et d’une structure de dossiers (pour les vues).
ms.author: riande
ms.date: 03/21/2019
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
uid: mvc/controllers/areas
ms.openlocfilehash: 42eec406813adce4d7edbc1ab66a1f689c4aca0e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053525"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="4dea9-103">Zones dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4dea9-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="4dea9-104">Par [dhananjay Kumar](https://twitter.com/debug_mode) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4dea9-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4dea9-105">Les zones sont une fonctionnalité ASP.NET qui permet d’organiser les fonctionnalités associées dans un groupe séparément :</span><span class="sxs-lookup"><span data-stu-id="4dea9-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="4dea9-106">Espace de noms pour le routage.</span><span class="sxs-lookup"><span data-stu-id="4dea9-106">Namespace for routing.</span></span>
* <span data-ttu-id="4dea9-107">Structure de dossiers pour les affichages et les :::no-loc(Razor)::: pages.</span><span class="sxs-lookup"><span data-stu-id="4dea9-107">Folder structure for views and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="4dea9-108">L’utilisation de zones crée une hiérarchie à des fins de routage en ajoutant un autre paramètre de route, `area` , à `controller` et à `action` une :::no-loc(Razor)::: page `page` .</span><span class="sxs-lookup"><span data-stu-id="4dea9-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="4dea9-109">Les zones permettent de partitionner une application Web ASP.NET Core en groupes fonctionnels plus petits, chacun avec son propre ensemble de :::no-loc(Razor)::: pages, de contrôleurs, de vues et de modèles.</span><span class="sxs-lookup"><span data-stu-id="4dea9-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="4dea9-110">Une zone est en réalité une structure au sein d’une application.</span><span class="sxs-lookup"><span data-stu-id="4dea9-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="4dea9-111">Dans un projet web ASP.NET Core, les composants logiques comme Pages, Controller et View sont conservés dans différents dossiers.</span><span class="sxs-lookup"><span data-stu-id="4dea9-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="4dea9-112">Le runtime ASP.NET Core utilise des conventions de nommage pour créer la relation entre ces composants.</span><span class="sxs-lookup"><span data-stu-id="4dea9-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="4dea9-113">Pour une application volumineuse, il peut être avantageux de partitionner l’application en différentes zones de fonctionnalités de premier niveau.</span><span class="sxs-lookup"><span data-stu-id="4dea9-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="4dea9-114">Par exemple, pour une application d’e-commerce à plusieurs entités, il peut s’agir de la caisse, de la facturation et de la recherche.</span><span class="sxs-lookup"><span data-stu-id="4dea9-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="4dea9-115">Chacune de ces unités a leur propre zone pour contenir des vues, des contrôleurs, des :::no-loc(Razor)::: pages et des modèles.</span><span class="sxs-lookup"><span data-stu-id="4dea9-115">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="4dea9-116">Envisagez d’utiliser des zones dans un projet quand :</span><span class="sxs-lookup"><span data-stu-id="4dea9-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="4dea9-117">L’application est constituée de plusieurs composants fonctionnels globaux qui peuvent être logiquement séparés.</span><span class="sxs-lookup"><span data-stu-id="4dea9-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="4dea9-118">Vous pouvez partitionner l’application de façon à pouvoir travailler indépendamment sur chaque zone fonctionnelle.</span><span class="sxs-lookup"><span data-stu-id="4dea9-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="4dea9-119">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4dea9-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="4dea9-120">L’exemple de code téléchargeable fournit une application de base pour tester les zones.</span><span class="sxs-lookup"><span data-stu-id="4dea9-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="4dea9-121">Si vous utilisez des :::no-loc(Razor)::: pages, consultez [zones avec :::no-loc(Razor)::: pages](#areas-with-razor-pages) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="4dea9-121">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="4dea9-122">Zones pour contrôleurs avec vues</span><span class="sxs-lookup"><span data-stu-id="4dea9-122">Areas for controllers with views</span></span>

<span data-ttu-id="4dea9-123">Une application web ASP.NET Core type qui utilise des zones, des contrôleurs et des vues est constituée des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="4dea9-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="4dea9-124">Une [structure de dossiers Zone](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="4dea9-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="4dea9-125">Contrôleurs avec l' [`[Area]`](#attribute) attribut pour associer le contrôleur à la zone :</span><span class="sxs-lookup"><span data-stu-id="4dea9-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="4dea9-126">[Itinéraire de zone ajouté au démarrage](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="4dea9-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="4dea9-127">Structure de dossiers Zone</span><span class="sxs-lookup"><span data-stu-id="4dea9-127">Area folder structure</span></span>

<span data-ttu-id="4dea9-128">Imaginez une application qui contient deux groupes logiques, *Produits* et *Services* .</span><span class="sxs-lookup"><span data-stu-id="4dea9-128">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="4dea9-129">En utilisant des zones, la structure de dossiers se présenterait comme suit :</span><span class="sxs-lookup"><span data-stu-id="4dea9-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="4dea9-130">Nom du projet</span><span class="sxs-lookup"><span data-stu-id="4dea9-130">Project name</span></span>
  * <span data-ttu-id="4dea9-131">Zones (Areas)</span><span class="sxs-lookup"><span data-stu-id="4dea9-131">Areas</span></span>
    * <span data-ttu-id="4dea9-132">Produits</span><span class="sxs-lookup"><span data-stu-id="4dea9-132">Products</span></span>
      * <span data-ttu-id="4dea9-133">Contrôleurs</span><span class="sxs-lookup"><span data-stu-id="4dea9-133">Controllers</span></span>
        * <span data-ttu-id="4dea9-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="4dea9-134">HomeController.cs</span></span>
        * <span data-ttu-id="4dea9-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="4dea9-135">ManageController.cs</span></span>
      * <span data-ttu-id="4dea9-136">Les vues</span><span class="sxs-lookup"><span data-stu-id="4dea9-136">Views</span></span>
        * <span data-ttu-id="4dea9-137">Accueil</span><span class="sxs-lookup"><span data-stu-id="4dea9-137">Home</span></span>
          * <span data-ttu-id="4dea9-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4dea9-138">Index.cshtml</span></span>
        * <span data-ttu-id="4dea9-139">Gérer</span><span class="sxs-lookup"><span data-stu-id="4dea9-139">Manage</span></span>
          * <span data-ttu-id="4dea9-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4dea9-140">Index.cshtml</span></span>
          * <span data-ttu-id="4dea9-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="4dea9-141">About.cshtml</span></span>
    * <span data-ttu-id="4dea9-142">Services</span><span class="sxs-lookup"><span data-stu-id="4dea9-142">Services</span></span>
      * <span data-ttu-id="4dea9-143">Contrôleurs</span><span class="sxs-lookup"><span data-stu-id="4dea9-143">Controllers</span></span>
        * <span data-ttu-id="4dea9-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="4dea9-144">HomeController.cs</span></span>
      * <span data-ttu-id="4dea9-145">Les vues</span><span class="sxs-lookup"><span data-stu-id="4dea9-145">Views</span></span>
        * <span data-ttu-id="4dea9-146">Accueil</span><span class="sxs-lookup"><span data-stu-id="4dea9-146">Home</span></span>
          * <span data-ttu-id="4dea9-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4dea9-147">Index.cshtml</span></span>

<span data-ttu-id="4dea9-148">Si la disposition précédente est classique dans les cas où des zones sont utilisées, seuls les fichiers de vues sont nécessaires pour utiliser cette structure de dossiers.</span><span class="sxs-lookup"><span data-stu-id="4dea9-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="4dea9-149">La détection de vues recherche un fichier de vue de zone correspondant dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="4dea9-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="4dea9-150">Associer le contrôleur à une zone</span><span class="sxs-lookup"><span data-stu-id="4dea9-150">Associate the controller with an Area</span></span>

<span data-ttu-id="4dea9-151">Les contrôleurs de zone sont désignés avec l’attribut [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="4dea9-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="4dea9-152">Ajouter une route de zone</span><span class="sxs-lookup"><span data-stu-id="4dea9-152">Add Area route</span></span>

<span data-ttu-id="4dea9-153">Les itinéraires de zone utilisent généralement le  [routage conventionnel](xref:mvc/controllers/routing#cr) plutôt que le [routage d’attributs](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="4dea9-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="4dea9-154">Le routage conventionnel est dépendant de l’ordre.</span><span class="sxs-lookup"><span data-stu-id="4dea9-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="4dea9-155">En général, les routes avec des zones doivent être placées plus haut dans la table de routage, car elles sont plus spécifiques que les routes sans zone.</span><span class="sxs-lookup"><span data-stu-id="4dea9-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="4dea9-156">`{area:...}` peut être utilisé comme jeton dans les modèles de route si l’espace d’url est uniforme dans toutes les zones :</span><span class="sxs-lookup"><span data-stu-id="4dea9-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="4dea9-157">Dans le code précédent, `exists` applique une contrainte qui impose à la route de correspondre à une zone.</span><span class="sxs-lookup"><span data-stu-id="4dea9-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="4dea9-158">Utilisation `{area:...}` de avec `MapControllerRoute` :</span><span class="sxs-lookup"><span data-stu-id="4dea9-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="4dea9-159">Est le mécanisme le moins compliqué pour ajouter le routage à des zones.</span><span class="sxs-lookup"><span data-stu-id="4dea9-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="4dea9-160">Correspond à tous les contrôleurs avec l' `[Area("Area name")]` attribut.</span><span class="sxs-lookup"><span data-stu-id="4dea9-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="4dea9-161">Le code suivant utilise <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> pour créer deux routes de zone nommées :</span><span class="sxs-lookup"><span data-stu-id="4dea9-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="4dea9-162">Pour plus d’informations, consultez [Routage de zones](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="4dea9-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="4dea9-163">Génération de liens avec zones MVC</span><span class="sxs-lookup"><span data-stu-id="4dea9-163">Link generation with MVC areas</span></span>

<span data-ttu-id="4dea9-164">Le code suivant tiré de l’[exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) illustre une génération de liens avec la zone spécifiée :</span><span class="sxs-lookup"><span data-stu-id="4dea9-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="4dea9-165">L’exemple de téléchargement inclut une [vue partielle](xref:mvc/views/partial) qui contient les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="4dea9-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="4dea9-166">Liens précédents.</span><span class="sxs-lookup"><span data-stu-id="4dea9-166">The preceding links.</span></span>
* <span data-ttu-id="4dea9-167">Les liens similaires à l’exception précédente `area` ne sont pas spécifiés.</span><span class="sxs-lookup"><span data-stu-id="4dea9-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="4dea9-168">La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés.</span><span class="sxs-lookup"><span data-stu-id="4dea9-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="4dea9-169">Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone et le même contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4dea9-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="4dea9-170">Quand la zone ou le contrôleur n’est pas spécifié, le routage dépend des valeurs [ambiantes](xref:mvc/controllers/routing#ambient).</span><span class="sxs-lookup"><span data-stu-id="4dea9-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="4dea9-171">Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens.</span><span class="sxs-lookup"><span data-stu-id="4dea9-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="4dea9-172">Dans de nombreux cas pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects avec le balisage qui ne spécifie pas la zone.</span><span class="sxs-lookup"><span data-stu-id="4dea9-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="4dea9-173">Pour plus d’informations, consultez [Routage vers des actions de contrôleur](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="4dea9-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="4dea9-174">Disposition partagée pour les zones en utilisant le fichier _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="4dea9-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="4dea9-175">Pour partager une disposition commune pour l’ensemble de l’application, conservez le *_ViewStart. cshtml* dans le [dossier racine](#arf)de l’application.</span><span class="sxs-lookup"><span data-stu-id="4dea9-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="4dea9-176">Pour plus d'informations, consultez <xref:mvc/views/layout></span><span class="sxs-lookup"><span data-stu-id="4dea9-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="4dea9-177">Dossier racine de l’application</span><span class="sxs-lookup"><span data-stu-id="4dea9-177">Application root folder</span></span>

<span data-ttu-id="4dea9-178">Le dossier racine de l’application est le dossier qui contient *Startup.cs* dans l’application Web créée avec les modèles ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="4dea9-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="4dea9-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="4dea9-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="4dea9-180">*/Views/_ViewImports. cshtml* , pour MVC et */pages/_ViewImports. cshtml* pour les :::no-loc(Razor)::: pages, n’est pas importé dans les vues dans les zones.</span><span class="sxs-lookup"><span data-stu-id="4dea9-180">*/Views/_ViewImports.cshtml* , for MVC, and */Pages/_ViewImports.cshtml* for :::no-loc(Razor)::: Pages, is not imported to views in areas.</span></span> <span data-ttu-id="4dea9-181">Utilisez l’une des approches suivantes pour fournir des importations d’affichage à tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="4dea9-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="4dea9-182">Ajoutez *_ViewImports. cshtml* au [dossier racine](#arf)de l’application.</span><span class="sxs-lookup"><span data-stu-id="4dea9-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="4dea9-183">Un *_ViewImports. cshtml* dans le dossier racine de l’application s’applique à toutes les vues de l’application.</span><span class="sxs-lookup"><span data-stu-id="4dea9-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="4dea9-184">Copiez le fichier *_ViewImports. cshtml* dans le dossier d’affichage approprié sous zones.</span><span class="sxs-lookup"><span data-stu-id="4dea9-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="4dea9-185">Le fichier *_ViewImports. cshtml* [contient généralement](xref:mvc/views/tag-helpers/intro) les instructions Imports, `@using` et `@inject` .</span><span class="sxs-lookup"><span data-stu-id="4dea9-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="4dea9-186">Pour plus d’informations, consultez [importation de directives partagées](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="4dea9-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="4dea9-187">Changer le dossier de zone par défaut où sont stockées les vues</span><span class="sxs-lookup"><span data-stu-id="4dea9-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="4dea9-188">Le code suivant remplace le dossier de zone par défaut `"Areas"` par `"MyAreas"` :</span><span class="sxs-lookup"><span data-stu-id="4dea9-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="4dea9-189">Zones avec :::no-loc(Razor)::: pages</span><span class="sxs-lookup"><span data-stu-id="4dea9-189">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="4dea9-190">Les zones avec :::no-loc(Razor)::: pages requièrent un `Areas/<area name>/Pages` dossier à la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="4dea9-190">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="4dea9-191">La structure de dossiers suivante est utilisée avec [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) :</span><span class="sxs-lookup"><span data-stu-id="4dea9-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="4dea9-192">Nom du projet</span><span class="sxs-lookup"><span data-stu-id="4dea9-192">Project name</span></span>
  * <span data-ttu-id="4dea9-193">Zones (Areas)</span><span class="sxs-lookup"><span data-stu-id="4dea9-193">Areas</span></span>
    * <span data-ttu-id="4dea9-194">Produits</span><span class="sxs-lookup"><span data-stu-id="4dea9-194">Products</span></span>
      * <span data-ttu-id="4dea9-195">Pages</span><span class="sxs-lookup"><span data-stu-id="4dea9-195">Pages</span></span>
        * <span data-ttu-id="4dea9-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="4dea9-196">_ViewImports</span></span>
        * <span data-ttu-id="4dea9-197">À propos de</span><span class="sxs-lookup"><span data-stu-id="4dea9-197">About</span></span>
        * <span data-ttu-id="4dea9-198">Index</span><span class="sxs-lookup"><span data-stu-id="4dea9-198">Index</span></span>
    * <span data-ttu-id="4dea9-199">Services</span><span class="sxs-lookup"><span data-stu-id="4dea9-199">Services</span></span>
      * <span data-ttu-id="4dea9-200">Pages</span><span class="sxs-lookup"><span data-stu-id="4dea9-200">Pages</span></span>
        * <span data-ttu-id="4dea9-201">Gérer</span><span class="sxs-lookup"><span data-stu-id="4dea9-201">Manage</span></span>
          * <span data-ttu-id="4dea9-202">À propos de</span><span class="sxs-lookup"><span data-stu-id="4dea9-202">About</span></span>
          * <span data-ttu-id="4dea9-203">Index</span><span class="sxs-lookup"><span data-stu-id="4dea9-203">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="4dea9-204">Génération de liens avec des :::no-loc(Razor)::: pages et des zones</span><span class="sxs-lookup"><span data-stu-id="4dea9-204">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="4dea9-205">Le code suivant tiré de [l’exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) illustre une génération de liens avec la zone spécifiée (par exemple, `asp-area="Products"`) :</span><span class="sxs-lookup"><span data-stu-id="4dea9-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="4dea9-206">L’exemple de code téléchargeable comprend une [vue partielle](xref:mvc/views/partial) qui contient les liens précédents et les mêmes liens sans spécification de la zone.</span><span class="sxs-lookup"><span data-stu-id="4dea9-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="4dea9-207">La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés.</span><span class="sxs-lookup"><span data-stu-id="4dea9-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="4dea9-208">Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone.</span><span class="sxs-lookup"><span data-stu-id="4dea9-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="4dea9-209">Quand la zone n’est pas spécifiée, le routage dépend des valeurs *ambiantes* .</span><span class="sxs-lookup"><span data-stu-id="4dea9-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="4dea9-210">Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens.</span><span class="sxs-lookup"><span data-stu-id="4dea9-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="4dea9-211">Dans de nombreux cas, pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects.</span><span class="sxs-lookup"><span data-stu-id="4dea9-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="4dea9-212">Prenons l’exemple des liens générés à partir de l’extrait de code suivant :</span><span class="sxs-lookup"><span data-stu-id="4dea9-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="4dea9-213">Pour le code précédent :</span><span class="sxs-lookup"><span data-stu-id="4dea9-213">For the preceding code:</span></span>

* <span data-ttu-id="4dea9-214">Le lien généré à partir de `<a asp-page="/Manage/About">` est correct uniquement lorsque la dernière requête concernait une page dans la zone `Services`.</span><span class="sxs-lookup"><span data-stu-id="4dea9-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="4dea9-215">Par exemple, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="4dea9-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="4dea9-216">Le lien généré à partir de `<a asp-page="/About">` est correct uniquement lorsque la dernière requête concernait une page dans `/Home`.</span><span class="sxs-lookup"><span data-stu-id="4dea9-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="4dea9-217">Le code est issu du [téléchargement de l’exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="4dea9-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="4dea9-218">Importer l’espace de noms et les Tag Helpers avec le fichier _ViewImports</span><span class="sxs-lookup"><span data-stu-id="4dea9-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="4dea9-219">Un fichier *_ViewImports. cshtml* peut être ajouté à chaque dossier des *pages* de zone pour importer l’espace de noms et les tag helpers sur chaque :::no-loc(Razor)::: page du dossier.</span><span class="sxs-lookup"><span data-stu-id="4dea9-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="4dea9-220">Considérez la zone *Services* de l’exemple de code, qui ne contient pas de fichier *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4dea9-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="4dea9-221">La balise suivante affiche la page */services/Manage/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="4dea9-221">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="4dea9-222">Dans le balisage précédent :</span><span class="sxs-lookup"><span data-stu-id="4dea9-222">In the preceding markup:</span></span>

* <span data-ttu-id="4dea9-223">Le nom de domaine complet doit être utilisé pour spécifier le modèle (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="4dea9-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="4dea9-224">Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) sont activés par `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="4dea9-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="4dea9-225">Dans l’exemple de téléchargement, la zone Products contient le fichier *_ViewImports.cshtml* suivant :</span><span class="sxs-lookup"><span data-stu-id="4dea9-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="4dea9-226">La balise suivante affiche la page */Products/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="4dea9-226">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="4dea9-227">Dans le fichier précédent, l’espace de noms et la `@addTagHelper` directive sont importés dans le fichier par le fichier *Areas/Products/Pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4dea9-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="4dea9-228">Pour plus d’informations, consultez [Gestion de l’étendue des Tag Helpers](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) et [Importation de directives partagées](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="4dea9-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="4dea9-229">Disposition partagée pour les :::no-loc(Razor)::: zones de pages</span><span class="sxs-lookup"><span data-stu-id="4dea9-229">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="4dea9-230">Pour partager une disposition commune pour l’ensemble de l’application, déplacez *_ViewStart.cshtml* dans le dossier racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="4dea9-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="4dea9-231">Zones de publication</span><span class="sxs-lookup"><span data-stu-id="4dea9-231">Publishing Areas</span></span>

<span data-ttu-id="4dea9-232">Tous les fichiers \*.cshtml et autres fichiers dans le répertoire *wwwroot* sont publiés en sortie quand `<Project Sdk="Microsoft.NET.Sdk.Web">` est inclus dans le fichier \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="4dea9-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4dea9-233">Les zones sont une fonctionnalité d’ASP.NET qui servent à organiser les fonctionnalités associées dans un groupe sous la forme d’un espace de noms (pour le routage) et d’une structure de dossiers (pour les vues) distincts.</span><span class="sxs-lookup"><span data-stu-id="4dea9-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="4dea9-234">L’utilisation de zones crée une hiérarchie à des fins de routage en ajoutant un autre paramètre de route, `area` , à `controller` et à `action` une :::no-loc(Razor)::: page `page` .</span><span class="sxs-lookup"><span data-stu-id="4dea9-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="4dea9-235">Les zones permettent de partitionner une application Web ASP.NET Core en groupes fonctionnels plus petits, chacun avec son propre ensemble de :::no-loc(Razor)::: pages, de contrôleurs, de vues et de modèles.</span><span class="sxs-lookup"><span data-stu-id="4dea9-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="4dea9-236">Une zone est en réalité une structure au sein d’une application.</span><span class="sxs-lookup"><span data-stu-id="4dea9-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="4dea9-237">Dans un projet web ASP.NET Core, les composants logiques comme Pages, Controller et View sont conservés dans différents dossiers.</span><span class="sxs-lookup"><span data-stu-id="4dea9-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="4dea9-238">Le runtime ASP.NET Core utilise des conventions de nommage pour créer la relation entre ces composants.</span><span class="sxs-lookup"><span data-stu-id="4dea9-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="4dea9-239">Pour une application volumineuse, il peut être avantageux de partitionner l’application en différentes zones de fonctionnalités de premier niveau.</span><span class="sxs-lookup"><span data-stu-id="4dea9-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="4dea9-240">Par exemple, pour une application d’e-commerce à plusieurs entités, il peut s’agir de la caisse, de la facturation et de la recherche.</span><span class="sxs-lookup"><span data-stu-id="4dea9-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="4dea9-241">Chacune de ces unités a leur propre zone pour contenir des vues, des contrôleurs, des :::no-loc(Razor)::: pages et des modèles.</span><span class="sxs-lookup"><span data-stu-id="4dea9-241">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="4dea9-242">Envisagez d’utiliser des zones dans un projet quand :</span><span class="sxs-lookup"><span data-stu-id="4dea9-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="4dea9-243">L’application est constituée de plusieurs composants fonctionnels globaux qui peuvent être logiquement séparés.</span><span class="sxs-lookup"><span data-stu-id="4dea9-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="4dea9-244">Vous pouvez partitionner l’application de façon à pouvoir travailler indépendamment sur chaque zone fonctionnelle.</span><span class="sxs-lookup"><span data-stu-id="4dea9-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="4dea9-245">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4dea9-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="4dea9-246">L’exemple de code téléchargeable fournit une application de base pour tester les zones.</span><span class="sxs-lookup"><span data-stu-id="4dea9-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="4dea9-247">Si vous utilisez des :::no-loc(Razor)::: pages, consultez [zones avec :::no-loc(Razor)::: pages](#areas-with-razor-pages) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="4dea9-247">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="4dea9-248">Zones pour contrôleurs avec vues</span><span class="sxs-lookup"><span data-stu-id="4dea9-248">Areas for controllers with views</span></span>

<span data-ttu-id="4dea9-249">Une application web ASP.NET Core type qui utilise des zones, des contrôleurs et des vues est constituée des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="4dea9-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="4dea9-250">Une [structure de dossiers Zone](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="4dea9-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="4dea9-251">Contrôleurs avec l' [`[Area]`](#attribute) attribut pour associer le contrôleur à la zone :</span><span class="sxs-lookup"><span data-stu-id="4dea9-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="4dea9-252">[Itinéraire de zone ajouté au démarrage](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="4dea9-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="4dea9-253">Structure de dossiers Zone</span><span class="sxs-lookup"><span data-stu-id="4dea9-253">Area folder structure</span></span>

<span data-ttu-id="4dea9-254">Imaginez une application qui contient deux groupes logiques, *Produits* et *Services* .</span><span class="sxs-lookup"><span data-stu-id="4dea9-254">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="4dea9-255">En utilisant des zones, la structure de dossiers se présenterait comme suit :</span><span class="sxs-lookup"><span data-stu-id="4dea9-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="4dea9-256">Nom du projet</span><span class="sxs-lookup"><span data-stu-id="4dea9-256">Project name</span></span>
  * <span data-ttu-id="4dea9-257">Zones (Areas)</span><span class="sxs-lookup"><span data-stu-id="4dea9-257">Areas</span></span>
    * <span data-ttu-id="4dea9-258">Produits</span><span class="sxs-lookup"><span data-stu-id="4dea9-258">Products</span></span>
      * <span data-ttu-id="4dea9-259">Contrôleurs</span><span class="sxs-lookup"><span data-stu-id="4dea9-259">Controllers</span></span>
        * <span data-ttu-id="4dea9-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="4dea9-260">HomeController.cs</span></span>
        * <span data-ttu-id="4dea9-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="4dea9-261">ManageController.cs</span></span>
      * <span data-ttu-id="4dea9-262">Les vues</span><span class="sxs-lookup"><span data-stu-id="4dea9-262">Views</span></span>
        * <span data-ttu-id="4dea9-263">Accueil</span><span class="sxs-lookup"><span data-stu-id="4dea9-263">Home</span></span>
          * <span data-ttu-id="4dea9-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4dea9-264">Index.cshtml</span></span>
        * <span data-ttu-id="4dea9-265">Gérer</span><span class="sxs-lookup"><span data-stu-id="4dea9-265">Manage</span></span>
          * <span data-ttu-id="4dea9-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4dea9-266">Index.cshtml</span></span>
          * <span data-ttu-id="4dea9-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="4dea9-267">About.cshtml</span></span>
    * <span data-ttu-id="4dea9-268">Services</span><span class="sxs-lookup"><span data-stu-id="4dea9-268">Services</span></span>
      * <span data-ttu-id="4dea9-269">Contrôleurs</span><span class="sxs-lookup"><span data-stu-id="4dea9-269">Controllers</span></span>
        * <span data-ttu-id="4dea9-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="4dea9-270">HomeController.cs</span></span>
      * <span data-ttu-id="4dea9-271">Les vues</span><span class="sxs-lookup"><span data-stu-id="4dea9-271">Views</span></span>
        * <span data-ttu-id="4dea9-272">Accueil</span><span class="sxs-lookup"><span data-stu-id="4dea9-272">Home</span></span>
          * <span data-ttu-id="4dea9-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4dea9-273">Index.cshtml</span></span>

<span data-ttu-id="4dea9-274">Si la disposition précédente est classique dans les cas où des zones sont utilisées, seuls les fichiers de vues sont nécessaires pour utiliser cette structure de dossiers.</span><span class="sxs-lookup"><span data-stu-id="4dea9-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="4dea9-275">La détection de vues recherche un fichier de vue de zone correspondant dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="4dea9-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="4dea9-276">Associer le contrôleur à une zone</span><span class="sxs-lookup"><span data-stu-id="4dea9-276">Associate the controller with an Area</span></span>

<span data-ttu-id="4dea9-277">Les contrôleurs de zone sont désignés avec l’attribut [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="4dea9-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="4dea9-278">Ajouter une route de zone</span><span class="sxs-lookup"><span data-stu-id="4dea9-278">Add Area route</span></span>

<span data-ttu-id="4dea9-279">Les routes de zones utilisent généralement un routage conventionnel plutôt qu’un routage par attributs.</span><span class="sxs-lookup"><span data-stu-id="4dea9-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="4dea9-280">Le routage conventionnel est dépendant de l’ordre.</span><span class="sxs-lookup"><span data-stu-id="4dea9-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="4dea9-281">En général, les routes avec des zones doivent être placées plus haut dans la table de routage, car elles sont plus spécifiques que les routes sans zone.</span><span class="sxs-lookup"><span data-stu-id="4dea9-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="4dea9-282">`{area:...}` peut être utilisé comme jeton dans les modèles de route si l’espace d’url est uniforme dans toutes les zones :</span><span class="sxs-lookup"><span data-stu-id="4dea9-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="4dea9-283">Dans le code précédent, `exists` applique une contrainte qui impose à la route de correspondre à une zone.</span><span class="sxs-lookup"><span data-stu-id="4dea9-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="4dea9-284">Pour ajouter un routage à des zones, le mécanisme le moins compliqué consiste à utiliser `{area:...}`.</span><span class="sxs-lookup"><span data-stu-id="4dea9-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="4dea9-285">Le code suivant utilise <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> pour créer deux routes de zone nommées :</span><span class="sxs-lookup"><span data-stu-id="4dea9-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="4dea9-286">Si vous utilisez `MapAreaRoute` avec ASP.NET Core 2.2, prenez connaissance de [ce problème sur GitHub](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="4dea9-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="4dea9-287">Pour plus d’informations, consultez [Routage de zones](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="4dea9-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="4dea9-288">Génération de liens avec zones MVC</span><span class="sxs-lookup"><span data-stu-id="4dea9-288">Link generation with MVC areas</span></span>

<span data-ttu-id="4dea9-289">Le code suivant tiré de l’[exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) illustre une génération de liens avec la zone spécifiée :</span><span class="sxs-lookup"><span data-stu-id="4dea9-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="4dea9-290">Les liens générés par le code précédent sont valides où que ce soit dans l’application.</span><span class="sxs-lookup"><span data-stu-id="4dea9-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="4dea9-291">L’exemple de code téléchargeable comprend une [vue partielle](xref:mvc/views/partial) qui contient les liens précédents et les mêmes liens sans spécification de la zone.</span><span class="sxs-lookup"><span data-stu-id="4dea9-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="4dea9-292">La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés.</span><span class="sxs-lookup"><span data-stu-id="4dea9-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="4dea9-293">Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone et le même contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4dea9-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="4dea9-294">Quand la zone ou le contrôleur n’est pas spécifié, le routage dépend des valeurs *ambiantes* .</span><span class="sxs-lookup"><span data-stu-id="4dea9-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="4dea9-295">Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens.</span><span class="sxs-lookup"><span data-stu-id="4dea9-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="4dea9-296">Dans de nombreux cas, pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects.</span><span class="sxs-lookup"><span data-stu-id="4dea9-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="4dea9-297">Pour plus d’informations, consultez [Routage vers des actions de contrôleur](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="4dea9-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="4dea9-298">Disposition partagée pour les zones en utilisant le fichier _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="4dea9-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="4dea9-299">Pour partager une disposition commune pour l’ensemble de l’application, déplacez *_ViewStart.cshtml* dans le dossier racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="4dea9-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="4dea9-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="4dea9-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="4dea9-301">Dans son emplacement standard, */Views/_ViewImports.cshtml* ne s’applique pas aux zones.</span><span class="sxs-lookup"><span data-stu-id="4dea9-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="4dea9-302">Pour utiliser les [balises](xref:mvc/views/tag-helpers/intro)courantes, `@using` ou `@inject` dans votre zone, assurez-vous qu’un fichier *_ViewImports. cshtml* approprié [s’applique à vos vues de zone](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="4dea9-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="4dea9-303">Si vous souhaitez obtenir le même comportement dans toutes vos vues, déplacez */Views/_ViewImports.cshtml* vers la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="4dea9-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="4dea9-304">Changer le dossier de zone par défaut où sont stockées les vues</span><span class="sxs-lookup"><span data-stu-id="4dea9-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="4dea9-305">Le code suivant remplace le dossier de zone par défaut `"Areas"` par `"MyAreas"` :</span><span class="sxs-lookup"><span data-stu-id="4dea9-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="4dea9-306">Zones avec :::no-loc(Razor)::: pages</span><span class="sxs-lookup"><span data-stu-id="4dea9-306">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="4dea9-307">Les zones avec :::no-loc(Razor)::: pages requièrent un `Areas/<area name>/Pages` dossier à la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="4dea9-307">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="4dea9-308">La structure de dossiers suivante est utilisée avec [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) :</span><span class="sxs-lookup"><span data-stu-id="4dea9-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="4dea9-309">Nom du projet</span><span class="sxs-lookup"><span data-stu-id="4dea9-309">Project name</span></span>
  * <span data-ttu-id="4dea9-310">Zones (Areas)</span><span class="sxs-lookup"><span data-stu-id="4dea9-310">Areas</span></span>
    * <span data-ttu-id="4dea9-311">Produits</span><span class="sxs-lookup"><span data-stu-id="4dea9-311">Products</span></span>
      * <span data-ttu-id="4dea9-312">Pages</span><span class="sxs-lookup"><span data-stu-id="4dea9-312">Pages</span></span>
        * <span data-ttu-id="4dea9-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="4dea9-313">_ViewImports</span></span>
        * <span data-ttu-id="4dea9-314">À propos de</span><span class="sxs-lookup"><span data-stu-id="4dea9-314">About</span></span>
        * <span data-ttu-id="4dea9-315">Index</span><span class="sxs-lookup"><span data-stu-id="4dea9-315">Index</span></span>
    * <span data-ttu-id="4dea9-316">Services</span><span class="sxs-lookup"><span data-stu-id="4dea9-316">Services</span></span>
      * <span data-ttu-id="4dea9-317">Pages</span><span class="sxs-lookup"><span data-stu-id="4dea9-317">Pages</span></span>
        * <span data-ttu-id="4dea9-318">Gérer</span><span class="sxs-lookup"><span data-stu-id="4dea9-318">Manage</span></span>
          * <span data-ttu-id="4dea9-319">À propos de</span><span class="sxs-lookup"><span data-stu-id="4dea9-319">About</span></span>
          * <span data-ttu-id="4dea9-320">Index</span><span class="sxs-lookup"><span data-stu-id="4dea9-320">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="4dea9-321">Génération de liens avec des :::no-loc(Razor)::: pages et des zones</span><span class="sxs-lookup"><span data-stu-id="4dea9-321">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="4dea9-322">Le code suivant tiré de [l’exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) illustre une génération de liens avec la zone spécifiée (par exemple, `asp-area="Products"`) :</span><span class="sxs-lookup"><span data-stu-id="4dea9-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="4dea9-323">Les liens générés par le code précédent sont valides où que ce soit dans l’application.</span><span class="sxs-lookup"><span data-stu-id="4dea9-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="4dea9-324">L’exemple de code téléchargeable comprend une [vue partielle](xref:mvc/views/partial) qui contient les liens précédents et les mêmes liens sans spécification de la zone.</span><span class="sxs-lookup"><span data-stu-id="4dea9-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="4dea9-325">La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés.</span><span class="sxs-lookup"><span data-stu-id="4dea9-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="4dea9-326">Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone.</span><span class="sxs-lookup"><span data-stu-id="4dea9-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="4dea9-327">Quand la zone n’est pas spécifiée, le routage dépend des valeurs *ambiantes* .</span><span class="sxs-lookup"><span data-stu-id="4dea9-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="4dea9-328">Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens.</span><span class="sxs-lookup"><span data-stu-id="4dea9-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="4dea9-329">Dans de nombreux cas, pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects.</span><span class="sxs-lookup"><span data-stu-id="4dea9-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="4dea9-330">Prenons l’exemple des liens générés à partir de l’extrait de code suivant :</span><span class="sxs-lookup"><span data-stu-id="4dea9-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="4dea9-331">Pour le code précédent :</span><span class="sxs-lookup"><span data-stu-id="4dea9-331">For the preceding code:</span></span>

* <span data-ttu-id="4dea9-332">Le lien généré à partir de `<a asp-page="/Manage/About">` est correct uniquement lorsque la dernière requête concernait une page dans la zone `Services`.</span><span class="sxs-lookup"><span data-stu-id="4dea9-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="4dea9-333">Par exemple, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="4dea9-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="4dea9-334">Le lien généré à partir de `<a asp-page="/About">` est correct uniquement lorsque la dernière requête concernait une page dans `/Home`.</span><span class="sxs-lookup"><span data-stu-id="4dea9-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="4dea9-335">Le code est issu du [téléchargement de l’exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="4dea9-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="4dea9-336">Importer l’espace de noms et les Tag Helpers avec le fichier _ViewImports</span><span class="sxs-lookup"><span data-stu-id="4dea9-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="4dea9-337">Un fichier *_ViewImports. cshtml* peut être ajouté à chaque dossier des *pages* de zone pour importer l’espace de noms et les tag helpers sur chaque :::no-loc(Razor)::: page du dossier.</span><span class="sxs-lookup"><span data-stu-id="4dea9-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="4dea9-338">Considérez la zone *Services* de l’exemple de code, qui ne contient pas de fichier *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4dea9-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="4dea9-339">La balise suivante affiche la page */services/Manage/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="4dea9-339">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="4dea9-340">Dans le balisage précédent :</span><span class="sxs-lookup"><span data-stu-id="4dea9-340">In the preceding markup:</span></span>

* <span data-ttu-id="4dea9-341">Le nom de domaine complet doit être utilisé pour spécifier le modèle (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="4dea9-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="4dea9-342">Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) sont activés par `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="4dea9-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="4dea9-343">Dans l’exemple de téléchargement, la zone Products contient le fichier *_ViewImports.cshtml* suivant :</span><span class="sxs-lookup"><span data-stu-id="4dea9-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="4dea9-344">La balise suivante affiche la page */Products/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="4dea9-344">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="4dea9-345">Dans le fichier précédent, l’espace de noms et la `@addTagHelper` directive sont importés dans le fichier par le fichier *Areas/Products/Pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4dea9-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="4dea9-346">Pour plus d’informations, consultez [Gestion de l’étendue des Tag Helpers](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) et [Importation de directives partagées](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="4dea9-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="4dea9-347">Disposition partagée pour les :::no-loc(Razor)::: zones de pages</span><span class="sxs-lookup"><span data-stu-id="4dea9-347">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="4dea9-348">Pour partager une disposition commune pour l’ensemble de l’application, déplacez *_ViewStart.cshtml* dans le dossier racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="4dea9-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="4dea9-349">Zones de publication</span><span class="sxs-lookup"><span data-stu-id="4dea9-349">Publishing Areas</span></span>

<span data-ttu-id="4dea9-350">Tous les fichiers \*.cshtml et autres fichiers dans le répertoire *wwwroot* sont publiés en sortie quand `<Project Sdk="Microsoft.NET.Sdk.Web">` est inclus dans le fichier \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="4dea9-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
