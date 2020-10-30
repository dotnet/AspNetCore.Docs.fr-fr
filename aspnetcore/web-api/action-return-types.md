---
title: Types de retour de l’action du contrôleur dans ASP.NET Core API Web
author: scottaddie
description: En savoir plus sur l’utilisation des différents types de retour de méthode d’action de contrôleur dans une API Web ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
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
uid: web-api/action-return-types
ms.openlocfilehash: a2866970a20785ae8fa306d484972697817b7f92
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058946"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="0d18e-103">Types de retour de l’action du contrôleur dans ASP.NET Core API Web</span><span class="sxs-lookup"><span data-stu-id="0d18e-103">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="0d18e-104">Par [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="0d18e-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="0d18e-105">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0d18e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0d18e-106">ASP.NET Core offre les options suivantes pour les types de retours d’action du contrôleur d’API Web :</span><span class="sxs-lookup"><span data-stu-id="0d18e-106">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="0d18e-107">Type spécifique</span><span class="sxs-lookup"><span data-stu-id="0d18e-107">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="0d18e-108">IActionResult</span><span class="sxs-lookup"><span data-stu-id="0d18e-108">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="0d18e-109">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="0d18e-109">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="0d18e-110">Type spécifique</span><span class="sxs-lookup"><span data-stu-id="0d18e-110">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="0d18e-111">IActionResult</span><span class="sxs-lookup"><span data-stu-id="0d18e-111">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="0d18e-112">Ce document décrit le moment le plus approprié pour utiliser chaque type de retour.</span><span class="sxs-lookup"><span data-stu-id="0d18e-112">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="0d18e-113">Type spécifique</span><span class="sxs-lookup"><span data-stu-id="0d18e-113">Specific type</span></span>

<span data-ttu-id="0d18e-114">L’action la plus simple retourne un type de données primitif ou complexe (par exemple, `string` ou un type d’objet personnalisé).</span><span class="sxs-lookup"><span data-stu-id="0d18e-114">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="0d18e-115">Considérez l’action suivante, qui retourne une collection d’objets `Product` personnalisés :</span><span class="sxs-lookup"><span data-stu-id="0d18e-115">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="0d18e-116">Sans conditions connues contre lesquelles une protection est nécessaire pendant l’exécution de l’action, le retour d’un type spécifique peut suffire.</span><span class="sxs-lookup"><span data-stu-id="0d18e-116">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="0d18e-117">L’action précédente n’acceptant aucun paramètre, la validation des contraintes de paramètre n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="0d18e-117">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="0d18e-118">Lorsque plusieurs types de retour sont possibles, il est courant de mélanger un <xref:Microsoft.AspNetCore.Mvc.ActionResult> type de retour avec le type de retour primitif ou complexe.</span><span class="sxs-lookup"><span data-stu-id="0d18e-118">When multiple return types are possible, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="0d18e-119">[IActionResult](#iactionresult-type) ou [ActionResult \<T> ](#actionresultt-type) sont nécessaires pour prendre en charge ce type d’action.</span><span class="sxs-lookup"><span data-stu-id="0d18e-119">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span> <span data-ttu-id="0d18e-120">Plusieurs exemples de types de retour multiples sont fournis dans ce document.</span><span class="sxs-lookup"><span data-stu-id="0d18e-120">Several samples of multiple return types are provided in this document.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="0d18e-121">Retourne IEnumerable \<T> ou IAsyncEnumerable\<T></span><span class="sxs-lookup"><span data-stu-id="0d18e-121">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="0d18e-122">Dans ASP.NET Core 2,2 et versions antérieures, <xref:System.Collections.Generic.IEnumerable%601> le retour à partir d’une action entraîne une itération de collection synchrone par le sérialiseur.</span><span class="sxs-lookup"><span data-stu-id="0d18e-122">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="0d18e-123">Le résultat est le blocage des appels et un potentiel pour la privation du pool de threads.</span><span class="sxs-lookup"><span data-stu-id="0d18e-123">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="0d18e-124">À titre d’illustration, imaginez que Entity Framework (EF) Core est utilisé pour les besoins d’accès aux données de l’API Web.</span><span class="sxs-lookup"><span data-stu-id="0d18e-124">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="0d18e-125">Le type de retour de l’action suivante est énuméré de manière synchrone lors de la sérialisation :</span><span class="sxs-lookup"><span data-stu-id="0d18e-125">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="0d18e-126">Pour éviter l’énumération synchrone et bloquer les attentes sur la base de données dans ASP.NET Core 2,2 et versions antérieures, appelez `ToListAsync` :</span><span class="sxs-lookup"><span data-stu-id="0d18e-126">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="0d18e-127">Dans ASP.NET Core 3,0 et versions ultérieures, en retournant `IAsyncEnumerable<T>` à partir d’une action :</span><span class="sxs-lookup"><span data-stu-id="0d18e-127">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="0d18e-128">N’entraîne plus d’itération synchrone.</span><span class="sxs-lookup"><span data-stu-id="0d18e-128">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="0d18e-129">Devient aussi efficace que le retour de <xref:System.Collections.Generic.IEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="0d18e-129">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="0d18e-130">ASP.NET Core 3,0 et ultérieur met en mémoire tampon le résultat de l’action suivante avant de la fournir au sérialiseur :</span><span class="sxs-lookup"><span data-stu-id="0d18e-130">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="0d18e-131">Envisagez de déclarer le type de retour de la signature d’action en tant que `IAsyncEnumerable<T>` pour garantir l’itération asynchrone.</span><span class="sxs-lookup"><span data-stu-id="0d18e-131">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="0d18e-132">En fin de compte, le mode d’itération est basé sur le type concret sous-jacent qui est retourné.</span><span class="sxs-lookup"><span data-stu-id="0d18e-132">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="0d18e-133">MVC met automatiquement en mémoire tampon tout type concret qui implémente `IAsyncEnumerable<T>` .</span><span class="sxs-lookup"><span data-stu-id="0d18e-133">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="0d18e-134">Examinez l’action suivante, qui retourne les enregistrements de produit facturés sur la vente comme `IEnumerable<Product>` suit :</span><span class="sxs-lookup"><span data-stu-id="0d18e-134">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="0d18e-135">L' `IAsyncEnumerable<Product>` équivalent de l’action précédente est le suivant :</span><span class="sxs-lookup"><span data-stu-id="0d18e-135">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="0d18e-136">Les deux actions précédentes sont non bloquantes à partir de ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="0d18e-136">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="0d18e-137">Type IActionResult</span><span class="sxs-lookup"><span data-stu-id="0d18e-137">IActionResult type</span></span>

<span data-ttu-id="0d18e-138">Le <xref:Microsoft.AspNetCore.Mvc.IActionResult> type de retour est approprié lorsque plusieurs `ActionResult` types de retour sont possibles dans une action.</span><span class="sxs-lookup"><span data-stu-id="0d18e-138">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="0d18e-139">Les types `ActionResult` représentent différents codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="0d18e-139">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="0d18e-140">Toute classe non abstraite dérivant de `ActionResult` qualifie en tant que type de retour valide.</span><span class="sxs-lookup"><span data-stu-id="0d18e-140">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="0d18e-141">Voici quelques types de retour courants dans cette catégorie : <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) et <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span><span class="sxs-lookup"><span data-stu-id="0d18e-141">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="0d18e-142">Les méthodes pratiques de la <xref:Microsoft.AspNetCore.Mvc.ControllerBase> classe peuvent également être utilisées pour retourner `ActionResult` des types à partir d’une action.</span><span class="sxs-lookup"><span data-stu-id="0d18e-142">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="0d18e-143">Par exemple, `return BadRequest();` est une forme abrégée de `return new BadRequestResult();` .</span><span class="sxs-lookup"><span data-stu-id="0d18e-143">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="0d18e-144">Étant donné qu’il existe plusieurs types de retour et chemins d’accès dans ce type d’action, l’utilisation libérale de l' [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribut est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="0d18e-144">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="0d18e-145">Cet attribut produit des détails de réponse plus descriptifs pour les pages d’aide de l’API Web générées par des outils tels que [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="0d18e-145">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="0d18e-146">`[ProducesResponseType]` indique les types connus et les codes d’état HTTP que l’action doit retourner.</span><span class="sxs-lookup"><span data-stu-id="0d18e-146">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="0d18e-147">Action synchrone</span><span class="sxs-lookup"><span data-stu-id="0d18e-147">Synchronous action</span></span>

<span data-ttu-id="0d18e-148">Considérez l’action synchrone suivante pour laquelle il existe deux types de retour possibles :</span><span class="sxs-lookup"><span data-stu-id="0d18e-148">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="0d18e-149">Dans l’action précédente :</span><span class="sxs-lookup"><span data-stu-id="0d18e-149">In the preceding action:</span></span>

* <span data-ttu-id="0d18e-150">Un code d’État 404 est retourné lorsque le produit représenté par `id` n’existe pas dans le magasin de données sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="0d18e-150">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="0d18e-151">La <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> méthode pratique est appelée en tant que raccourci pour `return new NotFoundResult();` .</span><span class="sxs-lookup"><span data-stu-id="0d18e-151">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="0d18e-152">Un code d’état 200 est retourné avec l' `Product` objet lorsque le produit existe.</span><span class="sxs-lookup"><span data-stu-id="0d18e-152">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="0d18e-153">La <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> méthode pratique est appelée en tant que raccourci pour `return new OkObjectResult(product);` .</span><span class="sxs-lookup"><span data-stu-id="0d18e-153">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="0d18e-154">Action asynchrone</span><span class="sxs-lookup"><span data-stu-id="0d18e-154">Asynchronous action</span></span>

<span data-ttu-id="0d18e-155">Considérez l’action asynchrone suivante pour laquelle il existe deux types de retour possibles :</span><span class="sxs-lookup"><span data-stu-id="0d18e-155">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="0d18e-156">Dans l’action précédente :</span><span class="sxs-lookup"><span data-stu-id="0d18e-156">In the preceding action:</span></span>

* <span data-ttu-id="0d18e-157">Un code d’état 400 est retourné lorsque la description du produit contient « widget XYZ ».</span><span class="sxs-lookup"><span data-stu-id="0d18e-157">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="0d18e-158">La <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> méthode pratique est appelée en tant que raccourci pour `return new BadRequestResult();` .</span><span class="sxs-lookup"><span data-stu-id="0d18e-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="0d18e-159">Un code d’État 201 est généré par la <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> méthode pratique lors de la création d’un produit.</span><span class="sxs-lookup"><span data-stu-id="0d18e-159">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="0d18e-160">Une alternative à l’appel de `CreatedAtAction` est `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);` .</span><span class="sxs-lookup"><span data-stu-id="0d18e-160">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="0d18e-161">Dans ce chemin d’accès de code, l' `Product` objet est fourni dans le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="0d18e-161">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="0d18e-162">Un `Location` en-tête de réponse contenant l’URL du produit nouvellement créé est fourni.</span><span class="sxs-lookup"><span data-stu-id="0d18e-162">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="0d18e-163">Par exemple, le modèle suivant indique que les requêtes doivent inclure les propriétés `Name` et `Description`.</span><span class="sxs-lookup"><span data-stu-id="0d18e-163">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="0d18e-164">Si vous ne fournissez `Name` `Description` pas et dans la demande, la validation du modèle échoue.</span><span class="sxs-lookup"><span data-stu-id="0d18e-164">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0d18e-165">Si l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut dans ASP.NET Core 2,1 ou une version ultérieure est appliqué, les erreurs de validation de modèle génèrent un code d’état 400.</span><span class="sxs-lookup"><span data-stu-id="0d18e-165">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="0d18e-166">Pour plus d’informations, consultez [Réponses HTTP 400 automatiques](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="0d18e-166">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="0d18e-167">\<T>Type ActionResult</span><span class="sxs-lookup"><span data-stu-id="0d18e-167">ActionResult\<T> type</span></span>

<span data-ttu-id="0d18e-168">ASP.NET Core 2,1 a introduit le type de retour [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) pour les actions du contrôleur d’API Web.</span><span class="sxs-lookup"><span data-stu-id="0d18e-168">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="0d18e-169">Elle vous permet de retourner un type dérivant de <xref:Microsoft.AspNetCore.Mvc.ActionResult> ou qui retourne un [type spécifique](#specific-type).</span><span class="sxs-lookup"><span data-stu-id="0d18e-169">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="0d18e-170">`ActionResult<T>` offre les avantages suivants par rapport au [type IActionResult](#iactionresult-type) :</span><span class="sxs-lookup"><span data-stu-id="0d18e-170">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="0d18e-171">La [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) propriété de l’attribut `Type` peut être exclue.</span><span class="sxs-lookup"><span data-stu-id="0d18e-171">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="0d18e-172">Par exemple, `[ProducesResponseType(200, Type = typeof(Product))]` est simplifié en `[ProducesResponseType(200)]`.</span><span class="sxs-lookup"><span data-stu-id="0d18e-172">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="0d18e-173">Le type de retour attendu pour l’action est déduit de `T` dans `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="0d18e-173">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="0d18e-174">Des [opérateurs de cast implicite](/dotnet/csharp/language-reference/keywords/implicit) prennent en charge la conversion de `T` et `ActionResult` en `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="0d18e-174">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="0d18e-175">`T` convertit en <xref:Microsoft.AspNetCore.Mvc.ObjectResult> , ce qui signifie que `return new ObjectResult(T);` est simplifié en `return T;` .</span><span class="sxs-lookup"><span data-stu-id="0d18e-175">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="0d18e-176">C# ne prend pas en charge les opérateurs de cast implicite sur les interfaces.</span><span class="sxs-lookup"><span data-stu-id="0d18e-176">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="0d18e-177">Par conséquent, `ActionResult<T>` nécessite une conversion de l’interface en un type concret.</span><span class="sxs-lookup"><span data-stu-id="0d18e-177">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="0d18e-178">Par exemple, l’utilisation de `IEnumerable` dans l’exemple suivant ne fonctionne pas :</span><span class="sxs-lookup"><span data-stu-id="0d18e-178">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="0d18e-179">Pour réparer le code précédent, vous pouvez retourner `_repository.GetProducts().ToList();`.</span><span class="sxs-lookup"><span data-stu-id="0d18e-179">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="0d18e-180">La plupart des actions ont un type de retour spécifique.</span><span class="sxs-lookup"><span data-stu-id="0d18e-180">Most actions have a specific return type.</span></span> <span data-ttu-id="0d18e-181">Des conditions inattendues peuvent se produire pendant l’exécution d’une action, auquel cas le type spécifique n’est pas retourné.</span><span class="sxs-lookup"><span data-stu-id="0d18e-181">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="0d18e-182">Par exemple, le paramètre d’entrée d’une action peut entraîner l’échec de la validation du modèle.</span><span class="sxs-lookup"><span data-stu-id="0d18e-182">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="0d18e-183">Dans ce cas, il est courant de retourner le type `ActionResult` approprié à la place du type spécifique.</span><span class="sxs-lookup"><span data-stu-id="0d18e-183">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="0d18e-184">Action synchrone</span><span class="sxs-lookup"><span data-stu-id="0d18e-184">Synchronous action</span></span>

<span data-ttu-id="0d18e-185">Considérez une action synchrone pour laquelle il existe deux types de retour possibles :</span><span class="sxs-lookup"><span data-stu-id="0d18e-185">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="0d18e-186">Dans l’action précédente :</span><span class="sxs-lookup"><span data-stu-id="0d18e-186">In the preceding action:</span></span>

* <span data-ttu-id="0d18e-187">Un code d’État 404 est retourné lorsque le produit n’existe pas dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="0d18e-187">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="0d18e-188">Un code d’état 200 est retourné avec l' `Product` objet correspondant lorsque le produit existe.</span><span class="sxs-lookup"><span data-stu-id="0d18e-188">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="0d18e-189">Avant le ASP.NET Core 2,1, la `return product;` ligne devait être `return Ok(product);` .</span><span class="sxs-lookup"><span data-stu-id="0d18e-189">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="0d18e-190">Action asynchrone</span><span class="sxs-lookup"><span data-stu-id="0d18e-190">Asynchronous action</span></span>

<span data-ttu-id="0d18e-191">Considérez une action asynchrone pour laquelle il existe deux types de retour possibles :</span><span class="sxs-lookup"><span data-stu-id="0d18e-191">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="0d18e-192">Dans l’action précédente :</span><span class="sxs-lookup"><span data-stu-id="0d18e-192">In the preceding action:</span></span>

* <span data-ttu-id="0d18e-193">Un code d’état 400 ( <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> ) est retourné par le runtime ASP.net core dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="0d18e-193">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="0d18e-194">L' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut a été appliqué et la validation du modèle échoue.</span><span class="sxs-lookup"><span data-stu-id="0d18e-194">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="0d18e-195">La description de produit contient « XYZ Widget ».</span><span class="sxs-lookup"><span data-stu-id="0d18e-195">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="0d18e-196">Un code d’État 201 est généré par la <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> méthode lors de la création d’un produit.</span><span class="sxs-lookup"><span data-stu-id="0d18e-196">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="0d18e-197">Dans ce chemin d’accès de code, l' `Product` objet est fourni dans le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="0d18e-197">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="0d18e-198">Un `Location` en-tête de réponse contenant l’URL du produit nouvellement créé est fourni.</span><span class="sxs-lookup"><span data-stu-id="0d18e-198">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0d18e-199">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0d18e-199">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
