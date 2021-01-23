---
title: Types de retour des actions des contrôleurs dans l’API web ASP.NET Core
author: scottaddie
description: En savoir plus sur l’utilisation des différents types de retour de méthode d’action de contrôleur dans une API Web ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
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
uid: web-api/action-return-types
ms.openlocfilehash: 62c8227ca770a3a9adbe780685b140bc0e86841e
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710696"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>Types de retour des actions des contrôleurs dans l’API web ASP.NET Core

Par [Scott Addie](https://github.com/scottaddie)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

ASP.NET Core offre les options suivantes pour les types de retours d’action du contrôleur d’API Web :

::: moniker range=">= aspnetcore-2.1"

* [Type spécifique](#specific-type)
* [IActionResult](#iactionresult-type)
* [ActionResult\<T>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [Type spécifique](#specific-type)
* [IActionResult](#iactionresult-type)

::: moniker-end

Ce document décrit le moment le plus approprié pour utiliser chaque type de retour.

## <a name="specific-type"></a>Type spécifique

L’action la plus simple retourne un type de données primitif ou complexe (par exemple, `string` ou un type d’objet personnalisé). Considérez l’action suivante, qui retourne une collection d’objets `Product` personnalisés :

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

Sans conditions connues contre lesquelles une protection est nécessaire pendant l’exécution de l’action, le retour d’un type spécifique peut suffire. L’action précédente n’acceptant aucun paramètre, la validation des contraintes de paramètre n’est pas nécessaire.

Lorsque plusieurs types de retour sont possibles, il est courant de mélanger un <xref:Microsoft.AspNetCore.Mvc.ActionResult> type de retour avec le type de retour primitif ou complexe. [IActionResult](#iactionresult-type) ou [ActionResult \<T> ](#actionresultt-type) sont nécessaires pour prendre en charge ce type d’action. Plusieurs exemples de types de retour multiples sont fournis dans ce document.

### <a name="return-ienumerablet-or-iasyncenumerablet"></a>Retourne IEnumerable \<T> ou IAsyncEnumerable\<T>

Dans ASP.NET Core 2,2 et versions antérieures, <xref:System.Collections.Generic.IEnumerable%601> le retour à partir d’une action entraîne une itération de collection synchrone par le sérialiseur. Le résultat est le blocage des appels et un potentiel pour la privation du pool de threads. À titre d’illustration, imaginez que Entity Framework (EF) Core est utilisé pour les besoins d’accès aux données de l’API Web. Le type de retour de l’action suivante est énuméré de manière synchrone lors de la sérialisation :

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Pour éviter l’énumération synchrone et bloquer les attentes sur la base de données dans ASP.NET Core 2,2 et versions antérieures, appelez `ToListAsync` :

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

Dans ASP.NET Core 3,0 et versions ultérieures, en retournant `IAsyncEnumerable<T>` à partir d’une action :

* N’entraîne plus d’itération synchrone.
* Devient aussi efficace que le retour de <xref:System.Collections.Generic.IEnumerable%601> .

ASP.NET Core 3,0 et ultérieur met en mémoire tampon le résultat de l’action suivante avant de la fournir au sérialiseur :

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Envisagez de déclarer le type de retour de la signature d’action en tant que `IAsyncEnumerable<T>` pour garantir l’itération asynchrone. En fin de compte, le mode d’itération est basé sur le type concret sous-jacent qui est retourné. MVC met automatiquement en mémoire tampon tout type concret qui implémente `IAsyncEnumerable<T>` .

Examinez l’action suivante, qui retourne les enregistrements de produit facturés sur la vente comme `IEnumerable<Product>` suit :

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.31/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

L' `IAsyncEnumerable<Product>` équivalent de l’action précédente est le suivant :

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.31/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

Les deux actions précédentes sont non bloquantes à partir de ASP.NET Core 3,0.

## <a name="iactionresult-type"></a>Type IActionResult

Le <xref:Microsoft.AspNetCore.Mvc.IActionResult> type de retour est approprié lorsque plusieurs `ActionResult` types de retour sont possibles dans une action. Les types `ActionResult` représentent différents codes d’état HTTP. Toute classe non abstraite dérivant de `ActionResult` qualifie en tant que type de retour valide. Voici quelques types de retour courants dans cette catégorie : <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) et <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200). Les méthodes pratiques de la <xref:Microsoft.AspNetCore.Mvc.ControllerBase> classe peuvent également être utilisées pour retourner `ActionResult` des types à partir d’une action. Par exemple, `return BadRequest();` est une forme abrégée de `return new BadRequestResult();` .

Étant donné qu’il existe plusieurs types de retour et chemins d’accès dans ce type d’action, l’utilisation libérale de l' [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribut est nécessaire. Cet attribut produit des détails de réponse plus descriptifs pour les pages d’aide de l’API Web générées par des outils tels que [Swagger](xref:tutorials/web-api-help-pages-using-swagger). `[ProducesResponseType]` indique les types connus et les codes d’état HTTP que l’action doit retourner.

### <a name="synchronous-action"></a>Action synchrone

Considérez l’action synchrone suivante pour laquelle il existe deux types de retour possibles :

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

Dans l’action précédente :

* Un code d’État 404 est retourné lorsque le produit représenté par `id` n’existe pas dans le magasin de données sous-jacent. La <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> méthode pratique est appelée en tant que raccourci pour `return new NotFoundResult();` .
* Un code d’état 200 est retourné avec l' `Product` objet lorsque le produit existe. La <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> méthode pratique est appelée en tant que raccourci pour `return new OkObjectResult(product);` .

### <a name="asynchronous-action"></a>Action asynchrone

Considérez l’action asynchrone suivante pour laquelle il existe deux types de retour possibles :

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

Dans l’action précédente :

* Un code d’état 400 est retourné lorsque la description du produit contient « widget XYZ ». La <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> méthode pratique est appelée en tant que raccourci pour `return new BadRequestResult();` .
* Un code d’État 201 est généré par la <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> méthode pratique lors de la création d’un produit. Une alternative à l’appel de `CreatedAtAction` est `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);` . Dans ce chemin d’accès de code, l' `Product` objet est fourni dans le corps de la réponse. Un `Location` en-tête de réponse contenant l’URL du produit nouvellement créé est fourni.

Par exemple, le modèle suivant indique que les requêtes doivent inclure les propriétés `Name` et `Description`. Si vous ne fournissez `Name` `Description` pas et dans la demande, la validation du modèle échoue.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

Si l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut dans ASP.NET Core 2,1 ou une version ultérieure est appliqué, les erreurs de validation de modèle génèrent un code d’état 400. Pour plus d’informations, consultez [Réponses HTTP 400 automatiques](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>\<T>Type ActionResult

ASP.NET Core 2,1 a introduit le type de retour [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) pour les actions du contrôleur d’API Web. Elle vous permet de retourner un type dérivant de <xref:Microsoft.AspNetCore.Mvc.ActionResult> ou qui retourne un [type spécifique](#specific-type). `ActionResult<T>` offre les avantages suivants par rapport au [type IActionResult](#iactionresult-type) :

* La [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) propriété de l’attribut `Type` peut être exclue. Par exemple, `[ProducesResponseType(200, Type = typeof(Product))]` est simplifié en `[ProducesResponseType(200)]`. Le type de retour attendu pour l’action est déduit de `T` dans `ActionResult<T>`.
* Des [opérateurs de cast implicite](/dotnet/csharp/language-reference/keywords/implicit) prennent en charge la conversion de `T` et `ActionResult` en `ActionResult<T>`. `T` convertit en <xref:Microsoft.AspNetCore.Mvc.ObjectResult> , ce qui signifie que `return new ObjectResult(T);` est simplifié en `return T;` .

C# ne prend pas en charge les opérateurs de cast implicite sur les interfaces. Par conséquent, `ActionResult<T>` nécessite une conversion de l’interface en un type concret. Par exemple, l’utilisation de `IEnumerable` dans l’exemple suivant ne fonctionne pas :

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

Pour réparer le code précédent, vous pouvez retourner `_repository.GetProducts().ToList();`.

La plupart des actions ont un type de retour spécifique. Des conditions inattendues peuvent se produire pendant l’exécution d’une action, auquel cas le type spécifique n’est pas retourné. Par exemple, le paramètre d’entrée d’une action peut entraîner l’échec de la validation du modèle. Dans ce cas, il est courant de retourner le type `ActionResult` approprié à la place du type spécifique.

### <a name="synchronous-action"></a>Action synchrone

Considérez une action synchrone pour laquelle il existe deux types de retour possibles :

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

Dans l’action précédente :

* Un code d’État 404 est retourné lorsque le produit n’existe pas dans la base de données.
* Un code d’état 200 est retourné avec l' `Product` objet correspondant lorsque le produit existe. Avant le ASP.NET Core 2,1, la `return product;` ligne devait être `return Ok(product);` .

### <a name="asynchronous-action"></a>Action asynchrone

Considérez une action asynchrone pour laquelle il existe deux types de retour possibles :

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

Dans l’action précédente :

* Un code d’état 400 ( <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> ) est retourné par le runtime ASP.net core dans les cas suivants :
  * L' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut a été appliqué et la validation du modèle échoue.
  * La description de produit contient « XYZ Widget ».
* Un code d’État 201 est généré par la <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> méthode lors de la création d’un produit. Dans ce chemin d’accès de code, l' `Product` objet est fourni dans le corps de la réponse. Un `Location` en-tête de réponse contenant l’URL du produit nouvellement créé est fourni.

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
