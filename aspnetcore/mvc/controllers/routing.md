---
title: Routage vers les actions du contrôleur dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ASP.NET Core MVC utilise le middleware (intergiciel) de routage pour mettre en correspondance les URL des requêtes entrantes et les mapper à des actions.
ms.author: riande
ms.date: 3/25/2020
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
uid: mvc/controllers/routing
ms.openlocfilehash: 9f64dd8f0ca026cec4b7ee4b5ea02523139eed4f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057152"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="7b514-103">Routage vers les actions du contrôleur dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b514-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="7b514-104">Par [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7b514-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7b514-105">Les contrôleurs de ASP.NET Core utilisent l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) de routage pour faire correspondre les URL des demandes entrantes et les mapper aux [actions](#action).</span><span class="sxs-lookup"><span data-stu-id="7b514-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="7b514-106">Itinéraires des modèles :</span><span class="sxs-lookup"><span data-stu-id="7b514-106">Routes templates:</span></span>

* <span data-ttu-id="7b514-107">Sont définis dans le code de démarrage ou les attributs.</span><span class="sxs-lookup"><span data-stu-id="7b514-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="7b514-108">Décrivez comment les chemins d’URL sont mis en correspondance avec les [actions](#action).</span><span class="sxs-lookup"><span data-stu-id="7b514-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="7b514-109">Sont utilisées pour générer des URL pour les liens.</span><span class="sxs-lookup"><span data-stu-id="7b514-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="7b514-110">Les liens générés sont généralement retournés dans les réponses.</span><span class="sxs-lookup"><span data-stu-id="7b514-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="7b514-111">Les actions sont soit [de façon conventionnelle,](#cr) soit [routées par attribut](#ar).</span><span class="sxs-lookup"><span data-stu-id="7b514-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="7b514-112">Le fait de placer un itinéraire sur le contrôleur ou l' [action](#action) le rend positionné par attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="7b514-113">Pour plus d’informations, consultez [Routage mixte](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="7b514-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="7b514-114">Ce document :</span><span class="sxs-lookup"><span data-stu-id="7b514-114">This document:</span></span>

* <span data-ttu-id="7b514-115">Explique les interactions entre MVC et le routage :</span><span class="sxs-lookup"><span data-stu-id="7b514-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="7b514-116">Comment les applications MVC classiques utilisent les fonctionnalités de routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="7b514-117">Couvre les deux :</span><span class="sxs-lookup"><span data-stu-id="7b514-117">Covers both:</span></span>
    * <span data-ttu-id="7b514-118">Le [routage conventionnel](#cr) est généralement utilisé avec les contrôleurs et les vues.</span><span class="sxs-lookup"><span data-stu-id="7b514-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="7b514-119">*Routage d’attribut* utilisé avec les API REST.</span><span class="sxs-lookup"><span data-stu-id="7b514-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="7b514-120">Si vous êtes principalement intéressé par le routage des API REST, passez à la section relative à l' [acheminement des attributs pour les API REST](#ar) .</span><span class="sxs-lookup"><span data-stu-id="7b514-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="7b514-121">Consultez [routage](xref:fundamentals/routing) pour plus d’informations sur le routage avancé.</span><span class="sxs-lookup"><span data-stu-id="7b514-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="7b514-122">Fait référence au système de routage par défaut ajouté dans ASP.NET Core 3,0, appelé routage de point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7b514-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="7b514-123">Il est possible d’utiliser des contrôleurs avec la version précédente du routage pour des raisons de compatibilité.</span><span class="sxs-lookup"><span data-stu-id="7b514-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="7b514-124">Pour obtenir des instructions, consultez le [Guide de migration 2.2-3.0](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="7b514-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="7b514-125">Reportez-vous à la [version 2,2 de ce document](xref:mvc/controllers/routing?view=aspnetcore-2.2) pour obtenir des documents de référence sur le système de routage hérité.</span><span class="sxs-lookup"><span data-stu-id="7b514-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="7b514-126">Configurer l’itinéraire conventionnel</span><span class="sxs-lookup"><span data-stu-id="7b514-126">Set up conventional route</span></span>

<span data-ttu-id="7b514-127">`Startup.Configure` a généralement un code similaire à ce qui suit lors de l’utilisation du [routage conventionnel](#crd):</span><span class="sxs-lookup"><span data-stu-id="7b514-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="7b514-128">À l’intérieur de l’appel à <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> est utilisé pour créer un itinéraire unique.</span><span class="sxs-lookup"><span data-stu-id="7b514-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> is used to create a single route.</span></span> <span data-ttu-id="7b514-129">L’itinéraire unique est nommé `default` route.</span><span class="sxs-lookup"><span data-stu-id="7b514-129">The single route is named `default` route.</span></span> <span data-ttu-id="7b514-130">La plupart des applications avec contrôleurs et vues utilisent un modèle de routage similaire à l' `default` itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7b514-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="7b514-131">Les API REST doivent utiliser le [routage d’attributs](#ar).</span><span class="sxs-lookup"><span data-stu-id="7b514-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="7b514-132">Le modèle de routage `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="7b514-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="7b514-133">Correspond à un chemin d’URL comme `/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="7b514-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="7b514-134">Extrait les valeurs d’itinéraire `{ controller = Products, action = Details, id = 5 }` en tokenant le chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="7b514-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="7b514-135">L’extraction des valeurs de route aboutit à une correspondance si l’application possède un contrôleur nommé `ProductsController` et une `Details` action :</span><span class="sxs-lookup"><span data-stu-id="7b514-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="7b514-136">`/Products/Details/5` le modèle lie la valeur de `id = 5` pour définir le `id` paramètre sur `5` .</span><span class="sxs-lookup"><span data-stu-id="7b514-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="7b514-137">Pour plus d’informations, consultez [liaison de modèle](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="7b514-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="7b514-138">`{controller=Home}` définit `Home` comme valeur par défaut `controller` .</span><span class="sxs-lookup"><span data-stu-id="7b514-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="7b514-139">`{action=Index}` définit `Index` comme valeur par défaut `action` .</span><span class="sxs-lookup"><span data-stu-id="7b514-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="7b514-140">Le `?` caractère dans `{id?}` définit `id` comme étant facultatif.</span><span class="sxs-lookup"><span data-stu-id="7b514-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="7b514-141">Les paramètres de route par défaut et facultatifs n’ont pas besoin d’être présents dans le chemin d’URL pour qu’une correspondance soit établie.</span><span class="sxs-lookup"><span data-stu-id="7b514-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="7b514-142">Pour une description détaillée de la syntaxe du modèle de route, consultez [Informations de référence sur le modèle de route](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="7b514-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="7b514-143">Correspond au chemin d’accès de l’URL `/` .</span><span class="sxs-lookup"><span data-stu-id="7b514-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="7b514-144">Produit les valeurs de route `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="7b514-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="7b514-145">Les valeurs pour `controller` et `action` utilisent les valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="7b514-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="7b514-146">`id` ne produit pas de valeur, car il n’existe pas de segment correspondant dans le chemin d’accès de l’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="7b514-147">`/` correspond uniquement s’il existe une `HomeController` `Index` action et :</span><span class="sxs-lookup"><span data-stu-id="7b514-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="7b514-148">À l’aide de la définition de contrôleur et du modèle de routage précédents, l' `HomeController.Index` action est exécutée pour les chemins d’URL suivants :</span><span class="sxs-lookup"><span data-stu-id="7b514-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="7b514-149">Le chemin d’accès de l’URL `/` utilise les `Home` contrôleurs et l’action du modèle de routage par défaut `Index` .</span><span class="sxs-lookup"><span data-stu-id="7b514-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="7b514-150">Le chemin d’accès de l’URL `/Home` utilise l’action par défaut du modèle de routage `Index` .</span><span class="sxs-lookup"><span data-stu-id="7b514-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="7b514-151">La méthode pratique <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> :</span><span class="sxs-lookup"><span data-stu-id="7b514-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="7b514-152">Remplace</span><span class="sxs-lookup"><span data-stu-id="7b514-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="7b514-153">Le routage est configuré à l’aide de et de l' <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="7b514-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> middleware.</span></span> <span data-ttu-id="7b514-154">Pour utiliser des contrôleurs :</span><span class="sxs-lookup"><span data-stu-id="7b514-154">To use controllers:</span></span>
>
> * <span data-ttu-id="7b514-155">Appelez <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> à l’intérieur `UseEndpoints` pour mapper les contrôleurs [routés d’attribut](#ar) .</span><span class="sxs-lookup"><span data-stu-id="7b514-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="7b514-156">Appelez <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> ou <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> pour mapper les contrôleurs routés de façon [conventionnelle](#cr) et les contrôleurs [routés d’attribut](#ar) .</span><span class="sxs-lookup"><span data-stu-id="7b514-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="7b514-157">Routage conventionnel</span><span class="sxs-lookup"><span data-stu-id="7b514-157">Conventional routing</span></span>

<span data-ttu-id="7b514-158">Le routage conventionnel est utilisé avec les contrôleurs et les vues.</span><span class="sxs-lookup"><span data-stu-id="7b514-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="7b514-159">La route `default` :</span><span class="sxs-lookup"><span data-stu-id="7b514-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="7b514-160">est un exemple de *routage conventionnel* .</span><span class="sxs-lookup"><span data-stu-id="7b514-160">is an example of a *conventional routing* .</span></span> <span data-ttu-id="7b514-161">Il s’agit du *routage conventionnel* , car il établit une *Convention* pour les chemins d’URL :</span><span class="sxs-lookup"><span data-stu-id="7b514-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="7b514-162">Le premier segment de chemin d’accès, `{controller=Home}` , correspond au nom du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7b514-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="7b514-163">Le deuxième segment, `{action=Index}` , correspond au nom de l' [action](#action) .</span><span class="sxs-lookup"><span data-stu-id="7b514-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="7b514-164">Le troisième segment, `{id?}` est utilisé pour un facultatif `id` .</span><span class="sxs-lookup"><span data-stu-id="7b514-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="7b514-165">Le `?` dans le `{id?}` rend facultatif.</span><span class="sxs-lookup"><span data-stu-id="7b514-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="7b514-166">`id` est utilisé pour mapper à une entité de modèle.</span><span class="sxs-lookup"><span data-stu-id="7b514-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="7b514-167">À l’aide de cet `default` itinéraire, le chemin d’accès de l’URL :</span><span class="sxs-lookup"><span data-stu-id="7b514-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="7b514-168">`/Products/List` correspond à l' `ProductsController.List` action.</span><span class="sxs-lookup"><span data-stu-id="7b514-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="7b514-169">`/Blog/Article/17` est mappé à `BlogController.Article` et en général, le modèle lie le `id` paramètre à 17.</span><span class="sxs-lookup"><span data-stu-id="7b514-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="7b514-170">Ce mappage :</span><span class="sxs-lookup"><span data-stu-id="7b514-170">This mapping:</span></span>

* <span data-ttu-id="7b514-171">Est basé **uniquement** sur les noms de contrôleur et d' [action](#action) .</span><span class="sxs-lookup"><span data-stu-id="7b514-171">Is based on the controller and [action](#action) names **only** .</span></span>
* <span data-ttu-id="7b514-172">N’est pas basé sur des espaces de noms, des emplacements de fichiers sources ou des paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="7b514-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="7b514-173">L’utilisation du routage conventionnel avec l’itinéraire par défaut permet de créer l’application sans avoir à trouver un nouveau modèle d’URL pour chaque action.</span><span class="sxs-lookup"><span data-stu-id="7b514-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="7b514-174">Pour une application avec des actions de style [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) , avec cohérence des URL entre les contrôleurs :</span><span class="sxs-lookup"><span data-stu-id="7b514-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="7b514-175">Permet de simplifier le code.</span><span class="sxs-lookup"><span data-stu-id="7b514-175">Helps simplify the code.</span></span>
* <span data-ttu-id="7b514-176">Rend l’interface utilisateur plus prévisible.</span><span class="sxs-lookup"><span data-stu-id="7b514-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="7b514-177">Le `id` dans le code précédent est défini comme facultatif par le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="7b514-178">Les actions peuvent s’exécuter sans l’ID facultatif fourni dans le cadre de l’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="7b514-179">En règle générale, lorsque `id` est omis de l’URL :</span><span class="sxs-lookup"><span data-stu-id="7b514-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="7b514-180">`id` a la valeur `0` par liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="7b514-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="7b514-181">Aucune entité n’a été trouvée dans la correspondance de base de données `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="7b514-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="7b514-182">Le [routage d’attributs](#ar) fournit un contrôle affiné pour rendre l’ID requis pour certaines actions et non pour d’autres.</span><span class="sxs-lookup"><span data-stu-id="7b514-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="7b514-183">Par Convention, la documentation comprend des paramètres facultatifs tels que le `id` moment où ils sont susceptibles d’apparaître dans une utilisation correcte.</span><span class="sxs-lookup"><span data-stu-id="7b514-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="7b514-184">La plupart des applications doivent choisir un schéma de routage de base et descriptif pour que les URL soient lisibles et explicites.</span><span class="sxs-lookup"><span data-stu-id="7b514-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="7b514-185">La route conventionnelle par défaut `{controller=Home}/{action=Index}/{id?}` :</span><span class="sxs-lookup"><span data-stu-id="7b514-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="7b514-186">Prend en charge un schéma de routage de base et descriptif.</span><span class="sxs-lookup"><span data-stu-id="7b514-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="7b514-187">Est un point de départ pratique pour les applications basées sur une interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7b514-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="7b514-188">Est le seul modèle de routage nécessaire pour de nombreuses applications d’interface utilisateur Web.</span><span class="sxs-lookup"><span data-stu-id="7b514-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="7b514-189">Pour les applications d’interface utilisateur Web plus volumineuses, un autre itinéraire utilisant des [zones](#areas) est souvent le plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7b514-189">For larger web UI apps, another route using [Areas](#areas) is frequently all that's needed.</span></span>

<span data-ttu-id="7b514-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> et <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span><span class="sxs-lookup"><span data-stu-id="7b514-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span></span>

* <span data-ttu-id="7b514-191">Assigner automatiquement une valeur de **commande** à leurs points de terminaison en fonction de l’ordre dans lequel ils sont appelés.</span><span class="sxs-lookup"><span data-stu-id="7b514-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="7b514-192">Routage des points de terminaison dans ASP.NET Core 3,0 et versions ultérieures :</span><span class="sxs-lookup"><span data-stu-id="7b514-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="7b514-193">N’a pas de concept d’itinéraires.</span><span class="sxs-lookup"><span data-stu-id="7b514-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="7b514-194">Ne fournit pas de garantie de classement pour l’exécution de l’extensibilité, tous les points de terminaison sont traités à la fois.</span><span class="sxs-lookup"><span data-stu-id="7b514-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="7b514-195">Activez la [journalisation](xref:fundamentals/logging/index) pour voir comment les implémentations de routage intégrées, comme <xref:Microsoft.AspNetCore.Routing.Route>, établissent des correspondances avec les requêtes.</span><span class="sxs-lookup"><span data-stu-id="7b514-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="7b514-196">Le [routage des attributs](#ar) est expliqué plus loin dans ce document.</span><span class="sxs-lookup"><span data-stu-id="7b514-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="7b514-197">Plusieurs itinéraires conventionnels</span><span class="sxs-lookup"><span data-stu-id="7b514-197">Multiple conventional routes</span></span>

<span data-ttu-id="7b514-198">Vous pouvez ajouter plusieurs [itinéraires conventionnels](#cr) dans `UseEndpoints` en ajoutant des appels à <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> et <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> .</span><span class="sxs-lookup"><span data-stu-id="7b514-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>.</span></span> <span data-ttu-id="7b514-199">Cela permet de définir plusieurs conventions ou d’ajouter des itinéraires conventionnels dédiés à une [action](#action)spécifique, par exemple :</span><span class="sxs-lookup"><span data-stu-id="7b514-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="7b514-200">L' `blog` itinéraire dans le code précédent est un **itinéraire conventionnel dédié** .</span><span class="sxs-lookup"><span data-stu-id="7b514-200">The `blog` route in the preceding code is a **dedicated conventional route** .</span></span> <span data-ttu-id="7b514-201">Il s’agit d’un itinéraire conventionnel dédié, car :</span><span class="sxs-lookup"><span data-stu-id="7b514-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="7b514-202">Il utilise le [routage conventionnel](#cr).</span><span class="sxs-lookup"><span data-stu-id="7b514-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="7b514-203">Elle est dédiée à une [action](#action)spécifique.</span><span class="sxs-lookup"><span data-stu-id="7b514-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="7b514-204">Étant donné que `controller` et `action` n’apparaissent pas dans le modèle de routage `"blog/{*article}"` en tant que paramètres :</span><span class="sxs-lookup"><span data-stu-id="7b514-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="7b514-205">Ils peuvent uniquement avoir les valeurs par défaut `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="7b514-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="7b514-206">Cet itinéraire est toujours mappé à l’action `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="7b514-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="7b514-207">`/Blog`, `/Blog/Article` et `/Blog/{any-string}` sont les seuls chemins d’URL qui correspondent à l’itinéraire du blog.</span><span class="sxs-lookup"><span data-stu-id="7b514-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="7b514-208">L’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="7b514-208">The preceding example:</span></span>

* <span data-ttu-id="7b514-209">`blog` l’itinéraire a une priorité plus élevée pour les correspondances que l' `default` itinéraire, car il est d’abord ajouté.</span><span class="sxs-lookup"><span data-stu-id="7b514-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="7b514-210">Est un exemple de routage de style [Slug](https://developer.mozilla.org/docs/Glossary/Slug) dans lequel il est courant d’avoir un nom d’article dans le cadre de l’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-210">Is an example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="7b514-211">Dans ASP.NET Core 3,0 et versions ultérieures, le routage ne suit pas :</span><span class="sxs-lookup"><span data-stu-id="7b514-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="7b514-212">Définissez un concept appelé *itinéraire* .</span><span class="sxs-lookup"><span data-stu-id="7b514-212">Define a concept called a *route* .</span></span> <span data-ttu-id="7b514-213">`UseRouting` Ajoute la correspondance d’itinéraire au pipeline de l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="7b514-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="7b514-214">L' `UseRouting` intergiciel examine l’ensemble des points de terminaison définis dans l’application et sélectionne la meilleure correspondance de point de terminaison en fonction de la demande.</span><span class="sxs-lookup"><span data-stu-id="7b514-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="7b514-215">Fournir des garanties sur l’ordre d’exécution de l’extensibilité comme <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> ou <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="7b514-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="7b514-216">Consultez [routage](xref:fundamentals/routing) pour obtenir des documents de référence sur le routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="7b514-217">Ordre de routage conventionnel</span><span class="sxs-lookup"><span data-stu-id="7b514-217">Conventional routing order</span></span>

<span data-ttu-id="7b514-218">Le routage conventionnel correspond uniquement à une combinaison d’action et de contrôleur définie par l’application.</span><span class="sxs-lookup"><span data-stu-id="7b514-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="7b514-219">Cela vise à simplifier les cas où les itinéraires conventionnels se chevauchent.</span><span class="sxs-lookup"><span data-stu-id="7b514-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="7b514-220">L’ajout d’itinéraires à l’aide de <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> et <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> assigne automatiquement une valeur de commande à leurs points de terminaison en fonction de l’ordre dans lequel ils sont appelés.</span><span class="sxs-lookup"><span data-stu-id="7b514-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="7b514-221">Les correspondances d’un itinéraire qui apparaît précédemment ont une priorité plus élevée.</span><span class="sxs-lookup"><span data-stu-id="7b514-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="7b514-222">Le routage conventionnel est dépendant de l’ordre.</span><span class="sxs-lookup"><span data-stu-id="7b514-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7b514-223">En général, les itinéraires avec des zones doivent être placés plus tôt, car ils sont plus spécifiques que les itinéraires sans zone.</span><span class="sxs-lookup"><span data-stu-id="7b514-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="7b514-224">Les [itinéraires conventionnels dédiés](#dcr) avec des paramètres d’itinéraire Catch-All comme `{*article}` peuvent rendre une route trop [gourmande](xref:fundamentals/routing#greedy), ce qui signifie qu’elle correspond aux URL que vous avez prévues pour être mises en correspondance par d’autres itinéraires.</span><span class="sxs-lookup"><span data-stu-id="7b514-224">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="7b514-225">Mettez les itinéraires gourmands plus tard dans la table de routage pour empêcher les correspondances gourmandes.</span><span class="sxs-lookup"><span data-stu-id="7b514-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="7b514-226">Résolution des actions ambiguës</span><span class="sxs-lookup"><span data-stu-id="7b514-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="7b514-227">Lorsque deux points de terminaison correspondent au routage, le routage doit effectuer l’une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7b514-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="7b514-228">Choisissez le meilleur candidat.</span><span class="sxs-lookup"><span data-stu-id="7b514-228">Choose the best candidate.</span></span>
* <span data-ttu-id="7b514-229">Levée d'une exception.</span><span class="sxs-lookup"><span data-stu-id="7b514-229">Throw an exception.</span></span>

<span data-ttu-id="7b514-230">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7b514-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="7b514-231">Le contrôleur précédent définit deux actions qui correspondent :</span><span class="sxs-lookup"><span data-stu-id="7b514-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="7b514-232">Chemin de l’URL `/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="7b514-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="7b514-233">Acheminer les données `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="7b514-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="7b514-234">Il s’agit d’un modèle classique pour les contrôleurs MVC :</span><span class="sxs-lookup"><span data-stu-id="7b514-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="7b514-235">`Edit(int)` affiche un formulaire pour modifier un produit.</span><span class="sxs-lookup"><span data-stu-id="7b514-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="7b514-236">`Edit(int, Product)` traite le formulaire publié.</span><span class="sxs-lookup"><span data-stu-id="7b514-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="7b514-237">Pour résoudre le routage correct :</span><span class="sxs-lookup"><span data-stu-id="7b514-237">To resolve the correct route:</span></span>

* <span data-ttu-id="7b514-238">`Edit(int, Product)` est sélectionné lorsque la requête est HTTP `POST` .</span><span class="sxs-lookup"><span data-stu-id="7b514-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="7b514-239">`Edit(int)` est sélectionné lorsque le [verbe http](#verb) est autre chose.</span><span class="sxs-lookup"><span data-stu-id="7b514-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="7b514-240">`Edit(int)` est généralement appelé via `GET` .</span><span class="sxs-lookup"><span data-stu-id="7b514-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="7b514-241">Le <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` , est fourni au routage afin qu’il puisse choisir en fonction de la méthode http de la requête.</span><span class="sxs-lookup"><span data-stu-id="7b514-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="7b514-242">Le `HttpPostAttribute` fournit `Edit(int, Product)` une meilleure correspondance que `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="7b514-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="7b514-243">Il est important de comprendre le rôle des attributs comme `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="7b514-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="7b514-244">Des attributs similaires sont définis pour d’autres [verbes HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="7b514-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="7b514-245">Dans le cadre d’un [routage conventionnel](#cr), il est courant que des actions utilisent le même nom d’action lorsqu’elles font partie d’un formulaire d’affichage, envoyer un flux de travail de formulaire.</span><span class="sxs-lookup"><span data-stu-id="7b514-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="7b514-246">Par exemple, consultez [examiner les deux méthodes d’action de modification](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="7b514-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="7b514-247">Si le routage ne peut pas choisir un meilleur candidat, une <xref:System.Reflection.AmbiguousMatchException> exception est levée et répertorie les différents points de terminaison correspondants.</span><span class="sxs-lookup"><span data-stu-id="7b514-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="7b514-248">Noms de routes conventionnels</span><span class="sxs-lookup"><span data-stu-id="7b514-248">Conventional route names</span></span>

<span data-ttu-id="7b514-249">Les chaînes  `"blog"` et `"default"` dans les exemples suivants sont des noms de route conventionnels :</span><span class="sxs-lookup"><span data-stu-id="7b514-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="7b514-250">Les noms de routes donnent un nom logique à l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7b514-250">The route names give the route a logical name.</span></span> <span data-ttu-id="7b514-251">L’itinéraire nommé peut être utilisé pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="7b514-252">L’utilisation d’un itinéraire nommé simplifie la création d’URL lorsque l’ordonnancement des itinéraires peut compliquer la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="7b514-253">Les noms de routes doivent être uniques à l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b514-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="7b514-254">Noms des itinéraires :</span><span class="sxs-lookup"><span data-stu-id="7b514-254">Route names:</span></span>

* <span data-ttu-id="7b514-255">N’ont aucun impact sur la correspondance d’URL ou la gestion des demandes.</span><span class="sxs-lookup"><span data-stu-id="7b514-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="7b514-256">Sont utilisés uniquement pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-256">Are used only for URL generation.</span></span>

<span data-ttu-id="7b514-257">Le concept de nom d’itinéraire est représenté dans le routage en tant que [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="7b514-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="7b514-258">Nom de l' **itinéraire** et **nom du point de terminaison** :</span><span class="sxs-lookup"><span data-stu-id="7b514-258">The terms **route name** and **endpoint name** :</span></span>

* <span data-ttu-id="7b514-259">Sont interchangeables.</span><span class="sxs-lookup"><span data-stu-id="7b514-259">Are interchangeable.</span></span>
* <span data-ttu-id="7b514-260">Celui qui est utilisé dans la documentation et le code dépend de l’API qui est décrite.</span><span class="sxs-lookup"><span data-stu-id="7b514-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="7b514-261">Routage d’attribut pour les API REST</span><span class="sxs-lookup"><span data-stu-id="7b514-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="7b514-262">Les API REST doivent utiliser le routage d’attributs pour modéliser les fonctionnalités de l’application sous la forme d’un ensemble de ressources où les opérations sont représentées par des [verbes HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="7b514-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="7b514-263">Le routage par attributs utilise un ensemble d’attributs pour mapper les actions directement aux modèles de routes.</span><span class="sxs-lookup"><span data-stu-id="7b514-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="7b514-264">Le `StartUp.Configure` code suivant est courant pour une API REST et est utilisé dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7b514-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="7b514-265">Dans le code précédent, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> est appelé dans `UseEndpoints` pour mapper les contrôleurs routés d’attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="7b514-266">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7b514-266">In the following example:</span></span>

* <span data-ttu-id="7b514-267">La `Configure` méthode précédente est utilisée.</span><span class="sxs-lookup"><span data-stu-id="7b514-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="7b514-268">`HomeController` correspond à un ensemble d’URL similaires à ce que la route conventionnelle par défaut `{controller=Home}/{action=Index}/{id?}` correspond.</span><span class="sxs-lookup"><span data-stu-id="7b514-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="7b514-269">L' `HomeController.Index` action est exécutée pour tous les chemins d’accès d’URL `/` ,, `/Home` `/Home/Index` ou `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="7b514-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="7b514-270">Cet exemple met en évidence une différence de programmation clé entre le routage d’attributs et le [routage conventionnel](#cr).</span><span class="sxs-lookup"><span data-stu-id="7b514-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="7b514-271">Le routage des attributs requiert plus d’entrée pour spécifier un itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7b514-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="7b514-272">L’itinéraire par défaut conventionnel gère les routes de façon plus succincte.</span><span class="sxs-lookup"><span data-stu-id="7b514-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="7b514-273">Toutefois, le routage d’attributs permet et requiert un contrôle précis des modèles de routage qui s’appliquent à chaque [action](#action).</span><span class="sxs-lookup"><span data-stu-id="7b514-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="7b514-274">Avec le routage d’attributs, les noms de contrôleur et d’action ne jouent aucun rôle dans lequel l’action est mise en correspondance, sauf si le [remplacement de jeton](#routing-token-replacement-templates-ref-label) est utilisé.</span><span class="sxs-lookup"><span data-stu-id="7b514-274">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="7b514-275">L’exemple suivant correspond aux mêmes URL que l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="7b514-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="7b514-276">Le code suivant utilise le remplacement de jeton pour `action` et `controller` :</span><span class="sxs-lookup"><span data-stu-id="7b514-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="7b514-277">Le code suivant s’applique `[Route("[controller]/[action]")]` au contrôleur :</span><span class="sxs-lookup"><span data-stu-id="7b514-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="7b514-278">Dans le code précédent, les `Index` modèles de méthode doivent précéder `/` ou `~/` atteindre les modèles de routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="7b514-279">Les modèles de routes appliqués à une action qui commencent par `/` ou `~/` ne sont pas combinés avec les modèles de routes appliqués au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7b514-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="7b514-280">Pour plus d’informations sur la sélection d’un modèle de routage, consultez [priorité des modèles de routage](xref:fundamentals/routing#rtp) .</span><span class="sxs-lookup"><span data-stu-id="7b514-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="7b514-281">Noms de routage réservés</span><span class="sxs-lookup"><span data-stu-id="7b514-281">Reserved routing names</span></span>

<span data-ttu-id="7b514-282">Les mots clés suivants sont des noms de paramètres d’itinéraire réservés lors de l’utilisation de contrôleurs ou de :::no-loc(Razor)::: pages :</span><span class="sxs-lookup"><span data-stu-id="7b514-282">The following keywords are reserved route parameter names when using Controllers or :::no-loc(Razor)::: Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="7b514-283">L’utilisation de `page` comme paramètre d’itinéraire avec routage d’attribut est une erreur courante.</span><span class="sxs-lookup"><span data-stu-id="7b514-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="7b514-284">Cela entraîne un comportement incohérent et confus avec la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="7b514-285">Les noms de paramètres spéciaux sont utilisés par la génération d’URL pour déterminer si une opération de génération d’URL fait référence à une :::no-loc(Razor)::: page ou à un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7b514-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a :::no-loc(Razor)::: Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="7b514-286">Modèles de verbe HTTP</span><span class="sxs-lookup"><span data-stu-id="7b514-286">HTTP verb templates</span></span>

<span data-ttu-id="7b514-287">ASP.NET Core a les modèles de verbe HTTP suivants :</span><span class="sxs-lookup"><span data-stu-id="7b514-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="7b514-288">[HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="7b514-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="7b514-289">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="7b514-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="7b514-290">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="7b514-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="7b514-291">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="7b514-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="7b514-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="7b514-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="7b514-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="7b514-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="7b514-294">Modèles de routage</span><span class="sxs-lookup"><span data-stu-id="7b514-294">Route templates</span></span>

<span data-ttu-id="7b514-295">ASP.NET Core contient les modèles d’itinéraire suivants :</span><span class="sxs-lookup"><span data-stu-id="7b514-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="7b514-296">Tous les [modèles de verbe http](#verb) sont des modèles de routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="7b514-297">[Itinéraire](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="7b514-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="7b514-298">Routage d’attribut avec attributs de verbe http</span><span class="sxs-lookup"><span data-stu-id="7b514-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="7b514-299">Prenons le contrôleur suivant :</span><span class="sxs-lookup"><span data-stu-id="7b514-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="7b514-300">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="7b514-300">In the preceding code:</span></span>

* <span data-ttu-id="7b514-301">Chaque action contient l' `[HttpGet]` attribut, qui limite la correspondance aux requêtes http obtient uniquement.</span><span class="sxs-lookup"><span data-stu-id="7b514-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="7b514-302">L' `GetProduct` action comprend le `"{id}"` modèle. par conséquent, `id` est ajouté au `"api/[controller]"` modèle sur le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7b514-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="7b514-303">Le modèle de méthode est `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="7b514-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="7b514-304">Par conséquent, cette action correspond uniquement aux demandes d’extraction pour le formulaire `/api/test2/xyz` ,, `/api/test2/123` `/api/test2/{any string}` , etc.</span><span class="sxs-lookup"><span data-stu-id="7b514-304">Therefore this action only matches GET requests for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="7b514-305">L' `GetIntProduct` action contient le `"int/{id:int}")` modèle.</span><span class="sxs-lookup"><span data-stu-id="7b514-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="7b514-306">La `:int` partie du modèle limite les `id` valeurs d’itinéraire aux chaînes qui peuvent être converties en un entier.</span><span class="sxs-lookup"><span data-stu-id="7b514-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="7b514-307">Une demande d’accès à `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="7b514-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="7b514-308">Ne correspond pas à cette action.</span><span class="sxs-lookup"><span data-stu-id="7b514-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="7b514-309">Retourne une erreur [404 introuvable](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="7b514-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="7b514-310">L' `GetInt2Product` action contient `{id}` dans le modèle, mais ne contraint pas les `id` valeurs qui peuvent être converties en entier.</span><span class="sxs-lookup"><span data-stu-id="7b514-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="7b514-311">Une demande d’accès à `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="7b514-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="7b514-312">Correspond à cet itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7b514-312">Matches this route.</span></span>
  * <span data-ttu-id="7b514-313">La liaison de modèle ne peut pas être convertie `abc` en entier.</span><span class="sxs-lookup"><span data-stu-id="7b514-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="7b514-314">Le `id` paramètre de la méthode est un entier.</span><span class="sxs-lookup"><span data-stu-id="7b514-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="7b514-315">Retourne une [demande 400 incorrecte](https://developer.mozilla.org/docs/Web/HTTP/Status/400) , car la liaison de modèle n’a pas pu être convertie `abc` en entier.</span><span class="sxs-lookup"><span data-stu-id="7b514-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="7b514-316">Le routage d’attribut peut utiliser des <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributs tels que <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> et <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="7b514-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="7b514-317">Tous les attributs de [verbe http](#verb) acceptent un modèle d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7b514-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="7b514-318">L’exemple suivant montre deux actions qui correspondent au même modèle de routage :</span><span class="sxs-lookup"><span data-stu-id="7b514-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="7b514-319">Utilisation du chemin d’accès de l’URL `/products3` :</span><span class="sxs-lookup"><span data-stu-id="7b514-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="7b514-320">L' `MyProductsController.ListProducts` action s’exécute lorsque le [verbe http](#verb) est `GET` .</span><span class="sxs-lookup"><span data-stu-id="7b514-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="7b514-321">L' `MyProductsController.CreateProduct` action s’exécute lorsque le [verbe http](#verb) est `POST` .</span><span class="sxs-lookup"><span data-stu-id="7b514-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="7b514-322">Lors de la création d’une API REST, il est rare que vous deviez utiliser `[Route(...)]` sur une méthode d’action, car l’action accepte toutes les méthodes http.</span><span class="sxs-lookup"><span data-stu-id="7b514-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="7b514-323">Il est préférable d’utiliser l’attribut de [verbe http](#verb) plus spécifique pour préciser ce que votre API prend en charge.</span><span class="sxs-lookup"><span data-stu-id="7b514-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="7b514-324">Les clients des API REST doivent normalement connaître les chemins et les verbes HTTP qui correspondent à des opérations logiques spécifiques.</span><span class="sxs-lookup"><span data-stu-id="7b514-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="7b514-325">Les API REST doivent utiliser le routage d’attributs pour modéliser les fonctionnalités de l’application sous la forme d’un ensemble de ressources où les opérations sont représentées par des verbes HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b514-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="7b514-326">Cela signifie que de nombreuses opérations, par exemple, obtenir et poster sur la même ressource logique, utilisent la même URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="7b514-327">Le routage d’attributs fournit le niveau de contrôle nécessaire pour concevoir avec soin la disposition des points de terminaison publics d’une API.</span><span class="sxs-lookup"><span data-stu-id="7b514-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="7b514-328">Dans la mesure où une route d’attribut s’applique à une action spécifique, il est facile de placer les paramètres nécessaires dans la définition du modèle de route.</span><span class="sxs-lookup"><span data-stu-id="7b514-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="7b514-329">Dans l’exemple suivant, `id` est obligatoire dans le cadre du chemin d’accès de l’URL :</span><span class="sxs-lookup"><span data-stu-id="7b514-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="7b514-330">L' `Products2ApiController.GetProduct(int)` action :</span><span class="sxs-lookup"><span data-stu-id="7b514-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="7b514-331">Est exécuté avec un chemin d’URL comme `/products2/3`</span><span class="sxs-lookup"><span data-stu-id="7b514-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="7b514-332">N’est pas exécuté avec le chemin d’accès de l’URL `/products2` .</span><span class="sxs-lookup"><span data-stu-id="7b514-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="7b514-333">L’attribut [[Consommed]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permet à une action de limiter les types de contenu de demande pris en charge.</span><span class="sxs-lookup"><span data-stu-id="7b514-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="7b514-334">Pour plus d’informations, consultez [définir les types de contenu de demande pris en charge avec l’attribut consomme](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="7b514-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="7b514-335">Consultez [Routage](xref:fundamentals/routing) pour obtenir une description complète des modèles de routes et des options associées.</span><span class="sxs-lookup"><span data-stu-id="7b514-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="7b514-336">Pour plus d’informations sur `[ApiController]` , consultez [attribut ApiController](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="7b514-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="7b514-337">Nom de l’itinéraire</span><span class="sxs-lookup"><span data-stu-id="7b514-337">Route name</span></span>

<span data-ttu-id="7b514-338">Le code suivant définit un nom de route`Products_List` :</span><span class="sxs-lookup"><span data-stu-id="7b514-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="7b514-339">Les noms de routes peuvent être utilisés pour générer une URL basée sur une route spécifique.</span><span class="sxs-lookup"><span data-stu-id="7b514-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="7b514-340">Noms des itinéraires :</span><span class="sxs-lookup"><span data-stu-id="7b514-340">Route names:</span></span>

* <span data-ttu-id="7b514-341">N’ont aucun impact sur le comportement de correspondance d’URL du routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="7b514-342">Sont utilisés uniquement pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-342">Are only used for URL generation.</span></span>

<span data-ttu-id="7b514-343">Les noms de routes doivent être unique à l’échelle de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b514-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="7b514-344">Comparez le code précédent avec l’itinéraire par défaut conventionnel, qui définit le `id` paramètre comme facultatif ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="7b514-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="7b514-345">La possibilité de spécifier avec précision les API présente des avantages, tels que l’autorisation `/products` et la `/products/5` distribution à des actions différentes.</span><span class="sxs-lookup"><span data-stu-id="7b514-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="7b514-346">Combinaison d’itinéraires d’attributs</span><span class="sxs-lookup"><span data-stu-id="7b514-346">Combining attribute routes</span></span>

<span data-ttu-id="7b514-347">Pour rendre le routage par attributs moins répétitif, les attributs de route sont combinés avec des attributs de route sur les actions individuelles.</span><span class="sxs-lookup"><span data-stu-id="7b514-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="7b514-348">Les modèles de routes définis sur le contrôleur sont ajoutés à des modèles de routes sur les actions.</span><span class="sxs-lookup"><span data-stu-id="7b514-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="7b514-349">Placer un attribut de route sur le contrôleur a pour effet que **toutes** les actions du contrôleur utilisent le routage par attributs.</span><span class="sxs-lookup"><span data-stu-id="7b514-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="7b514-350">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="7b514-350">In the preceding example:</span></span>

* <span data-ttu-id="7b514-351">Le chemin d’accès de l’URL `/products` peut correspondre `ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="7b514-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="7b514-352">Le chemin d’accès de l’URL `/products/5` peut correspondre `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="7b514-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="7b514-353">Ces deux actions correspondent uniquement à HTTP `GET` , car elles sont marquées avec l' `[HttpGet]` attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="7b514-354">Les modèles de routes appliqués à une action qui commencent par `/` ou `~/` ne sont pas combinés avec les modèles de routes appliqués au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7b514-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="7b514-355">L’exemple suivant correspond à un ensemble de chemins d’URL similaires à l’itinéraire par défaut.</span><span class="sxs-lookup"><span data-stu-id="7b514-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="7b514-356">Le tableau suivant décrit les `[Route]` attributs dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="7b514-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="7b514-357">Attribut</span><span class="sxs-lookup"><span data-stu-id="7b514-357">Attribute</span></span>               | <span data-ttu-id="7b514-358">Est combiné avec `[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="7b514-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="7b514-359">Définit le modèle de routage</span><span class="sxs-lookup"><span data-stu-id="7b514-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="7b514-360">Oui</span><span class="sxs-lookup"><span data-stu-id="7b514-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="7b514-361">Oui</span><span class="sxs-lookup"><span data-stu-id="7b514-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="7b514-362">**Non**</span><span class="sxs-lookup"><span data-stu-id="7b514-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="7b514-363">Yes</span><span class="sxs-lookup"><span data-stu-id="7b514-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="7b514-364">Ordre de routage des attributs</span><span class="sxs-lookup"><span data-stu-id="7b514-364">Attribute route order</span></span>

<span data-ttu-id="7b514-365">Le routage génère une arborescence et met en correspondance tous les points de terminaison simultanément :</span><span class="sxs-lookup"><span data-stu-id="7b514-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="7b514-366">Les entrées d’itinéraire se comportent comme si elles étaient placées dans un ordre idéal.</span><span class="sxs-lookup"><span data-stu-id="7b514-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="7b514-367">Les itinéraires les plus spécifiques ont une chance de s’exécuter avant les itinéraires plus généraux.</span><span class="sxs-lookup"><span data-stu-id="7b514-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="7b514-368">Par exemple, un itinéraire d’attribut comme `blog/search/{topic}` est plus spécifique qu’un itinéraire d’attribut comme `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="7b514-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="7b514-369">L' `blog/search/{topic}` itinéraire a une priorité plus élevée, par défaut, car il est plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="7b514-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="7b514-370">En utilisant le [routage conventionnel](#cr), le développeur est chargé de placer les routes dans l’ordre souhaité.</span><span class="sxs-lookup"><span data-stu-id="7b514-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="7b514-371">Les routes d’attribut peuvent configurer une commande à l’aide de la <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> propriété.</span><span class="sxs-lookup"><span data-stu-id="7b514-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="7b514-372">Tous les [attributs d’itinéraire](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) fournis par l’infrastructure incluent `Order` .</span><span class="sxs-lookup"><span data-stu-id="7b514-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="7b514-373">Les routes sont traitées selon un ordre croissant de la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="7b514-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="7b514-374">L’ordre par défaut est `0`.</span><span class="sxs-lookup"><span data-stu-id="7b514-374">The default order is `0`.</span></span> <span data-ttu-id="7b514-375">La définition d’un itinéraire à l’aide de `Order = -1` s’exécute avant les itinéraires qui ne définissent pas de commande.</span><span class="sxs-lookup"><span data-stu-id="7b514-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="7b514-376">La définition d’un itinéraire à l’aide de `Order = 1` s’exécute après l’ordonnancement par défaut.</span><span class="sxs-lookup"><span data-stu-id="7b514-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="7b514-377">**Évitez** de dépendre de `Order` .</span><span class="sxs-lookup"><span data-stu-id="7b514-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="7b514-378">Si l’espace URL d’une application exige que les valeurs d’ordre explicites soient routées correctement, il est probable que les clients soient également déroutants.</span><span class="sxs-lookup"><span data-stu-id="7b514-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="7b514-379">En général, le routage des attributs sélectionne l’itinéraire correct avec la correspondance d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="7b514-380">Si l’ordre par défaut utilisé pour la génération d’URL ne fonctionne pas, l’utilisation d’un nom d’itinéraire comme remplacement est généralement plus simple que l’application de la `Order` propriété.</span><span class="sxs-lookup"><span data-stu-id="7b514-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="7b514-381">Considérez les deux contrôleurs suivants qui définissent la correspondance d’itinéraire `/home` :</span><span class="sxs-lookup"><span data-stu-id="7b514-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="7b514-382">`/home`La demande avec le code précédent lève une exception semblable à la suivante :</span><span class="sxs-lookup"><span data-stu-id="7b514-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="7b514-383">`Order`L’ajout de à l’un des attributs de route résout l’ambiguïté :</span><span class="sxs-lookup"><span data-stu-id="7b514-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="7b514-384">Avec le code précédent, `/home` exécute le `HomeController.Index` point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7b514-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="7b514-385">Pour accéder à la `MyDemoController.MyIndex` requête, `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="7b514-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="7b514-386">**Remarque**  :</span><span class="sxs-lookup"><span data-stu-id="7b514-386">**Note** :</span></span>

* <span data-ttu-id="7b514-387">Le code précédent est un exemple ou une conception de routage médiocre.</span><span class="sxs-lookup"><span data-stu-id="7b514-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="7b514-388">Il a été utilisé pour illustrer la `Order` propriété.</span><span class="sxs-lookup"><span data-stu-id="7b514-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="7b514-389">La `Order` propriété ne résout que l’ambiguïté, ce modèle ne peut pas être mis en correspondance.</span><span class="sxs-lookup"><span data-stu-id="7b514-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="7b514-390">Il serait préférable de supprimer le `[Route("Home")]` modèle.</span><span class="sxs-lookup"><span data-stu-id="7b514-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="7b514-391">Pour plus d’informations sur l’ordre des itinéraires avec les pages [ :::no-loc(Razor)::: , consultez conventions des applications et des itinéraires](xref:razor-pages/razor-pages-conventions#route-order) de routage :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="7b514-391">See [:::no-loc(Razor)::: Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="7b514-392">Dans certains cas, une erreur HTTP 500 est retournée avec des itinéraires ambigus.</span><span class="sxs-lookup"><span data-stu-id="7b514-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="7b514-393">Utilisez la [journalisation](xref:fundamentals/logging/index) pour voir quels points de terminaison ont provoqué le `AmbiguousMatchException` .</span><span class="sxs-lookup"><span data-stu-id="7b514-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="7b514-394">Remplacement de jetons dans les modèles de routage [contrôleur], [action], [zone]</span><span class="sxs-lookup"><span data-stu-id="7b514-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="7b514-395">Pour plus de commodité, les itinéraires d’attributs prennent en charge le remplacement de jeton pour les paramètres d’itinéraire réservés en plaçant un jeton dans l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="7b514-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="7b514-396">Crochets : `[]`</span><span class="sxs-lookup"><span data-stu-id="7b514-396">Square brackets: `[]`</span></span>
* <span data-ttu-id="7b514-397">Accolades : `{}`</span><span class="sxs-lookup"><span data-stu-id="7b514-397">Curly braces: `{}`</span></span>

<span data-ttu-id="7b514-398">Les jetons `[action]` , `[area]` et `[controller]` sont remplacés par les valeurs du nom d’action, du nom de la zone et du nom de contrôleur de l’action dans laquelle l’itinéraire est défini :</span><span class="sxs-lookup"><span data-stu-id="7b514-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="7b514-399">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="7b514-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="7b514-400">Correspond `/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="7b514-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="7b514-401">Correspond `/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="7b514-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="7b514-402">Le remplacement des jetons se produit à la dernière étape de la création des routes d’attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="7b514-403">L’exemple précédent se comporte de la même façon que le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7b514-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="7b514-404">Les routes d’attribut peuvent aussi être combinées avec l’héritage.</span><span class="sxs-lookup"><span data-stu-id="7b514-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="7b514-405">Il s’agit d’une puissante combinaison avec le remplacement des jetons.</span><span class="sxs-lookup"><span data-stu-id="7b514-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="7b514-406">Le remplacement des jetons s’applique aussi aux noms de routes définis par des routes d’attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="7b514-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`génère un nom d’itinéraire unique pour chaque action :</span><span class="sxs-lookup"><span data-stu-id="7b514-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="7b514-408">Le remplacement des jetons s’applique aussi aux noms de routes définis par des routes d’attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="7b514-409"> génère un nom de route unique pour chaque action.</span><span class="sxs-lookup"><span data-stu-id="7b514-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="7b514-410">Pour faire correspondre le délimiteur littéral de remplacement de jetons `[` ou `]`, placez-le en échappement en répétant le caractère (`[[` ou `]]`).</span><span class="sxs-lookup"><span data-stu-id="7b514-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="7b514-411">Utiliser un transformateur de paramètre pour personnaliser le remplacement des jetons</span><span class="sxs-lookup"><span data-stu-id="7b514-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="7b514-412">Le remplacement des jetons peut être personnalisé à l’aide d’un transformateur de paramètre.</span><span class="sxs-lookup"><span data-stu-id="7b514-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="7b514-413">Un transformateur de paramètre implémente <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> et transforme la valeur des paramètres.</span><span class="sxs-lookup"><span data-stu-id="7b514-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="7b514-414">Par exemple, un `SlugifyParameterTransformer` transformateur de paramètre personnalisé remplace la `SubscriptionManagement` valeur d’itinéraire par `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="7b514-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="7b514-415"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> est une convention de modèle d’application qui :</span><span class="sxs-lookup"><span data-stu-id="7b514-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="7b514-416">Applique un transformateur de paramètre à toutes les routes d’attribut dans une application.</span><span class="sxs-lookup"><span data-stu-id="7b514-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="7b514-417">Personnalise les valeurs de jeton de route d’attribut quand elles sont remplacées.</span><span class="sxs-lookup"><span data-stu-id="7b514-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="7b514-418">La `ListAll` méthode précédente correspond à `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="7b514-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="7b514-419">`RouteTokenTransformerConvention` est inscrit en tant qu’option dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7b514-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="7b514-420">Consultez la [documentation Web MDN sur Slug](https://developer.mozilla.org/docs/Glossary/Slug) pour connaître la définition de Slug.</span><span class="sxs-lookup"><span data-stu-id="7b514-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="7b514-421">Plusieurs itinéraires d’attributs</span><span class="sxs-lookup"><span data-stu-id="7b514-421">Multiple attribute routes</span></span>

<span data-ttu-id="7b514-422">Le routage par attributs prend en charge la définition de plusieurs routes pour atteindre la même action.</span><span class="sxs-lookup"><span data-stu-id="7b514-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="7b514-423">L’utilisation la plus courante de ceci est d’imiter le comportement de la route conventionnelle par défaut, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7b514-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="7b514-424">Le fait de placer plusieurs attributs de route sur le contrôleur signifie que chacun d’eux est combiné avec chacun des attributs de route sur les méthodes d’action :</span><span class="sxs-lookup"><span data-stu-id="7b514-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="7b514-425">Toutes les contraintes d’itinéraire [http Verb](#verb) implémentent `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="7b514-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="7b514-426">Quand plusieurs attributs d’itinéraire implémentent <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> sont placés sur une action :</span><span class="sxs-lookup"><span data-stu-id="7b514-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="7b514-427">Chaque contrainte d’action est associée au modèle de routage appliqué au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7b514-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="7b514-428">L’utilisation de plusieurs itinéraires sur des actions peut paraître utile et puissante, mais il est préférable de conserver l’espace d’URL de base et bien défini.</span><span class="sxs-lookup"><span data-stu-id="7b514-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="7b514-429">Utilisez plusieurs itinéraires sur des actions **uniquement** lorsque cela est nécessaire, par exemple pour prendre en charge les clients existants.</span><span class="sxs-lookup"><span data-stu-id="7b514-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="7b514-430">Spécification facultative de paramètres, de valeurs par défaut et de contraintes pour les routes d’attribut</span><span class="sxs-lookup"><span data-stu-id="7b514-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="7b514-431">Les routes d’attribut prennent en charge la même syntaxe inline que les routes conventionnelles pour spécifier des paramètres, des valeurs par défaut et des contraintes facultatifs.</span><span class="sxs-lookup"><span data-stu-id="7b514-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="7b514-432">Dans le code précédent, `[HttpPost("product/{id:int}")]` applique une contrainte d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7b514-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="7b514-433">L' `ProductsController.ShowProduct` action est mise en correspondance uniquement par les chemins d’accès d’URL tels que `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="7b514-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="7b514-434">La partie de modèle `{id:int}` de routage limite ce segment à des entiers uniquement.</span><span class="sxs-lookup"><span data-stu-id="7b514-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="7b514-435">Pour une description détaillée de la syntaxe du modèle de route, consultez [Informations de référence sur le modèle de route](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="7b514-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="7b514-436">Attributs d’itinéraire personnalisés à l’aide de IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="7b514-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="7b514-437">Tous les [attributs d’itinéraire](#rt) implémentent <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="7b514-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="7b514-438">Le runtime ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="7b514-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="7b514-439">Recherche des attributs sur les classes de contrôleur et les méthodes d’action au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b514-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="7b514-440">Utilise les attributs qui implémentent `IRouteTemplateProvider` pour générer le jeu initial d’itinéraires.</span><span class="sxs-lookup"><span data-stu-id="7b514-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="7b514-441">Implémentez `IRouteTemplateProvider` pour définir des attributs de routage personnalisés.</span><span class="sxs-lookup"><span data-stu-id="7b514-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="7b514-442">Chaque `IRouteTemplateProvider` vous permet de définir une route avec un modèle, un nom et un ordre de route personnalisés :</span><span class="sxs-lookup"><span data-stu-id="7b514-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="7b514-443">La `Get` méthode précédente retourne `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="7b514-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="7b514-444">Utiliser le modèle d’application pour personnaliser les itinéraires d’attributs</span><span class="sxs-lookup"><span data-stu-id="7b514-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="7b514-445">Le modèle d’application :</span><span class="sxs-lookup"><span data-stu-id="7b514-445">The application model:</span></span>

* <span data-ttu-id="7b514-446">Est un modèle objet créé au démarrage.</span><span class="sxs-lookup"><span data-stu-id="7b514-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="7b514-447">Contient toutes les métadonnées utilisées par ASP.NET Core pour router et exécuter les actions dans une application.</span><span class="sxs-lookup"><span data-stu-id="7b514-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="7b514-448">Le modèle d’application comprend toutes les données collectées à partir des attributs d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7b514-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="7b514-449">Les données des attributs de route sont fournies par l' `IRouteTemplateProvider` implémentation de.</span><span class="sxs-lookup"><span data-stu-id="7b514-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="7b514-450">Utilisées</span><span class="sxs-lookup"><span data-stu-id="7b514-450">Conventions:</span></span>

* <span data-ttu-id="7b514-451">Peut être écrit pour modifier le modèle d’application afin de personnaliser le comportement du routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="7b514-452">Sont lus au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b514-452">Are read at app startup.</span></span>

<span data-ttu-id="7b514-453">Cette section présente un exemple simple de personnalisation du routage à l’aide du modèle d’application.</span><span class="sxs-lookup"><span data-stu-id="7b514-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="7b514-454">Le code suivant permet aux itinéraires de s’aligner à peu près avec la structure de dossiers du projet.</span><span class="sxs-lookup"><span data-stu-id="7b514-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="7b514-455">Le code suivant empêche l' `namespace` application de la Convention aux contrôleurs qui sont des attributs routés :</span><span class="sxs-lookup"><span data-stu-id="7b514-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="7b514-456">Par exemple, le contrôleur suivant n’utilise pas `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="7b514-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="7b514-457">La méthode `NamespaceRoutingConvention.Apply` :</span><span class="sxs-lookup"><span data-stu-id="7b514-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="7b514-458">Ne fait rien si le contrôleur est un attribut routé.</span><span class="sxs-lookup"><span data-stu-id="7b514-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="7b514-459">Définit le modèle de contrôleurs en fonction de `namespace` , avec la base `namespace` supprimée.</span><span class="sxs-lookup"><span data-stu-id="7b514-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="7b514-460">Le `NamespaceRoutingConvention` peut être appliqué dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7b514-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="7b514-461">Considérons, par exemple, le contrôleur suivant :</span><span class="sxs-lookup"><span data-stu-id="7b514-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="7b514-462">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="7b514-462">In the preceding code:</span></span>

* <span data-ttu-id="7b514-463">La base `namespace` est `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="7b514-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="7b514-464">Le nom complet du contrôleur précédent est `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="7b514-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="7b514-465">`NamespaceRoutingConvention`Définit le modèle de contrôleurs sur `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="7b514-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="7b514-466">`NamespaceRoutingConvention`Peut également être appliqué en tant qu’attribut sur un contrôleur :</span><span class="sxs-lookup"><span data-stu-id="7b514-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="7b514-467">Routage mixte : routage conventionnel et routage par attributs</span><span class="sxs-lookup"><span data-stu-id="7b514-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="7b514-468">Les applications ASP.NET Core peuvent combiner l’utilisation du routage conventionnel et du routage des attributs.</span><span class="sxs-lookup"><span data-stu-id="7b514-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="7b514-469">Il est courant d’utiliser des routes conventionnelles pour les contrôleurs délivrant des pages HTML pour les navigateurs, et le routage par attributs pour les contrôleurs délivrant des API REST.</span><span class="sxs-lookup"><span data-stu-id="7b514-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="7b514-470">Les actions sont routées de façon conventionnelle ou routées par attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7b514-471">Le fait de placer une route sur le contrôleur ou sur l’action les rend « routés par attribut ».</span><span class="sxs-lookup"><span data-stu-id="7b514-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7b514-472">Les actions qui définissent des routes d’attribut ne sont pas accessibles via les routes conventionnelles et vice versa.</span><span class="sxs-lookup"><span data-stu-id="7b514-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="7b514-473">**Tout** attribut de route sur le contrôleur effectue **toutes les** actions dans l’attribut de contrôleur routé.</span><span class="sxs-lookup"><span data-stu-id="7b514-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="7b514-474">Le routage des attributs et le routage conventionnel utilisent le même moteur de routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="7b514-475">Génération d’URL et valeurs ambiantes</span><span class="sxs-lookup"><span data-stu-id="7b514-475">URL Generation and ambient values</span></span>

<span data-ttu-id="7b514-476">Les applications peuvent utiliser les fonctionnalités de génération d’URL de routage pour générer des liens URL vers des actions.</span><span class="sxs-lookup"><span data-stu-id="7b514-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="7b514-477">La génération d’URL élimine le codage en dur des URL, ce qui rend le code plus robuste et plus facile à gérer.</span><span class="sxs-lookup"><span data-stu-id="7b514-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="7b514-478">Cette section se concentre sur les fonctionnalités de génération d’URL fournies par MVC et couvre uniquement les notions de base du fonctionnement de la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="7b514-479">Pour une description détaillée de la génération d’URL, consultez [Routage](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="7b514-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="7b514-480">L' <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface est l’élément sous-jacent de l’infrastructure entre MVC et le routage pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="7b514-481">Une instance de `IUrlHelper` est disponible via la `Url` propriété dans les contrôleurs, les vues et les composants de vue.</span><span class="sxs-lookup"><span data-stu-id="7b514-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="7b514-482">Dans l’exemple suivant, l' `IUrlHelper` interface est utilisée par le biais de la `Controller.Url` propriété pour générer une URL vers une autre action.</span><span class="sxs-lookup"><span data-stu-id="7b514-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="7b514-483">Si l’application utilise l’itinéraire conventionnel par défaut, la valeur de la `url` variable est la chaîne de chemin d’URL `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="7b514-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="7b514-484">Ce chemin d’URL est créé par le routage en combinant :</span><span class="sxs-lookup"><span data-stu-id="7b514-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="7b514-485">Valeurs d’itinéraire de la requête actuelle, qui sont appelées **valeurs ambiantes** .</span><span class="sxs-lookup"><span data-stu-id="7b514-485">The route values from the current request, which are called **ambient values** .</span></span>
* <span data-ttu-id="7b514-486">Valeurs passées à `Url.Action` et substituant ces valeurs dans le modèle de routage :</span><span class="sxs-lookup"><span data-stu-id="7b514-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="7b514-487">La valeur de chaque paramètre de route du modèle de route est remplacée en établissant une correspondance avec les valeurs et les valeurs ambiantes.</span><span class="sxs-lookup"><span data-stu-id="7b514-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="7b514-488">Un paramètre d’itinéraire qui n’a pas de valeur peut :</span><span class="sxs-lookup"><span data-stu-id="7b514-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="7b514-489">Utilisez une valeur par défaut s’il en a une.</span><span class="sxs-lookup"><span data-stu-id="7b514-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="7b514-490">Être ignoré s’il est facultatif.</span><span class="sxs-lookup"><span data-stu-id="7b514-490">Be skipped if it's optional.</span></span> <span data-ttu-id="7b514-491">Par exemple, `id` à partir du modèle de routage `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="7b514-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="7b514-492">La génération d’URL échoue si un paramètre d’itinéraire requis n’a pas de valeur correspondante.</span><span class="sxs-lookup"><span data-stu-id="7b514-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="7b514-493">Si la génération d’URL échoue pour une route, la route suivante est essayée, ceci jusqu’à ce que toutes les routes aient été essayées ou qu’une correspondance soit trouvée.</span><span class="sxs-lookup"><span data-stu-id="7b514-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="7b514-494">L’exemple précédent de `Url.Action` suppose un [routage conventionnel](#cr).</span><span class="sxs-lookup"><span data-stu-id="7b514-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="7b514-495">La génération d’URL fonctionne de la même manière avec le [routage des attributs](#ar), bien que les concepts soient différents.</span><span class="sxs-lookup"><span data-stu-id="7b514-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="7b514-496">Avec routage conventionnel :</span><span class="sxs-lookup"><span data-stu-id="7b514-496">With conventional routing:</span></span>

* <span data-ttu-id="7b514-497">Les valeurs de route sont utilisées pour développer un modèle.</span><span class="sxs-lookup"><span data-stu-id="7b514-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="7b514-498">Les valeurs de route pour `controller` et `action` apparaissent généralement dans ce modèle.</span><span class="sxs-lookup"><span data-stu-id="7b514-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="7b514-499">Cela fonctionne parce que les URL mises en correspondance par le routage adhèrent à une convention.</span><span class="sxs-lookup"><span data-stu-id="7b514-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="7b514-500">L’exemple suivant utilise le routage d’attributs :</span><span class="sxs-lookup"><span data-stu-id="7b514-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="7b514-501">L' `Source` action dans le code précédent génère `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="7b514-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="7b514-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> a été ajouté dans ASP.NET Core 3,0 comme alternative à `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="7b514-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="7b514-503">`LinkGenerator` offre des fonctionnalités similaires mais plus flexibles.</span><span class="sxs-lookup"><span data-stu-id="7b514-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="7b514-504">Chaque méthode sur `IUrlHelper` a également une famille de méthodes correspondante `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="7b514-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="7b514-505">Génération des URL par nom d’action</span><span class="sxs-lookup"><span data-stu-id="7b514-505">Generating URLs by action name</span></span>

<span data-ttu-id="7b514-506">[URL. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)et toutes les surcharges associées sont toutes conçues pour générer le point de terminaison cible en spécifiant un nom de contrôleur et un nom d’action.</span><span class="sxs-lookup"><span data-stu-id="7b514-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="7b514-507">Lors de l’utilisation `Url.Action` de, les valeurs d’itinéraire actuelles pour `controller` et `action` sont fournies par le Runtime :</span><span class="sxs-lookup"><span data-stu-id="7b514-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="7b514-508">La valeur de `controller` et `action` font partie des valeurs et valeurs [ambiantes](#ambient) .</span><span class="sxs-lookup"><span data-stu-id="7b514-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="7b514-509">La méthode `Url.Action` utilise toujours les valeurs actuelles de `action` et `controller` et génère un chemin d’accès d’URL qui achemine vers l’action actuelle.</span><span class="sxs-lookup"><span data-stu-id="7b514-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="7b514-510">Le routage tente d’utiliser les valeurs des valeurs ambiantes pour renseigner les informations qui n’ont pas été fournies lors de la génération d’une URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="7b514-511">Prenons l’exemple d’un itinéraire `{a}/{b}/{c}/{d}` avec des valeurs ambiantes `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="7b514-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="7b514-512">Le routage dispose de suffisamment d’informations pour générer une URL sans aucune valeur supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="7b514-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="7b514-513">Le routage a suffisamment d’informations, car tous les paramètres d’itinéraire ont une valeur.</span><span class="sxs-lookup"><span data-stu-id="7b514-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="7b514-514">Si la valeur `{ d = Donovan }` est ajoutée :</span><span class="sxs-lookup"><span data-stu-id="7b514-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="7b514-515">La valeur `{ d = David }` est ignorée.</span><span class="sxs-lookup"><span data-stu-id="7b514-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="7b514-516">Le chemin d’URL généré est `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="7b514-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="7b514-517">**Avertissement** : les chemins d’accès d’URL sont hiérarchiques.</span><span class="sxs-lookup"><span data-stu-id="7b514-517">**Warning** : URL paths are hierarchical.</span></span> <span data-ttu-id="7b514-518">Dans l’exemple précédent, si la valeur `{ c = Cheryl }` est ajoutée :</span><span class="sxs-lookup"><span data-stu-id="7b514-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="7b514-519">Les deux valeurs `{ c = Carol, d = David }` sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="7b514-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="7b514-520">Il n’y a plus de valeur pour `d` et la génération d’URL échoue.</span><span class="sxs-lookup"><span data-stu-id="7b514-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="7b514-521">Les valeurs souhaitées de `c` et `d` doivent être spécifiées pour générer une URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="7b514-522">Vous pouvez vous attendre à rencontrer ce problème avec l’itinéraire par défaut `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="7b514-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="7b514-523">Ce problème est rare dans la pratique, car `Url.Action` spécifie toujours explicitement une `controller` `action` valeur et.</span><span class="sxs-lookup"><span data-stu-id="7b514-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="7b514-524">Plusieurs surcharges d' [URL. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) acceptent un objet de valeurs d’itinéraire pour fournir des valeurs pour les paramètres de routage autres que `controller` et `action` .</span><span class="sxs-lookup"><span data-stu-id="7b514-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="7b514-525">L’objet de valeurs d’itinéraire est fréquemment utilisé avec `id` .</span><span class="sxs-lookup"><span data-stu-id="7b514-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="7b514-526">Par exemple : `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="7b514-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="7b514-527">Objet de valeurs d’itinéraire :</span><span class="sxs-lookup"><span data-stu-id="7b514-527">The route values object:</span></span>

* <span data-ttu-id="7b514-528">Par Convention, est généralement un objet de type anonyme.</span><span class="sxs-lookup"><span data-stu-id="7b514-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="7b514-529">Il peut s’agir d’un `IDictionary<>` ou d’un [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="7b514-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="7b514-530">Toutes les valeurs de route supplémentaires qui ne correspondent pas aux paramètres de route sont placées dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="7b514-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="7b514-531">Le code précédent génère `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="7b514-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="7b514-532">Le code suivant génère une URL absolue :</span><span class="sxs-lookup"><span data-stu-id="7b514-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="7b514-533">Pour créer une URL absolue, utilisez l’une des options suivantes :</span><span class="sxs-lookup"><span data-stu-id="7b514-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="7b514-534">Surcharge qui accepte un `protocol` .</span><span class="sxs-lookup"><span data-stu-id="7b514-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="7b514-535">Par exemple, le code précédent.</span><span class="sxs-lookup"><span data-stu-id="7b514-535">For example, the preceding code.</span></span>
* <span data-ttu-id="7b514-536">[LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), qui génère des URI absolus par défaut.</span><span class="sxs-lookup"><span data-stu-id="7b514-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="7b514-537">Générer des URL par itinéraire</span><span class="sxs-lookup"><span data-stu-id="7b514-537">Generate URLs by route</span></span>

<span data-ttu-id="7b514-538">Le code précédent a démontré la génération d’une URL en passant le nom du contrôleur et de l’action.</span><span class="sxs-lookup"><span data-stu-id="7b514-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="7b514-539">`IUrlHelper` fournit également la famille de méthodes [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) .</span><span class="sxs-lookup"><span data-stu-id="7b514-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="7b514-540">Ces méthodes sont similaires à [URL. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), mais elles ne copient pas les valeurs actuelles de `action` et `controller` vers les valeurs de route.</span><span class="sxs-lookup"><span data-stu-id="7b514-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="7b514-541">L’utilisation la plus courante de `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="7b514-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="7b514-542">Spécifie un nom d’itinéraire pour générer l’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="7b514-543">Ne spécifie généralement pas un nom de contrôleur ou d’action.</span><span class="sxs-lookup"><span data-stu-id="7b514-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="7b514-544">Le :::no-loc(Razor)::: fichier suivant génère un lien HTML vers `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="7b514-544">The following :::no-loc(Razor)::: file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a><span data-ttu-id="7b514-545">Générer des URL en HTML et :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="7b514-545">Generate URLs in HTML and :::no-loc(Razor):::</span></span>

<span data-ttu-id="7b514-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> fournit les <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> méthodes [html. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) et [html. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) pour générer `<form>` des `<a>` éléments et respectivement.</span><span class="sxs-lookup"><span data-stu-id="7b514-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="7b514-547">Ces méthodes utilisent la méthode [URL. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) pour générer une URL et elles acceptent des arguments similaires.</span><span class="sxs-lookup"><span data-stu-id="7b514-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="7b514-548">Les pendants de `Url.RouteUrl` pour `HtmlHelper` sont `Html.BeginRouteForm` et `Html.RouteLink`, qui ont des fonctionnalités similaires.</span><span class="sxs-lookup"><span data-stu-id="7b514-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="7b514-549">Les TagHelpers génèrent des URL via le TagHelper `form` et le TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="7b514-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="7b514-550">Ils utilisent tous les deux `IUrlHelper` pour leur implémentation.</span><span class="sxs-lookup"><span data-stu-id="7b514-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="7b514-551">Pour plus d’informations, consultez [tag Helpers in Forms](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="7b514-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="7b514-552">Dans les vues, `IUrlHelper` est disponible via la propriété `Url` pour toute génération d’URL ad hoc non couverte par ce qui figure ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="7b514-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="7b514-553">Génération d’URL dans les résultats d’action</span><span class="sxs-lookup"><span data-stu-id="7b514-553">URL generation in Action Results</span></span>

<span data-ttu-id="7b514-554">Les exemples précédents ont montré l’utilisation `IUrlHelper` de dans un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7b514-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="7b514-555">L’utilisation la plus courante dans un contrôleur consiste à générer une URL dans le cadre d’un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="7b514-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="7b514-556">Les classes de base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> et <xref:Microsoft.AspNetCore.Mvc.Controller> fournissent des méthodes pratiques pour les résultats d’action qui référencent une autre action.</span><span class="sxs-lookup"><span data-stu-id="7b514-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="7b514-557">Une utilisation classique consiste à effectuer une redirection après avoir accepté l’entrée d’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="7b514-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="7b514-558">Les méthodes de fabrique des résultats d’action, telles que <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> et, <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> suivent un modèle similaire aux méthodes sur `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="7b514-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="7b514-559">Cas spécial pour les routes conventionnelles dédiées</span><span class="sxs-lookup"><span data-stu-id="7b514-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="7b514-560">Le [routage conventionnel](#cr) peut utiliser un type spécial de définition d’itinéraire appelé [itinéraire conventionnel dédié](#dcr).</span><span class="sxs-lookup"><span data-stu-id="7b514-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="7b514-561">Dans l’exemple suivant, l’itinéraire nommé `blog` est une route conventionnelle dédiée :</span><span class="sxs-lookup"><span data-stu-id="7b514-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="7b514-562">À l’aide des définitions d’itinéraire précédentes, `Url.Action("Index", "Home")` génère le chemin d’accès de l’URL `/` à l’aide de l' `default` itinéraire, mais pourquoi ?</span><span class="sxs-lookup"><span data-stu-id="7b514-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="7b514-563">Vous pouvez deviner que les valeurs de route `{ controller = Home, action = Index }` seraient suffisantes pour générer une URL avec `blog`, et que le résultat serait `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="7b514-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="7b514-564">Les [itinéraires conventionnels dédiés](#dcr) s’appuient sur un comportement spécial des valeurs par défaut qui n’ont pas de paramètre de routage correspondant qui empêche l’itinéraire d’être trop [gourmand](xref:fundamentals/routing#greedy) en génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="7b514-565">Dans ce cas, les valeurs par défaut sont `{ controller = Blog, action = Article }`, et ni `controller` ni `action` n’apparaissent comme paramètre de route.</span><span class="sxs-lookup"><span data-stu-id="7b514-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="7b514-566">Quand le routage effectue une génération d’URL, les valeurs fournies doivent correspondre aux valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="7b514-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="7b514-567">La génération d’URL avec `blog` échoue car les valeurs `{ controller = Home, action = Index }` ne correspondent pas `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="7b514-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="7b514-568">Le routage essaye alors d’utiliser `default`, ce qui réussit.</span><span class="sxs-lookup"><span data-stu-id="7b514-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="7b514-569">Zones (Areas)</span><span class="sxs-lookup"><span data-stu-id="7b514-569">Areas</span></span>

<span data-ttu-id="7b514-570">Les [zones](xref:mvc/controllers/areas) sont une fonctionnalité MVC utilisée pour organiser les fonctionnalités associées dans un groupe sous la forme d’un groupe distinct :</span><span class="sxs-lookup"><span data-stu-id="7b514-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="7b514-571">Espace de noms de routage pour les actions du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7b514-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="7b514-572">Structure de dossiers pour les vues.</span><span class="sxs-lookup"><span data-stu-id="7b514-572">Folder structure for views.</span></span>

<span data-ttu-id="7b514-573">L’utilisation de zones permet à une application d’avoir plusieurs contrôleurs portant le même nom, à condition qu’ils aient des zones différentes.</span><span class="sxs-lookup"><span data-stu-id="7b514-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="7b514-574">L’utilisation de zones crée une hiérarchie qui permet le routage par ajout d’un autre paramètre de route, `area`, à `controller` et à `action`.</span><span class="sxs-lookup"><span data-stu-id="7b514-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="7b514-575">Cette section explique comment le routage interagit avec les zones.</span><span class="sxs-lookup"><span data-stu-id="7b514-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="7b514-576">Pour plus d’informations sur la façon dont les zones sont utilisées avec les vues, consultez [zones](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="7b514-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="7b514-577">L’exemple suivant configure MVC pour utiliser l’itinéraire conventionnel par défaut et un `area` itinéraire pour un `area` nommé `Blog` :</span><span class="sxs-lookup"><span data-stu-id="7b514-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="7b514-578">Dans le code précédent, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> est appelé pour créer le `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="7b514-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="7b514-579">Le deuxième paramètre, `"Blog"` , est le nom de la zone.</span><span class="sxs-lookup"><span data-stu-id="7b514-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="7b514-580">En cas de correspondance avec un chemin d’URL comme `/Manage/Users/AddUser` , l' `"blog_route"` itinéraire génère les valeurs d’itinéraire `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="7b514-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="7b514-581">La `area` valeur de route est produite par une valeur par défaut pour `area` .</span><span class="sxs-lookup"><span data-stu-id="7b514-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="7b514-582">L’itinéraire créé par `MapAreaControllerRoute` est équivalent à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="7b514-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="7b514-583">`MapAreaControllerRoute` crée une route avec à la fois une valeur par défaut et une contrainte pour `area` en utilisant le nom de la zone fournie, dans ce cas `Blog`.</span><span class="sxs-lookup"><span data-stu-id="7b514-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="7b514-584">La valeur par défaut garantit que la route produit toujours `{ area = Blog, ... }`, et la contrainte nécessite la valeur `{ area = Blog, ... }` pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="7b514-585">Le routage conventionnel est dépendant de l’ordre.</span><span class="sxs-lookup"><span data-stu-id="7b514-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7b514-586">En général, les itinéraires avec des zones doivent être placés plus tôt, car ils sont plus spécifiques que les itinéraires sans zone.</span><span class="sxs-lookup"><span data-stu-id="7b514-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="7b514-587">À l’aide de l’exemple précédent, les valeurs d’itinéraire `{ area = Blog, controller = Users, action = AddUser }` correspondent à l’action suivante :</span><span class="sxs-lookup"><span data-stu-id="7b514-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="7b514-588">L’attribut [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) désigne un contrôleur dans le cadre d’une zone.</span><span class="sxs-lookup"><span data-stu-id="7b514-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="7b514-589">Ce contrôleur est dans la `Blog` zone.</span><span class="sxs-lookup"><span data-stu-id="7b514-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="7b514-590">Les contrôleurs sans `[Area]` attribut ne sont pas membres d’une zone et ne correspondent **pas** lorsque la valeur de routage `area` est fournie par le routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="7b514-591">Dans l’exemple suivant, seul le premier contrôleur répertorié peut correspondre aux valeurs de route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="7b514-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="7b514-592">L’espace de noms de chaque contrôleur est indiqué ici par exhaustivité.</span><span class="sxs-lookup"><span data-stu-id="7b514-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="7b514-593">Si les contrôleurs précédents utilisent le même espace de noms, une erreur de compilateur est générée.</span><span class="sxs-lookup"><span data-stu-id="7b514-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="7b514-594">Les espaces de noms de classe n’ont pas d’effet sur le routage de MVC.</span><span class="sxs-lookup"><span data-stu-id="7b514-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="7b514-595">Les deux premiers contrôleurs sont membres de zones, et ils sont trouvés en correspondance seulement quand le nom de leur zone respective est fourni par la valeur de route `area`.</span><span class="sxs-lookup"><span data-stu-id="7b514-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="7b514-596">Le troisième contrôleur n’est membre d’aucune zone et peut être trouvé en correspondance seulement quand aucune valeur pour `area` n’est fournie par le routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="7b514-597">En termes de mise en correspondance avec *aucune valeur* , l’absence de la valeur `area` est identique à une valeur null ou de chaîne vide pour `area`.</span><span class="sxs-lookup"><span data-stu-id="7b514-597">In terms of matching *no value* , the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="7b514-598">Lors de l’exécution d’une action à l’intérieur d’une zone, la valeur de route pour `area` est disponible en tant que [valeur ambiante](#ambient) pour le routage à utiliser pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="7b514-599">Cela signifie que par défaut, les zones agissent *par attraction* pour la génération d’URL, comme le montre l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="7b514-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="7b514-600">Le code suivant génère une URL vers `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="7b514-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="7b514-601">Définition de l’action</span><span class="sxs-lookup"><span data-stu-id="7b514-601">Action definition</span></span>

<span data-ttu-id="7b514-602">Les méthodes publiques sur un contrôleur, à l’exception de celles avec l’attribut non- [action](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , sont des actions.</span><span class="sxs-lookup"><span data-stu-id="7b514-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="7b514-603">Exemple de code</span><span class="sxs-lookup"><span data-stu-id="7b514-603">Sample code</span></span>

 * <span data-ttu-id="7b514-604">La méthode [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) est incluse dans l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) et est utilisée pour afficher des informations de routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-604">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="7b514-605">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7b514-605">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7b514-606">ASP.NET Core MVC utilise [l’intergiciel](xref:fundamentals/middleware/index) de routage pour mettre en correspondance les URL des requêtes entrantes et les mapper à des actions.</span><span class="sxs-lookup"><span data-stu-id="7b514-606">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="7b514-607">Les routes sont définies dans le code de démarrage ou dans des attributs.</span><span class="sxs-lookup"><span data-stu-id="7b514-607">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="7b514-608">Les routes décrivent comment les chemins des URL doivent être mis en correspondance avec les actions.</span><span class="sxs-lookup"><span data-stu-id="7b514-608">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="7b514-609">Les routes sont également utilisées pour générer des URL (pour les liens) envoyés dans les réponses.</span><span class="sxs-lookup"><span data-stu-id="7b514-609">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="7b514-610">Les actions sont routées de façon conventionnelle ou routées par attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-610">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7b514-611">Le fait de placer une route sur le contrôleur ou sur l’action les rend « routés par attribut ».</span><span class="sxs-lookup"><span data-stu-id="7b514-611">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7b514-612">Pour plus d’informations, consultez [Routage mixte](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="7b514-612">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="7b514-613">Ce document explique les interactions entre MVC et le routage, et comment les applications MVC classiques utilisent les fonctionnalités du routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-613">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="7b514-614">Pour plus d’informations sur le routage avancé, consultez [Routage](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="7b514-614">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="7b514-615">Configuration de l’intergiciel de routage</span><span class="sxs-lookup"><span data-stu-id="7b514-615">Setting up Routing Middleware</span></span>

<span data-ttu-id="7b514-616">Dans votre méthode *Configure* , vous pouvez voir un code similaire à celui-ci :</span><span class="sxs-lookup"><span data-stu-id="7b514-616">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7b514-617">À l’intérieur de l’appel à `UseMvc`, `MapRoute` est utilisé pour créer une route, nous allons appeler route `default`.</span><span class="sxs-lookup"><span data-stu-id="7b514-617">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="7b514-618">La plupart des applications MVC utilisent une route avec un modèle similaire à la route `default`.</span><span class="sxs-lookup"><span data-stu-id="7b514-618">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="7b514-619">Le modèle de route `"{controller=Home}/{action=Index}/{id?}"` peut correspondre à un chemin d’URL comme `/Products/Details/5` et il extrait les valeurs `{ controller = Products, action = Details, id = 5 }` de la route en décomposant le chemin en jetons.</span><span class="sxs-lookup"><span data-stu-id="7b514-619">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="7b514-620">MVC tente de trouver un contrôleur nommé `ProductsController` et d’exécuter l’action `Details` :</span><span class="sxs-lookup"><span data-stu-id="7b514-620">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="7b514-621">Notez que dans cet exemple, la liaison de modèle utilise la valeur de `id = 5` pour définir le paramètre `id` sur `5` lors de l’appel de cette action.</span><span class="sxs-lookup"><span data-stu-id="7b514-621">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="7b514-622">Pour plus d’informations, consultez [Liaison de modèle](../models/model-binding.md).</span><span class="sxs-lookup"><span data-stu-id="7b514-622">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="7b514-623">Utilisation de la route `default` :</span><span class="sxs-lookup"><span data-stu-id="7b514-623">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="7b514-624">Le modèle de route :</span><span class="sxs-lookup"><span data-stu-id="7b514-624">The route template:</span></span>

* <span data-ttu-id="7b514-625">`{controller=Home}` définit `Home` comme `controller` par défaut.</span><span class="sxs-lookup"><span data-stu-id="7b514-625">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="7b514-626">`{action=Index}` définit `Index` comme `action` par défaut.</span><span class="sxs-lookup"><span data-stu-id="7b514-626">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="7b514-627">`{id?}` définit `id` comme étant facultatif.</span><span class="sxs-lookup"><span data-stu-id="7b514-627">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="7b514-628">Les paramètres de route par défaut et facultatifs n’ont pas besoin d’être présents dans le chemin d’URL pour qu’une correspondance soit établie.</span><span class="sxs-lookup"><span data-stu-id="7b514-628">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="7b514-629">Pour une description détaillée de la syntaxe du modèle de route, consultez [Informations de référence sur le modèle de route](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="7b514-629">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="7b514-630">`"{controller=Home}/{action=Index}/{id?}"` peut correspondre au chemin d’URL `/` et produit les valeurs de route `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="7b514-630">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="7b514-631">Les valeurs de `controller` et `action` utilisent les valeurs par défaut, et `id` ne produit pas de valeur, car il n’existe pas de segment correspondant dans le chemin d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-631">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="7b514-632">MVC utilisez ces valeurs de route pour sélectionner `HomeController` et l’action `Index` :</span><span class="sxs-lookup"><span data-stu-id="7b514-632">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="7b514-633">Avec cette définition de contrôleur et ce modèle de route, l’action `HomeController.Index` est exécutée pour tous les chemins d’URL suivants :</span><span class="sxs-lookup"><span data-stu-id="7b514-633">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="7b514-634">La méthode pratique `UseMvcWithDefaultRoute` :</span><span class="sxs-lookup"><span data-stu-id="7b514-634">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="7b514-635">Peut être utilisée pour remplacer :</span><span class="sxs-lookup"><span data-stu-id="7b514-635">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7b514-636">`UseMvc` et `UseMvcWithDefaultRoute` ajoutent une instance de `RouterMiddleware` au pipeline de l’intergiciel.</span><span class="sxs-lookup"><span data-stu-id="7b514-636">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="7b514-637">MVC n’interagit pas directement avec l’intergiciel et utilise le routage pour gérer les requêtes.</span><span class="sxs-lookup"><span data-stu-id="7b514-637">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="7b514-638">MVC est connecté aux routes via une instance de `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="7b514-638">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="7b514-639">Le code de `UseMvc` est similaire à ceci :</span><span class="sxs-lookup"><span data-stu-id="7b514-639">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="7b514-640">`UseMvc` ne définit directement aucune route, il ajoute un espace réservé à la collection de routes pour la route `attribute`.</span><span class="sxs-lookup"><span data-stu-id="7b514-640">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="7b514-641">La surcharge `UseMvc(Action<IRouteBuilder>)` vous permet d’ajouter vos propres routes et prend également en charge le routage par attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-641">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="7b514-642">`UseMvc` et toutes ses variantes ajoutent un espace réservé pour l’attribut route-attribute Routing est toujours disponible, quelle que soit la façon dont vous configurez `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="7b514-642">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="7b514-643">`UseMvcWithDefaultRoute` définit une route par défaut et prend en charge le routage par attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-643">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="7b514-644">La section [Routage par attribut](#attribute-routing-ref-label) comprend plus de détails sur le routage par attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-644">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="7b514-645">Routage conventionnel</span><span class="sxs-lookup"><span data-stu-id="7b514-645">Conventional routing</span></span>

<span data-ttu-id="7b514-646">La route `default` :</span><span class="sxs-lookup"><span data-stu-id="7b514-646">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="7b514-647">Le code précédent est un exemple de routage conventionnel.</span><span class="sxs-lookup"><span data-stu-id="7b514-647">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="7b514-648">Ce style est appelé routage conventionnel, car il établit une *Convention* pour les chemins d’URL :</span><span class="sxs-lookup"><span data-stu-id="7b514-648">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="7b514-649">Le premier segment de chemin d’accès correspond au nom du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7b514-649">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="7b514-650">Le deuxième correspond au nom de l’action.</span><span class="sxs-lookup"><span data-stu-id="7b514-650">The second maps to the action name.</span></span>
* <span data-ttu-id="7b514-651">Le troisième segment est utilisé pour un facultatif `id` .</span><span class="sxs-lookup"><span data-stu-id="7b514-651">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="7b514-652">`id` correspond à une entité de modèle.</span><span class="sxs-lookup"><span data-stu-id="7b514-652">`id` maps to a model entity.</span></span>

<span data-ttu-id="7b514-653">En utilisant cette route `default`, le chemin d’URL `/Products/List` mappe à l’action `ProductsController.List`, et `/Blog/Article/17` mappe à `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="7b514-653">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="7b514-654">Ce mappage est basé **uniquement** sur les noms de contrôleur et d’action ; il n’est pas basé sur les espaces de noms, les emplacements des fichiers sources ou les paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="7b514-654">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="7b514-655">L’utilisation du routage conventionnel avec la route par défaut vous permet de créer rapidement l’application sans avoir à élaborer un nouveau modèle d’URL pour chaque action que vous définissez.</span><span class="sxs-lookup"><span data-stu-id="7b514-655">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="7b514-656">Pour une application avec des actions de style CRUD, le fait de disposer d’une cohérence pour les URL entre vos contrôleurs peut simplifier votre code et rendre votre interface utilisateur plus prévisible.</span><span class="sxs-lookup"><span data-stu-id="7b514-656">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="7b514-657">`id` est défini comme étant facultatif par le modèle de route, ce qui signifie que vos actions peuvent s’exécuter sans l’ID fourni dans le cadre de l’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-657">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="7b514-658">En général, si `id` est omis de l’URL, il est défini sur `0` par la liaison de modèle : aucune entité correspondant à `id == 0` n’est donc trouvée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="7b514-658">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="7b514-659">Le routage par attribut vous donne un contrôle précis pour rendre le code obligatoire pour certaines actions et pas pour d’autres.</span><span class="sxs-lookup"><span data-stu-id="7b514-659">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="7b514-660">Par convention, la documentation inclut des paramètres facultatifs comme `id` quand ils sont susceptibles d’apparaître dans une utilisation correcte.</span><span class="sxs-lookup"><span data-stu-id="7b514-660">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="7b514-661">Routes multiples</span><span class="sxs-lookup"><span data-stu-id="7b514-661">Multiple routes</span></span>

<span data-ttu-id="7b514-662">Vous pouvez ajouter plusieurs routes dans `UseMvc` en ajoutant plusieurs appels à `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="7b514-662">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="7b514-663">Ceci vous permet de définir plusieurs conventions ou d’ajouter des routes conventionnelles qui sont dédiées à une action spécifique, comme :</span><span class="sxs-lookup"><span data-stu-id="7b514-663">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7b514-664">La route `blog` est ici une *route conventionnelle dédiée* , ce qui signifie qu’elle utilise le système de routage conventionnel, mais qu’elle est dédiée à une action spécifique.</span><span class="sxs-lookup"><span data-stu-id="7b514-664">The `blog` route here is a *dedicated conventional route* , meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="7b514-665">Comme `controller` et `action` n’apparaissent pas dans le modèle de route en tant que paramètres, ils peuvent seulement avoir les valeurs par défaut et par conséquent, cette route sera toujours mappée à l’action `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="7b514-665">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="7b514-666">Les routes de la collection de routes sont ordonnées et elles sont traitées dans l’ordre où elles sont ajoutées.</span><span class="sxs-lookup"><span data-stu-id="7b514-666">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="7b514-667">Ainsi, dans cet exemple, la route `blog` est essayée avant la route `default`.</span><span class="sxs-lookup"><span data-stu-id="7b514-667">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="7b514-668">Les *itinéraires conventionnels dédiés* utilisent souvent des paramètres de routage de type **« catch-all »** comme `{*article}` pour capturer la partie restante du chemin d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-668">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="7b514-669">Ceci peut rendre une route « trop globale », ce qui signifie qu’elle correspond à des URL qui devaient être mises en correspondance par d’autres routes.</span><span class="sxs-lookup"><span data-stu-id="7b514-669">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="7b514-670">Pour résoudre ce problème, placez les routes « globales » plus loin dans la table de routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-670">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="7b514-671">Secours</span><span class="sxs-lookup"><span data-stu-id="7b514-671">Fallback</span></span>

<span data-ttu-id="7b514-672">Dans le cadre du traitement des requêtes, MVC vérifie que les valeurs de route peuvent être utilisées pour rechercher un contrôleur et une action dans votre application.</span><span class="sxs-lookup"><span data-stu-id="7b514-672">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="7b514-673">Si les valeurs de route ne correspondent pas à une action, la route n’est pas considérée comme une correspondance, et la route suivante est essayée.</span><span class="sxs-lookup"><span data-stu-id="7b514-673">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="7b514-674">Ceci est appelé *processus de repli* et est conçu pour simplifier les cas où des routes conventionnelles se chevauchent.</span><span class="sxs-lookup"><span data-stu-id="7b514-674">This is called *fallback* , and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="7b514-675">Résolution des ambiguïtés pour les actions</span><span class="sxs-lookup"><span data-stu-id="7b514-675">Disambiguating actions</span></span>

<span data-ttu-id="7b514-676">Quand deux actions correspondent via le routage, MVC doit résoudre l’ambiguïté pour choisir le « meilleur » candidat ou sinon lever une exception.</span><span class="sxs-lookup"><span data-stu-id="7b514-676">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="7b514-677">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7b514-677">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="7b514-678">Ce contrôleur définit deux actions qui correspondraient au chemin d’URL `/Products/Edit/17` et les données de routage `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="7b514-678">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="7b514-679">Il s’agit d’un modèle classique pour les contrôleurs MVC, où `Edit(int)` montre un formulaire pour modifier un produit, et où `Edit(int, Product)` traite le formulaire envoyé.</span><span class="sxs-lookup"><span data-stu-id="7b514-679">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="7b514-680">Pour rendre cela possible, MVC doit choisir `Edit(int, Product)` quand la requête est `POST` HTTP, et `Edit(int)` quand le verbe HTTP est autre chose.</span><span class="sxs-lookup"><span data-stu-id="7b514-680">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="7b514-681">`HttpPostAttribute` (`[HttpPost]`) est une implémentation de `IActionConstraint` qui permet la sélection de l’action seulement quand le verbe HTTP est `POST`.</span><span class="sxs-lookup"><span data-stu-id="7b514-681">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="7b514-682">La présence d’une `IActionConstraint` fait de `Edit(int, Product)` une correspondance « meilleure » que `Edit(int)`, de sorte que `Edit(int, Product)` est essayé en premier.</span><span class="sxs-lookup"><span data-stu-id="7b514-682">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="7b514-683">Vous devez écrire des implémentations personnalisées de `IActionConstraint` seulement dans des scénarios spécifiques, mais il est important de comprendre le rôle d’attributs comme `HttpPostAttribute` ; des attributs similaires sont définis pour les autres verbes HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b514-683">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="7b514-684">Dans le routage conventionnel, il est courant que des actions utilisent un même nom d’action quand elles font partie d’un flux de travail `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="7b514-684">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="7b514-685">L’avantage de ce modèle devient plus évident après la lecture de la section [Présentation d’IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="7b514-685">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="7b514-686">Si plusieurs routes correspondent et que MVC ne peut pas trouver une « meilleure » route, elle lève une `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="7b514-686">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="7b514-687">Noms des routes</span><span class="sxs-lookup"><span data-stu-id="7b514-687">Route names</span></span>

<span data-ttu-id="7b514-688">Les chaînes `"blog"` et `"default"` dans les exemples suivants sont des noms de routes :</span><span class="sxs-lookup"><span data-stu-id="7b514-688">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7b514-689">Les noms de routes donnent aux routes des noms logiques : le nom de route peut ainsi être utilisé pour la génération des URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-689">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="7b514-690">Ceci simplifie considérablement la création d’URL quand l’ordonnancement des routes peut rendre compliquée la génération des URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-690">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="7b514-691">Les noms de routes doivent être unique à l’échelle de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b514-691">Route names must be unique application-wide.</span></span>

<span data-ttu-id="7b514-692">Les noms de routes n’ont pas d’impact sur la correspondance des URL ni sur le traitement des requêtes ; ils sont utilisés seulement pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-692">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="7b514-693">La section [Routage](xref:fundamentals/routing) contient des informations plus détaillées sur la génération d’URL, notamment la génération d’URL dans les helpers spécifiques à MVC.</span><span class="sxs-lookup"><span data-stu-id="7b514-693">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="7b514-694">Routage par attributs</span><span class="sxs-lookup"><span data-stu-id="7b514-694">Attribute routing</span></span>

<span data-ttu-id="7b514-695">Le routage par attributs utilise un ensemble d’attributs pour mapper les actions directement aux modèles de routes.</span><span class="sxs-lookup"><span data-stu-id="7b514-695">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="7b514-696">Dans l’exemple suivant, `app.UseMvc();` est utilisé dans la méthode `Configure` et aucune route n’est passée.</span><span class="sxs-lookup"><span data-stu-id="7b514-696">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="7b514-697">`HomeController` va trouver des correspondances avec un ensemble d’URL similaires aux correspondances qui seraient trouvées par la route par défaut `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="7b514-697">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="7b514-698">L’action `HomeController.Index()` sera exécutée pour tous les chemins d’URL `/`, `/Home` ou `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="7b514-698">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="7b514-699">Cet exemple met en évidence une différence importante en termes de programmation entre le routage par attributs et le routage conventionnel.</span><span class="sxs-lookup"><span data-stu-id="7b514-699">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="7b514-700">Le routage par attributs nécessite des entrées en plus grand nombre pour spécifier une route ; la route conventionnelle par défaut gère les routes de façon plus succincte.</span><span class="sxs-lookup"><span data-stu-id="7b514-700">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="7b514-701">Cependant, le routage par attributs permet (et nécessite) un contrôle précis des modèles de routes qui s’appliquent à chaque action.</span><span class="sxs-lookup"><span data-stu-id="7b514-701">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="7b514-702">Avec le routage par attributs, le nom du contrôleur et les noms des actions ne jouent **aucun** rôle dans la sélection de l’action.</span><span class="sxs-lookup"><span data-stu-id="7b514-702">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="7b514-703">Cet exemple trouve une correspondance avec les mêmes URL que l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="7b514-703">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="7b514-704">Les modèles de routes ci-dessus ne définissent pas de paramètres de route pour `action`, `area` et `controller`.</span><span class="sxs-lookup"><span data-stu-id="7b514-704">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="7b514-705">En fait, ces paramètres de route ne sont pas autorisés dans les routes par attributs.</span><span class="sxs-lookup"><span data-stu-id="7b514-705">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="7b514-706">Comme le modèle de route est déjà associé à une action, cela n’aurait pas de sens d’analyser le nom de l’action à partir de l’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-706">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="7b514-707">Routage par attributs avec des attributs Http[Verbe]</span><span class="sxs-lookup"><span data-stu-id="7b514-707">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="7b514-708">Le routage par attributs peut aussi utiliser les attributs `Http[Verb]`, comme `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="7b514-708">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="7b514-709">Tous ces attributs peuvent accepter un modèle de route.</span><span class="sxs-lookup"><span data-stu-id="7b514-709">All of these attributes can accept a route template.</span></span> <span data-ttu-id="7b514-710">Cet exemple montre deux actions qui correspondent au même modèle de route :</span><span class="sxs-lookup"><span data-stu-id="7b514-710">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="7b514-711">Pour un chemin d’URL comme `/products`, l’action `ProductsApi.ListProducts` est exécutée quand le verbe HTTP est `GET`, et `ProductsApi.CreateProduct` est exécutée quand le verbe HTTP est `POST`.</span><span class="sxs-lookup"><span data-stu-id="7b514-711">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="7b514-712">Le routage par attributs recherche d’abord une correspondance de l’URL par rapport à l’ensemble des modèles de routes défini par les attributs de la route.</span><span class="sxs-lookup"><span data-stu-id="7b514-712">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="7b514-713">Une fois qu’un modèle de route correspond, les contraintes de `IActionConstraint` sont appliquées pour déterminer quelles actions peuvent être exécutées.</span><span class="sxs-lookup"><span data-stu-id="7b514-713">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="7b514-714">Lors de la génération d’une API REST, il est rare que vous souhaitiez utiliser `[Route(...)]` sur une méthode d’action, car l’action acceptera toutes les méthodes http.</span><span class="sxs-lookup"><span data-stu-id="7b514-714">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="7b514-715">Il est préférable d’utiliser les `Http*Verb*Attributes` plus spécifiques pour plus de précision quant à ce qui est pris en charge par votre API.</span><span class="sxs-lookup"><span data-stu-id="7b514-715">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="7b514-716">Les clients des API REST doivent normalement connaître les chemins et les verbes HTTP qui correspondent à des opérations logiques spécifiques.</span><span class="sxs-lookup"><span data-stu-id="7b514-716">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="7b514-717">Dans la mesure où une route d’attribut s’applique à une action spécifique, il est facile de placer les paramètres nécessaires dans la définition du modèle de route.</span><span class="sxs-lookup"><span data-stu-id="7b514-717">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="7b514-718">Dans cet exemple, `id` est obligatoire dans le chemin d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-718">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7b514-719">L’action `ProductsApi.GetProduct(int)` est exécutée pour un chemin d’URL comme `/products/3`, mais pas pour un chemin d’URL comme `/products`.</span><span class="sxs-lookup"><span data-stu-id="7b514-719">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="7b514-720">Consultez [Routage](xref:fundamentals/routing) pour obtenir une description complète des modèles de routes et des options associées.</span><span class="sxs-lookup"><span data-stu-id="7b514-720">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="7b514-721">Nom des routes</span><span class="sxs-lookup"><span data-stu-id="7b514-721">Route Name</span></span>

<span data-ttu-id="7b514-722">Le code suivant définit un *nom de route*`Products_List` :</span><span class="sxs-lookup"><span data-stu-id="7b514-722">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7b514-723">Les noms de routes peuvent être utilisés pour générer une URL basée sur une route spécifique.</span><span class="sxs-lookup"><span data-stu-id="7b514-723">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="7b514-724">Les noms de routes n’ont pas d’impact sur le comportement de mise en correspondance des URL du routage et ils sont utilisés seulement pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-724">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="7b514-725">Les noms de routes doivent être unique à l’échelle de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b514-725">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="7b514-726">Comparez ceci avec la *route par défaut* conventionnelle, qui définit le paramètre `id` comme étant facultatif (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="7b514-726">Contrast this with the conventional *default route* , which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="7b514-727">Cette possibilité de spécifier les API avec précision présente des avantages, par exemple de permettre de diriger `/products` et `/products/5` vers des actions différentes.</span><span class="sxs-lookup"><span data-stu-id="7b514-727">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="7b514-728">Combinaison de routes</span><span class="sxs-lookup"><span data-stu-id="7b514-728">Combining routes</span></span>

<span data-ttu-id="7b514-729">Pour rendre le routage par attributs moins répétitif, les attributs de route sont combinés avec des attributs de route sur les actions individuelles.</span><span class="sxs-lookup"><span data-stu-id="7b514-729">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="7b514-730">Les modèles de routes définis sur le contrôleur sont ajoutés à des modèles de routes sur les actions.</span><span class="sxs-lookup"><span data-stu-id="7b514-730">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="7b514-731">Placer un attribut de route sur le contrôleur a pour effet que **toutes** les actions du contrôleur utilisent le routage par attributs.</span><span class="sxs-lookup"><span data-stu-id="7b514-731">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7b514-732">Dans cet exemple, le chemin d’URL `/products` peut être mis en correspondance avec `ProductsApi.ListProducts` et le chemin d’URL `/products/5` peut être mis en correspondance avec `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="7b514-732">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="7b514-733">Ces deux actions correspondent uniquement à HTTP `GET` , car elles sont marquées avec le `HttpGetAttribute` .</span><span class="sxs-lookup"><span data-stu-id="7b514-733">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="7b514-734">Les modèles de routes appliqués à une action qui commencent par `/` ou `~/` ne sont pas combinés avec les modèles de routes appliqués au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7b514-734">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="7b514-735">Cet exemple met en correspondance avec un ensemble de chemins d’URL similaires à la *route par défaut* .</span><span class="sxs-lookup"><span data-stu-id="7b514-735">This example matches a set of URL paths similar to the *default route* .</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="7b514-736">Ordonnancement des routes d’attributs</span><span class="sxs-lookup"><span data-stu-id="7b514-736">Ordering attribute routes</span></span>

<span data-ttu-id="7b514-737">Contrairement aux itinéraires conventionnels, qui s’exécutent dans un ordre défini, le routage d’attributs génère une arborescence et met en correspondance tous les itinéraires simultanément.</span><span class="sxs-lookup"><span data-stu-id="7b514-737">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="7b514-738">Il se comporte comme-si les entrées des routes étaient placées dans un ordre idéal ; les routes les plus spécifiques ont ainsi plus de probabilités de s’exécuter avant les routes plus générales.</span><span class="sxs-lookup"><span data-stu-id="7b514-738">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="7b514-739">Par exemple, une route comme `blog/search/{topic}` est plus spécifique qu’une route comme `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="7b514-739">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="7b514-740">D’un point de vue logique, la route `blog/search/{topic}` « s’exécute » par défaut en premier, car il s’agit du seul ordre sensé.</span><span class="sxs-lookup"><span data-stu-id="7b514-740">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="7b514-741">Avec le routage conventionnel, le développeur est responsable du placement des routes dans l’ordre souhaité.</span><span class="sxs-lookup"><span data-stu-id="7b514-741">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="7b514-742">Les routes d’attribut peuvent configurer un ordre en utilisant la propriété `Order` de tous les attributs de route fournis par le framework.</span><span class="sxs-lookup"><span data-stu-id="7b514-742">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="7b514-743">Les routes sont traitées selon un ordre croissant de la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="7b514-743">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="7b514-744">L’ordre par défaut est `0`.</span><span class="sxs-lookup"><span data-stu-id="7b514-744">The default order is `0`.</span></span> <span data-ttu-id="7b514-745">La définition d’une route avec `Order = -1` fait que cette route « s’exécute » avant les routes qui ne définissent pas d’ordre.</span><span class="sxs-lookup"><span data-stu-id="7b514-745">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="7b514-746">La définition d’une route avec `Order = 1` fait que cette route « s’exécute » après l’ordre des routes par défaut.</span><span class="sxs-lookup"><span data-stu-id="7b514-746">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="7b514-747">Évitez de dépendre de `Order`.</span><span class="sxs-lookup"><span data-stu-id="7b514-747">Avoid depending on `Order`.</span></span> <span data-ttu-id="7b514-748">Si votre espace d’URL nécessite des valeurs d’ordre explicites pour router correctement, il est probable qu’il prête également à confusion pour les clients.</span><span class="sxs-lookup"><span data-stu-id="7b514-748">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="7b514-749">D’une façon générale, le routage par attributs sélectionne la route correcte avec la mise en correspondance d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-749">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="7b514-750">Si l’ordre par défaut utilisé pour la génération d’URL ne fonctionne pas, l’utilisation à titre de remplacement d’un nom de route est généralement plus simple que d’appliquer la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="7b514-750">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="7b514-751">:::no-loc(Razor)::: Le routage des pages et le routage du contrôleur MVC partagent une implémentation.</span><span class="sxs-lookup"><span data-stu-id="7b514-751">:::no-loc(Razor)::: Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="7b514-752">Des informations sur l’ordre des itinéraires dans les :::no-loc(Razor)::: rubriques pages sont disponibles à la [ :::no-loc(Razor)::: page conventions de routage et d’application : ordre de routage](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="7b514-752">Information on route order in the :::no-loc(Razor)::: Pages topics is available at [:::no-loc(Razor)::: Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="7b514-753">Remplacement de jetons dans les modèles de routes ([contrôleur], [action], [zone])</span><span class="sxs-lookup"><span data-stu-id="7b514-753">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="7b514-754">Pour plus de commodité, les itinéraires d’attributs prennent en charge le *remplacement de jetons* en plaçant un jeton entre crochets ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="7b514-754">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="7b514-755">Les jetons `[action]`, `[area]` et `[controller]` sont remplacés par les valeurs du nom d’action, du nom de la zone et du nom du contrôleur de l’action où la route est définie.</span><span class="sxs-lookup"><span data-stu-id="7b514-755">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="7b514-756">Dans l’exemple suivant, les actions correspondent aux chemins d’URL comme décrit dans les commentaires :</span><span class="sxs-lookup"><span data-stu-id="7b514-756">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="7b514-757">Le remplacement des jetons se produit à la dernière étape de la création des routes d’attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-757">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="7b514-758">L’exemple ci-dessus se comporte comme le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7b514-758">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="7b514-759">Les routes d’attribut peuvent aussi être combinées avec l’héritage.</span><span class="sxs-lookup"><span data-stu-id="7b514-759">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="7b514-760">Combiné avec le remplacement de jetons, c’est particulièrement puissant.</span><span class="sxs-lookup"><span data-stu-id="7b514-760">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="7b514-761">Le remplacement des jetons s’applique aussi aux noms de routes définis par des routes d’attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-761">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="7b514-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` génère un nom de route unique pour chaque action.</span><span class="sxs-lookup"><span data-stu-id="7b514-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="7b514-763">Pour faire correspondre le délimiteur littéral de remplacement de jetons `[` ou `]`, placez-le en échappement en répétant le caractère (`[[` ou `]]`).</span><span class="sxs-lookup"><span data-stu-id="7b514-763">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="7b514-764">Utiliser un transformateur de paramètre pour personnaliser le remplacement des jetons</span><span class="sxs-lookup"><span data-stu-id="7b514-764">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="7b514-765">Le remplacement des jetons peut être personnalisé à l’aide d’un transformateur de paramètre.</span><span class="sxs-lookup"><span data-stu-id="7b514-765">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="7b514-766">Un transformateur de paramètre implémente `IOutboundParameterTransformer` et transforme la valeur des paramètres.</span><span class="sxs-lookup"><span data-stu-id="7b514-766">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="7b514-767">Par exemple, un transformateur de paramètre `SlugifyParameterTransformer` personnalisé transforme la valeur de la route `SubscriptionManagement` en `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="7b514-767">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="7b514-768">`RouteTokenTransformerConvention` est une convention de modèle d’application qui :</span><span class="sxs-lookup"><span data-stu-id="7b514-768">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="7b514-769">Applique un transformateur de paramètre à toutes les routes d’attribut dans une application.</span><span class="sxs-lookup"><span data-stu-id="7b514-769">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="7b514-770">Personnalise les valeurs de jeton de route d’attribut quand elles sont remplacées.</span><span class="sxs-lookup"><span data-stu-id="7b514-770">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="7b514-771">`RouteTokenTransformerConvention` est inscrit en tant qu’option dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7b514-771">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="7b514-772">Routes multiples</span><span class="sxs-lookup"><span data-stu-id="7b514-772">Multiple Routes</span></span>

<span data-ttu-id="7b514-773">Le routage par attributs prend en charge la définition de plusieurs routes pour atteindre la même action.</span><span class="sxs-lookup"><span data-stu-id="7b514-773">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="7b514-774">L’utilisation la plus courante de ceci est d’imiter le comportement de la *route conventionnelle par défaut* , comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7b514-774">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="7b514-775">Le fait de placer plusieurs attributs de route sur le contrôleur signifie que chacun d’eux se combine avec chacun des attributs de route sur les méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="7b514-775">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="7b514-776">Quand plusieurs attributs de route (qui implémentent `IActionConstraint`) sont placés sur une action, chaque contrainte d’action se combine avec le modèle de route de l’attribut qui l’a défini.</span><span class="sxs-lookup"><span data-stu-id="7b514-776">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="7b514-777">Si utiliser plusieurs routes sur des actions peut paître puissant, il est préférable de conserver l’espace d’URL de votre application simple et bien définie.</span><span class="sxs-lookup"><span data-stu-id="7b514-777">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="7b514-778">Utilisez plusieurs routes sur les actions seulement là où c’est nécessaire, par exemple pour prendre en charge des clients existants.</span><span class="sxs-lookup"><span data-stu-id="7b514-778">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="7b514-779">Spécification facultative de paramètres, de valeurs par défaut et de contraintes pour les routes d’attribut</span><span class="sxs-lookup"><span data-stu-id="7b514-779">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="7b514-780">Les routes d’attribut prennent en charge la même syntaxe inline que les routes conventionnelles pour spécifier des paramètres, des valeurs par défaut et des contraintes facultatifs.</span><span class="sxs-lookup"><span data-stu-id="7b514-780">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="7b514-781">Pour une description détaillée de la syntaxe du modèle de route, consultez [Informations de référence sur le modèle de route](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="7b514-781">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="7b514-782">Attributs de route personnalisés avec `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="7b514-782">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="7b514-783">Tous les attributs de route fournis dans le framework (`[Route(...)]`, `[HttpGet(...)]`, etc.) implémentent l’interface `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="7b514-783">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="7b514-784">MVC recherche les attributs sur les classes de contrôleur et les méthodes d’action quand l’application démarre et utilise ceux qui implémentent `IRouteTemplateProvider` pour créer l’ensemble initial des routes.</span><span class="sxs-lookup"><span data-stu-id="7b514-784">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="7b514-785">Vous pouvez implémenter `IRouteTemplateProvider` pour définir vos propres attributs de route.</span><span class="sxs-lookup"><span data-stu-id="7b514-785">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="7b514-786">Chaque `IRouteTemplateProvider` vous permet de définir une route avec un modèle, un nom et un ordre de route personnalisés :</span><span class="sxs-lookup"><span data-stu-id="7b514-786">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="7b514-787">L’attribut de l’exemple ci-dessus définit automatiquement `Template` sur `"api/[controller]"` quand `[MyApiController]` est appliqué.</span><span class="sxs-lookup"><span data-stu-id="7b514-787">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="7b514-788">Utilisation d’un modèle d’application pour personnaliser les routes d’attribut</span><span class="sxs-lookup"><span data-stu-id="7b514-788">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="7b514-789">Le *modèle d’application* est un modèle d’objet créé au démarrage avec toutes les métadonnées utilisée par MVC pour router et exécuter vos actions.</span><span class="sxs-lookup"><span data-stu-id="7b514-789">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="7b514-790">Le *modèle d’application* inclut toutes les données collectées à partir des attributs de route (via `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="7b514-790">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="7b514-791">Vous pouvez écrire des *conventions* pour modifier le modèle d’application au moment du démarrage pour personnaliser le comporte du routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-791">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="7b514-792">Cette section montre un exemple simple de personnalisation du routage avec le modèle d’application.</span><span class="sxs-lookup"><span data-stu-id="7b514-792">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="7b514-793">Routage mixte : routage conventionnel et routage par attributs</span><span class="sxs-lookup"><span data-stu-id="7b514-793">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="7b514-794">Les applications MVC peuvent combiner l’utilisation du routage conventionnel et du routage par attributs.</span><span class="sxs-lookup"><span data-stu-id="7b514-794">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="7b514-795">Il est courant d’utiliser des routes conventionnelles pour les contrôleurs délivrant des pages HTML pour les navigateurs, et le routage par attributs pour les contrôleurs délivrant des API REST.</span><span class="sxs-lookup"><span data-stu-id="7b514-795">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="7b514-796">Les actions sont routées de façon conventionnelle ou routées par attribut.</span><span class="sxs-lookup"><span data-stu-id="7b514-796">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7b514-797">Le fait de placer une route sur le contrôleur ou sur l’action les rend « routés par attribut ».</span><span class="sxs-lookup"><span data-stu-id="7b514-797">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7b514-798">Les actions qui définissent des routes d’attribut ne sont pas accessibles via les routes conventionnelles et vice versa.</span><span class="sxs-lookup"><span data-stu-id="7b514-798">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="7b514-799">**Tout** attribut de route sur le contrôleur a pour effet que toutes les actions du contrôleur sont routées par attributs.</span><span class="sxs-lookup"><span data-stu-id="7b514-799">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="7b514-800">Ce qui distingue les deux types de systèmes de routage est le processus appliqué une fois qu’une URL correspond à un modèle de route.</span><span class="sxs-lookup"><span data-stu-id="7b514-800">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="7b514-801">Dans le routage conventionnel, les valeurs de route de la correspondance sont utilisées pour choisir l’action et le contrôleur dans une table de recherche comprenant toutes les actions routées de façon conventionnelle.</span><span class="sxs-lookup"><span data-stu-id="7b514-801">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="7b514-802">Dans le routage par attributs, chaque modèle est déjà associé à une action, et aucune recherche supplémentaire n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7b514-802">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="7b514-803">Segments complexes</span><span class="sxs-lookup"><span data-stu-id="7b514-803">Complex segments</span></span>

<span data-ttu-id="7b514-804">Les segments complexes (par exemple, `[Route("/dog{token}cat")]`), sont traités par la mise en correspondance des littéraux de droite à gauche de manière non gourmande.</span><span class="sxs-lookup"><span data-stu-id="7b514-804">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="7b514-805">Consultez [le code source](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) pour obtenir une description.</span><span class="sxs-lookup"><span data-stu-id="7b514-805">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="7b514-806">Pour plus d’informations, consultez [ce problème](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="7b514-806">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="7b514-807">Génération des URL</span><span class="sxs-lookup"><span data-stu-id="7b514-807">URL Generation</span></span>

<span data-ttu-id="7b514-808">Les applications MVC peuvent utiliser les fonctionnalités de génération d’URL de routage pour générer des liens URL vers des actions.</span><span class="sxs-lookup"><span data-stu-id="7b514-808">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="7b514-809">La génération d’URL élimine le codage en dur des URL, ce qui rend votre code plus robuste et plus facile à maintenir.</span><span class="sxs-lookup"><span data-stu-id="7b514-809">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="7b514-810">Cette section se concentre sur les fonctionnalités de génération d’URL fournies par MVC et couvre seulement les principes de base du fonctionnement de la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-810">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="7b514-811">Pour une description détaillée de la génération d’URL, consultez [Routage](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="7b514-811">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="7b514-812">L’interface `IUrlHelper` est l’élément d’infrastructure sous-jacent entre MVC et le routage pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-812">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="7b514-813">Vous pouvez trouver une instance de `IUrlHelper` disponible via la propriété `Url` dans les composants controllers, views et view.</span><span class="sxs-lookup"><span data-stu-id="7b514-813">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="7b514-814">Dans cet exemple, l’interface `IUrlHelper` est utilisée via la propriété `Controller.Url` pour générer une URL vers une autre action.</span><span class="sxs-lookup"><span data-stu-id="7b514-814">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="7b514-815">Si l’application utilise la route conventionnelle par défaut, la valeur de la variable `url` est la chaîne de chemin d’URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="7b514-815">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="7b514-816">Ce chemin d’URL est créé par le routage en combinant les valeurs de route de la requête en cours (valeurs ambiantes) avec les valeurs passées à `Url.Action`, et en remplaçant les valeurs dans le modèle de route :</span><span class="sxs-lookup"><span data-stu-id="7b514-816">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="7b514-817">La valeur de chaque paramètre de route du modèle de route est remplacée en établissant une correspondance avec les valeurs et les valeurs ambiantes.</span><span class="sxs-lookup"><span data-stu-id="7b514-817">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="7b514-818">Un paramètre de route qui n’a pas de valeur peut utiliser une valeur par défaut s’il en a une, ou il peut être ignoré s’il est facultatif (comme dans le cas de `id` dans cet exemple).</span><span class="sxs-lookup"><span data-stu-id="7b514-818">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="7b514-819">La génération d’URL échoue si un paramètre de route obligatoire n’a pas de valeur correspondante.</span><span class="sxs-lookup"><span data-stu-id="7b514-819">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="7b514-820">Si la génération d’URL échoue pour une route, la route suivante est essayée, ceci jusqu’à ce que toutes les routes aient été essayées ou qu’une correspondance soit trouvée.</span><span class="sxs-lookup"><span data-stu-id="7b514-820">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="7b514-821">L’exemple de `Url.Action` ci-dessus suppose un routage conventionnel, mais la génération d’URL fonctionne de façon similaire avec le routage par attributs, même si les concepts sont différents.</span><span class="sxs-lookup"><span data-stu-id="7b514-821">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="7b514-822">Avec le routage conventionnel, les valeurs de route sont utilisées pour développer un modèle, et les valeurs de route pour `controller` et `action` apparaissent généralement dans ce modèle : ceci fonctionne car les URL mises en correspondance par le routage adhèrent à une *convention* .</span><span class="sxs-lookup"><span data-stu-id="7b514-822">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention* .</span></span> <span data-ttu-id="7b514-823">Dans le routage par attributs, les valeurs de route pour `controller` et `action` ne sont pas autorisées à apparaître dans le modèle : au lieu de cela, elles sont utilisées pour rechercher le modèle à utiliser.</span><span class="sxs-lookup"><span data-stu-id="7b514-823">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="7b514-824">Cet exemple utilise le routage par attributs :</span><span class="sxs-lookup"><span data-stu-id="7b514-824">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="7b514-825">MVC génère une table de recherche de toutes les actions routées par attributs et recherche une correspondance avec les valeurs de `controller` et de `action` pour sélectionner le modèle de route à utiliser pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-825">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="7b514-826">Dans l’exemple ci-dessus, `custom/url/to/destination` est généré.</span><span class="sxs-lookup"><span data-stu-id="7b514-826">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="7b514-827">Génération des URL par nom d’action</span><span class="sxs-lookup"><span data-stu-id="7b514-827">Generating URLs by action name</span></span>

<span data-ttu-id="7b514-828">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="7b514-828">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="7b514-829">`Action`) et toutes les surcharges associées sont tous basés sur cette idée que vous voulez spécifier ce à quoi vous liez en spécifiant un nom de contrôleur et un nom d’action.</span><span class="sxs-lookup"><span data-stu-id="7b514-829">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="7b514-830">Quand vous utilisez `Url.Action`, les valeurs de route actuelles pour `controller` et `action` sont spécifiées pour vous : la valeur de `controller` et de `action` font partie des *valeurs ambiantes* **et des** *valeurs* .</span><span class="sxs-lookup"><span data-stu-id="7b514-830">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values* .</span></span> <span data-ttu-id="7b514-831">La méthode `Url.Action` utilise toujours les valeurs actuelles de `action` et de `controller`, et génère un chemin d’URL qui route vers l’action actuelle.</span><span class="sxs-lookup"><span data-stu-id="7b514-831">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="7b514-832">Le routage essaye d’utiliser les valeurs dans les valeurs ambiantes pour renseigner les informations que vous n’avez pas fournies lors de la génération d’une URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-832">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="7b514-833">En utilisant une route comme `{a}/{b}/{c}/{d}` et les valeurs ambiantes `{ a = Alice, b = Bob, c = Carol, d = David }`, le routage a suffisamment d’informations pour générer une URL sans aucune autre valeur supplémentaire, car tous les paramètres de route ont une valeur.</span><span class="sxs-lookup"><span data-stu-id="7b514-833">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="7b514-834">Si vous avez ajouté la valeur `{ d = Donovan }`, la valeur `{ d = David }` est ignorée, et le chemin d’URL généré est `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="7b514-834">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="7b514-835">Les chemins d’URL sont hiérarchiques.</span><span class="sxs-lookup"><span data-stu-id="7b514-835">URL paths are hierarchical.</span></span> <span data-ttu-id="7b514-836">Dans l’exemple ci-dessus, si vous avez ajouté la valeur `{ c = Cheryl }`, les deux valeurs `{ c = Carol, d = David }` sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="7b514-836">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="7b514-837">Dans ce cas nous n’avons plus de valeur pour `d` et la génération d’URL échoue.</span><span class="sxs-lookup"><span data-stu-id="7b514-837">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="7b514-838">Vous devez spécifier la valeur souhaitée de `c` et de `d`.</span><span class="sxs-lookup"><span data-stu-id="7b514-838">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="7b514-839">Vous vous attendez peut-être à rencontrer ce problème avec la route par défaut (`{controller}/{action}/{id?}`), mais vous rencontrerez rarement ce comportement en pratique, car `Url.Action` spécifie toujours explicitement une valeur pour `controller` et pour `action`.</span><span class="sxs-lookup"><span data-stu-id="7b514-839">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="7b514-840">Les surcharges plus longues de `Url.Action` prennent également un objet supplémentaire de *valeurs de route* pour fournir des valeurs pour les paramètres de route autres que `controller` et `action`.</span><span class="sxs-lookup"><span data-stu-id="7b514-840">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="7b514-841">Vous verrez ceci plus couramment utilisé avec un `id` comme `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="7b514-841">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="7b514-842">Par convention, l’objet de *valeurs de route* objet est généralement un objet de type anonyme, mais il peut également être un `IDictionary<>` ou un *objet .NET traditionnel* .</span><span class="sxs-lookup"><span data-stu-id="7b514-842">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object* .</span></span> <span data-ttu-id="7b514-843">Toutes les valeurs de route supplémentaires qui ne correspondent pas aux paramètres de route sont placées dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="7b514-843">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="7b514-844">Pour créer une URL absolue, utilisez une surcharge qui accepte un `protocol` :`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="7b514-844">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="7b514-845">Génération des URL par route</span><span class="sxs-lookup"><span data-stu-id="7b514-845">Generating URLs by route</span></span>

<span data-ttu-id="7b514-846">Le code ci-dessus a montré la génération d’une URL en passant le nom du contrôleur et le nom de l’action.</span><span class="sxs-lookup"><span data-stu-id="7b514-846">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="7b514-847">`IUrlHelper` fournit également la famille de méthodes `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="7b514-847">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="7b514-848">Ces méthodes sont similaires à `Url.Action`, mais elle ne copient pas les valeurs actuelles de `action` et de `controller` vers les valeurs de route.</span><span class="sxs-lookup"><span data-stu-id="7b514-848">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="7b514-849">L’utilisation la plus courante est de spécifier un nom de route pour utiliser une route spécifique pour générer l’URL, généralement *sans* spécifier un nom de contrôleur ou d’action.</span><span class="sxs-lookup"><span data-stu-id="7b514-849">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="7b514-850">Génération des URL en HTML</span><span class="sxs-lookup"><span data-stu-id="7b514-850">Generating URLs in HTML</span></span>

<span data-ttu-id="7b514-851">`IHtmlHelper` fournit les méthodes `HtmlHelper``Html.BeginForm` et `Html.ActionLink` pour générer respectivement les éléments `<form>` et `<a>`.</span><span class="sxs-lookup"><span data-stu-id="7b514-851">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="7b514-852">Ces méthodes utilisent la méthode `Url.Action` pour générer une URL et ils acceptent les arguments similaires.</span><span class="sxs-lookup"><span data-stu-id="7b514-852">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="7b514-853">Les pendants de `Url.RouteUrl` pour `HtmlHelper` sont `Html.BeginRouteForm` et `Html.RouteLink`, qui ont des fonctionnalités similaires.</span><span class="sxs-lookup"><span data-stu-id="7b514-853">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="7b514-854">Les TagHelpers génèrent des URL via le TagHelper `form` et le TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="7b514-854">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="7b514-855">Ils utilisent tous les deux `IUrlHelper` pour leur implémentation.</span><span class="sxs-lookup"><span data-stu-id="7b514-855">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="7b514-856">Pour plus d’informations, consultez [Utilisation des formulaires](../views/working-with-forms.md).</span><span class="sxs-lookup"><span data-stu-id="7b514-856">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="7b514-857">Dans les vues, `IUrlHelper` est disponible via la propriété `Url` pour toute génération d’URL ad hoc non couverte par ce qui figure ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="7b514-857">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="7b514-858">Génération des URL dans les résultats d’action</span><span class="sxs-lookup"><span data-stu-id="7b514-858">Generating URLS in Action Results</span></span>

<span data-ttu-id="7b514-859">Les exemples précédents ont montré l’utilisation de `IUrlHelper` dans un contrôleur, alors que l’utilisation la plus courante dans un contrôleur est de générer une URL dans le cadre d’un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="7b514-859">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="7b514-860">Les classes de base `ControllerBase` et `Controller` fournissent des méthodes pratiques pour les résultats d’action qui référencent une autre action.</span><span class="sxs-lookup"><span data-stu-id="7b514-860">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="7b514-861">Une utilisation typique est de rediriger après acceptation de l’entrée utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7b514-861">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="7b514-862">Les méthodes de fabrique de résultats d’action suivent un modèle similaire aux méthodes sur `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="7b514-862">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="7b514-863">Cas spécial pour les routes conventionnelles dédiées</span><span class="sxs-lookup"><span data-stu-id="7b514-863">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="7b514-864">Le routage conventionnel peut utiliser un type spécial de définition de route appelé *route conventionnelle dédiée* .</span><span class="sxs-lookup"><span data-stu-id="7b514-864">Conventional routing can use a special kind of route definition called a *dedicated conventional route* .</span></span> <span data-ttu-id="7b514-865">Dans l’exemple ci-dessous, la route nommée `blog` est une route conventionnelle dédiée.</span><span class="sxs-lookup"><span data-stu-id="7b514-865">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7b514-866">En utilisant ces définitions de route, `Url.Action("Index", "Home")` génère le chemin d’URL `/` avec la route `default`, mais pourquoi ?</span><span class="sxs-lookup"><span data-stu-id="7b514-866">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="7b514-867">Vous pouvez deviner que les valeurs de route `{ controller = Home, action = Index }` seraient suffisantes pour générer une URL avec `blog`, et que le résultat serait `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="7b514-867">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="7b514-868">Les routes conventionnelles dédiées s’appuient sur un comportement spécial des valeurs par défaut qui n’ont pas de paramètre de route correspondant qui empêche la route d’être « trop globale » avec la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-868">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="7b514-869">Dans ce cas, les valeurs par défaut sont `{ controller = Blog, action = Article }`, et ni `controller` ni `action` n’apparaissent comme paramètre de route.</span><span class="sxs-lookup"><span data-stu-id="7b514-869">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="7b514-870">Quand le routage effectue une génération d’URL, les valeurs fournies doivent correspondre aux valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="7b514-870">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="7b514-871">La génération d’URL avec `blog` échoue, car les valeurs `{ controller = Home, action = Index }` ne correspondent pas à `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="7b514-871">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="7b514-872">Le routage essaye alors d’utiliser `default`, ce qui réussit.</span><span class="sxs-lookup"><span data-stu-id="7b514-872">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="7b514-873">Zones (Areas)</span><span class="sxs-lookup"><span data-stu-id="7b514-873">Areas</span></span>

<span data-ttu-id="7b514-874">Les [zones](areas.md) sont une fonctionnalité de MVC utilisée pour organiser des fonctionnalités connexes dans un groupe sous la forme d’un espace de noms de routage distinct (pour les actions de contrôleur) et d’une structure de dossiers (pour les vues).</span><span class="sxs-lookup"><span data-stu-id="7b514-874">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="7b514-875">L’utilisation de zones permet à une application d’avoir plusieurs contrôleurs portant le même nom, pour autant qu’ils soient dans des *zones* différentes.</span><span class="sxs-lookup"><span data-stu-id="7b514-875">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas* .</span></span> <span data-ttu-id="7b514-876">L’utilisation de zones crée une hiérarchie qui permet le routage par ajout d’un autre paramètre de route, `area`, à `controller` et à `action`.</span><span class="sxs-lookup"><span data-stu-id="7b514-876">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="7b514-877">Cette section explique comment le routage interagit avec les zones. Pour plus d’informations sur l’utilisation des zones avec des vues, consultez [Zones](areas.md).</span><span class="sxs-lookup"><span data-stu-id="7b514-877">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="7b514-878">L’exemple suivant configure MVC pour utiliser la route conventionnelle par défaut et une *route de zone* pour une zone nommée `Blog` :</span><span class="sxs-lookup"><span data-stu-id="7b514-878">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="7b514-879">Lors de la mise en correspondance d’un chemin d’URL comme `/Manage/Users/AddUser`, la première route produit les valeurs de route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="7b514-879">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="7b514-880">La valeur de route `area` est produite par une valeur par défaut pour `area` ; en fait, la route créée par `MapAreaRoute` est équivalente à la suivante :</span><span class="sxs-lookup"><span data-stu-id="7b514-880">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="7b514-881">`MapAreaRoute` crée une route avec à la fois une valeur par défaut et une contrainte pour `area` en utilisant le nom de la zone fournie, dans ce cas `Blog`.</span><span class="sxs-lookup"><span data-stu-id="7b514-881">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="7b514-882">La valeur par défaut garantit que la route produit toujours `{ area = Blog, ... }`, et la contrainte nécessite la valeur `{ area = Blog, ... }` pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-882">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="7b514-883">Le routage conventionnel est dépendant de l’ordre.</span><span class="sxs-lookup"><span data-stu-id="7b514-883">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7b514-884">En général, les routes avec des zones doivent être placées plus haut dans la table de routage, car elles sont plus spécifiques que les routes sans zone.</span><span class="sxs-lookup"><span data-stu-id="7b514-884">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="7b514-885">Avec l’exemple ci-dessus, les valeurs de route seraient mises en correspondance avec l’action suivante :</span><span class="sxs-lookup"><span data-stu-id="7b514-885">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="7b514-886">`AreaAttribute` est ce qui indique qu’un contrôleur fait partie d’une zone : nous disons que ce contrôleur est dans la zone `Blog`.</span><span class="sxs-lookup"><span data-stu-id="7b514-886">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="7b514-887">Les contrôleurs sans attribut `[Area]` ne sont membres d’aucune zone et ne sont **pas** trouvés en correspondance quand la valeur de route `area` est fournie par le routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-887">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="7b514-888">Dans l’exemple suivant, seul le premier contrôleur répertorié peut correspondre aux valeurs de route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="7b514-888">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="7b514-889">L’espace de noms de chaque contrôleur est montré ici par souci d’exhaustivité, sans quoi les contrôleurs auraient un conflit de noms et généreraient une erreur de compilateur.</span><span class="sxs-lookup"><span data-stu-id="7b514-889">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="7b514-890">Les espaces de noms de classe n’ont pas d’effet sur le routage de MVC.</span><span class="sxs-lookup"><span data-stu-id="7b514-890">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="7b514-891">Les deux premiers contrôleurs sont membres de zones, et ils sont trouvés en correspondance seulement quand le nom de leur zone respective est fourni par la valeur de route `area`.</span><span class="sxs-lookup"><span data-stu-id="7b514-891">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="7b514-892">Le troisième contrôleur n’est membre d’aucune zone et peut être trouvé en correspondance seulement quand aucune valeur pour `area` n’est fournie par le routage.</span><span class="sxs-lookup"><span data-stu-id="7b514-892">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="7b514-893">En termes de mise en correspondance avec *aucune valeur* , l’absence de la valeur `area` est identique à une valeur null ou de chaîne vide pour `area`.</span><span class="sxs-lookup"><span data-stu-id="7b514-893">In terms of matching *no value* , the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="7b514-894">Lors de l’exécution d’une action à l’intérieur d’une zone, la valeur de route pour `area` est disponible en tant que *valeur ambiante* , que le routage peut utiliser pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-894">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="7b514-895">Cela signifie que par défaut, les zones agissent *par attraction* pour la génération d’URL, comme le montre l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="7b514-895">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="7b514-896">Présentation d’IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="7b514-896">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="7b514-897">Cette section est une présentation détaillée des mécanismes internes du framework et de la façon dont MVC choisit une action à exécuter.</span><span class="sxs-lookup"><span data-stu-id="7b514-897">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="7b514-898">Une application classique n’a pas besoin d’une `IActionConstraint` personnalisée.</span><span class="sxs-lookup"><span data-stu-id="7b514-898">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="7b514-899">Vous avez probablement déjà utilisé `IActionConstraint` même si vous n’êtes pas familiarisé avec l’interface.</span><span class="sxs-lookup"><span data-stu-id="7b514-899">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="7b514-900">L’attribut `[HttpGet]` et les attributs `[Http-VERB]` similaires implémentent `IActionConstraint` de façon à limiter l’exécution d’une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="7b514-900">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="7b514-901">Dans l’hypothèse d’une route conventionnelle par défaut, le chemin d’URL `/Products/Edit` produirait les valeurs `{ controller = Products, action = Edit }`, qui seraient en correspondance avec **les deux** actions montrées ici.</span><span class="sxs-lookup"><span data-stu-id="7b514-901">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="7b514-902">Dans la terminologie `IActionConstraint`, nous pouvons dire que ces deux actions sont considérées comme candidates, car elles correspondent toutes deux aux données de la route.</span><span class="sxs-lookup"><span data-stu-id="7b514-902">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="7b514-903">Quand `HttpGetAttribute` s’exécute, il indique que *Edit()* est une correspondance pour *GET* et qu’il n’est une correspondance pour aucun des autres verbes HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b514-903">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="7b514-904">L’action `Edit(...)` n’a aucune contrainte définie et elle ne correspond donc à aucun verbe HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b514-904">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="7b514-905">Par conséquent, dans l’hypothèse d’un `POST`, seul `Edit(...)` est en correspondance.</span><span class="sxs-lookup"><span data-stu-id="7b514-905">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="7b514-906">Cependant, pour un `GET`, les deux actions peuvent néanmoins être en correspondance, mais une action avec `IActionConstraint` est toujours considérée comme étant *meilleure* qu’une action sans.</span><span class="sxs-lookup"><span data-stu-id="7b514-906">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="7b514-907">Par conséquent, comme `Edit()` a `[HttpGet]`, elle est considérée comme étant plus spécifique et est sélectionnée si les deux actions peuvent correspondre.</span><span class="sxs-lookup"><span data-stu-id="7b514-907">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="7b514-908">D’un point de vue conceptuel, `IActionConstraint` est une forme de *surcharge* , mais au lieu de surcharger des méthodes portant le même nom, elle surcharge entre des actions qui correspondent à la même URL.</span><span class="sxs-lookup"><span data-stu-id="7b514-908">Conceptually, `IActionConstraint` is a form of *overloading* , but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="7b514-909">Le routage par attributs utilise également `IActionConstraint` et peut aboutir à des actions de différents contrôleurs, toutes considérées comme candidates.</span><span class="sxs-lookup"><span data-stu-id="7b514-909">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="7b514-910">Implémentation d’IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="7b514-910">Implementing IActionConstraint</span></span>

<span data-ttu-id="7b514-911">La façon la plus simple d’implémenter une `IActionConstraint` est de créer une classe dérivée de `System.Attribute` et de la placer sur vos actions et vos contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="7b514-911">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="7b514-912">MVC découvre automatiquement les `IActionConstraint` qui sont appliquées en tant qu’attributs.</span><span class="sxs-lookup"><span data-stu-id="7b514-912">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="7b514-913">Vous pouvez utiliser le modèle d’application pour appliquer des contraintes, et il s’agit probablement de l’approche la plus souple, car elle vous permet de programmer des méta-informations indiquant comment elles sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="7b514-913">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="7b514-914">Dans l’exemple suivant, une contrainte choisit une action en fonction de l' *indicatif du pays* à partir des données de l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7b514-914">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="7b514-915">[Exemple complet sur GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="7b514-915">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="7b514-916">Vous êtes responsable de l’implémentation de la méthode `Accept` et du choix d’un « ordre » pour l’exécution de la contrainte.</span><span class="sxs-lookup"><span data-stu-id="7b514-916">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="7b514-917">Dans ce cas, la méthode `Accept` retourne `true` pour indiquer que l’action est une correspondance quand la valeur de la route `country` correspond à la valeur.</span><span class="sxs-lookup"><span data-stu-id="7b514-917">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="7b514-918">Ceci diffère d’un `RouteValueAttribute`, car elle permet à défaut d’appliquer une action sans attributs.</span><span class="sxs-lookup"><span data-stu-id="7b514-918">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="7b514-919">L’exemple montre que si vous définissez une action `en-US`, un code pays comme `fr-FR` passe à défaut à un contrôleur plus générique auquel `[CountrySpecific(...)]` n’est pas appliqué.</span><span class="sxs-lookup"><span data-stu-id="7b514-919">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="7b514-920">La propriété `Order` décide de *l’étape* dont la contrainte fait partie.</span><span class="sxs-lookup"><span data-stu-id="7b514-920">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="7b514-921">Les contraintes d’action s’exécutent dans des groupes en fonction de `Order`.</span><span class="sxs-lookup"><span data-stu-id="7b514-921">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="7b514-922">Par exemple, tous les attributs de méthode HTTP fournis par le framework utilisent la même valeur pour `Order`, de façon à ce qu’ils s’exécutent dans la même étape.</span><span class="sxs-lookup"><span data-stu-id="7b514-922">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="7b514-923">Vous pouvez avoir autant d’étapes que nécessaire pour implémenter les stratégies souhaitées.</span><span class="sxs-lookup"><span data-stu-id="7b514-923">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="7b514-924">Pour décider d’une valeur pour `Order`, déterminez si votre contrainte doit ou non être appliquée avant les méthodes HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b514-924">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="7b514-925">Les nombres les plus petits s’exécutent en premier.</span><span class="sxs-lookup"><span data-stu-id="7b514-925">Lower numbers run first.</span></span>

::: moniker-end
