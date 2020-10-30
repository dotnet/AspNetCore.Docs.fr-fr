---
title: Liaison de données dans ASP.NET Core
author: rick-anderson
description: Découvrez comment fonctionne la liaison de modèle avec ASP.NET Core, et comment personnaliser son comportement.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: a3be22134246c76b0a809ddb97b33ff97ace9a5b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057503"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="de391-103">Liaison de données dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="de391-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="de391-104">Cet article explique ce qu’est la liaison de modèle, comment elle fonctionne et comment personnaliser son comportement.</span><span class="sxs-lookup"><span data-stu-id="de391-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="de391-105">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="de391-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="de391-106">Description de la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="de391-106">What is Model binding</span></span>

<span data-ttu-id="de391-107">Les contrôleurs et les :::no-loc(Razor)::: pages fonctionnent avec des données provenant de requêtes http.</span><span class="sxs-lookup"><span data-stu-id="de391-107">Controllers and :::no-loc(Razor)::: pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="de391-108">Par exemple, les données de routage peuvent fournir une clé d’enregistrement, et les champs de formulaire posté peuvent fournir des valeurs pour les propriétés du modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="de391-109">L’écriture du code permettant de récupérer chacune de ces valeurs et de les convertir en types .NET à partir de chaînes est fastidieuse et source d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="de391-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="de391-110">La liaison de modèle automatise ce processus.</span><span class="sxs-lookup"><span data-stu-id="de391-110">Model binding automates this process.</span></span> <span data-ttu-id="de391-111">Le système de liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="de391-111">The model binding system:</span></span>

* <span data-ttu-id="de391-112">Récupère les données de diverses sources telles que les données de routage, les champs de formulaire et les chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="de391-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="de391-113">Fournit les données aux contrôleurs et aux :::no-loc(Razor)::: pages dans les paramètres de méthode et les propriétés publiques.</span><span class="sxs-lookup"><span data-stu-id="de391-113">Provides the data to controllers and :::no-loc(Razor)::: pages in method parameters and public properties.</span></span>
* <span data-ttu-id="de391-114">Convertit les données de chaîne en types .NET</span><span class="sxs-lookup"><span data-stu-id="de391-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="de391-115">Met à jour les propriétés des types complexes</span><span class="sxs-lookup"><span data-stu-id="de391-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="de391-116">Exemple</span><span class="sxs-lookup"><span data-stu-id="de391-116">Example</span></span>

<span data-ttu-id="de391-117">Supposons que vous ayez la méthode d’action suivante :</span><span class="sxs-lookup"><span data-stu-id="de391-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="de391-118">Et que l’application reçoive une requête avec l’URL suivante :</span><span class="sxs-lookup"><span data-stu-id="de391-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="de391-119">La liaison de modèle passe par les étapes suivantes après que le système de routage a sélectionné la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="de391-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="de391-120">Elle recherche le premier paramètre de `GetByID`, un entier nommé `id`.</span><span class="sxs-lookup"><span data-stu-id="de391-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="de391-121">Elle parcourt les sources disponibles dans la requête HTTP et trouve `id` = « 2 » dans les données de routage.</span><span class="sxs-lookup"><span data-stu-id="de391-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="de391-122">Elle convertit la chaîne « 2 » en entier 2.</span><span class="sxs-lookup"><span data-stu-id="de391-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="de391-123">Elle recherche le paramètre suivant de `GetByID`, un booléen nommé `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="de391-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="de391-124">Elle parcourt les sources et trouve « DogsOnly=true » dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="de391-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="de391-125">La mise en correspondance des noms ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="de391-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="de391-126">Elle convertit la chaîne « true » en booléen `true`.</span><span class="sxs-lookup"><span data-stu-id="de391-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="de391-127">Le framework appelle ensuite la méthode `GetById`, en passant 2 pour le paramètre `id`, et `true` pour le paramètre `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="de391-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="de391-128">Dans l’exemple précédent, les cibles de liaison de modèle sont des paramètres de méthode qui sont des types simples.</span><span class="sxs-lookup"><span data-stu-id="de391-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="de391-129">Les cibles peuvent être également les propriétés d’un type complexe.</span><span class="sxs-lookup"><span data-stu-id="de391-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="de391-130">Une fois chaque propriété correctement liée, la [validation du modèle](xref:mvc/models/validation) est effectuée pour la propriété concernée.</span><span class="sxs-lookup"><span data-stu-id="de391-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="de391-131">L’enregistrement des données liées au modèle (ainsi que des erreurs de liaison ou de validation) est stocké dans [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="de391-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="de391-132">Pour savoir si ce processus a abouti, l’application vérifie l’indicateur [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="de391-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="de391-133">Cibles</span><span class="sxs-lookup"><span data-stu-id="de391-133">Targets</span></span>

<span data-ttu-id="de391-134">La liaison de modèle tente de trouver des valeurs pour les genres de cible suivants :</span><span class="sxs-lookup"><span data-stu-id="de391-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="de391-135">Paramètres de la méthode d’action de contrôleur vers laquelle une requête est routée.</span><span class="sxs-lookup"><span data-stu-id="de391-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="de391-136">Paramètres de la :::no-loc(Razor)::: méthode de gestionnaire de pages vers laquelle une requête est routée.</span><span class="sxs-lookup"><span data-stu-id="de391-136">Parameters of the :::no-loc(Razor)::: Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="de391-137">Propriétés publiques d’un contrôleur ou d’une classe `PageModel`, si elles sont spécifiées par des attributs.</span><span class="sxs-lookup"><span data-stu-id="de391-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="de391-138">Attribut [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="de391-138">[BindProperty] attribute</span></span>

<span data-ttu-id="de391-139">Peut être appliqué à une propriété publique d’un contrôleur ou à une classe `PageModel` pour que la liaison de modèle cible cette propriété :</span><span class="sxs-lookup"><span data-stu-id="de391-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="de391-140">Attribut [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="de391-140">[BindProperties] attribute</span></span>

<span data-ttu-id="de391-141">Disponible avec ASP.NET Core 2.1 et les versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="de391-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="de391-142">Peut être appliqué à un contrôleur ou à une classe `PageModel` pour indiquer à la liaison de modèle de cibler toutes les propriétés publiques de la classe :</span><span class="sxs-lookup"><span data-stu-id="de391-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="de391-143">Liaison de modèle pour les requêtes HTTP GET</span><span class="sxs-lookup"><span data-stu-id="de391-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="de391-144">Par défaut, les propriétés ne sont pas liées pour les requêtes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="de391-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="de391-145">En règle générale, le paramètre ID d’un enregistrement est tout ce dont vous avez besoin pour une requête GET.</span><span class="sxs-lookup"><span data-stu-id="de391-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="de391-146">L’ID d’enregistrement est utilisé pour rechercher l’élément dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="de391-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="de391-147">Il n’est donc pas nécessaire de lier une propriété qui contient une instance du modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="de391-148">Pour les scénarios dans lesquels vous souhaitez que les propriétés soient liées aux données provenant de requêtes GET, affectez à la propriété `SupportsGet` la valeur `true` :</span><span class="sxs-lookup"><span data-stu-id="de391-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="de391-149">Sources</span><span class="sxs-lookup"><span data-stu-id="de391-149">Sources</span></span>

<span data-ttu-id="de391-150">Par défaut, la liaison de modèle obtient des données sous la forme de paires clé-valeur à partir des sources suivantes dans une requête HTTP :</span><span class="sxs-lookup"><span data-stu-id="de391-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="de391-151">Champs de formulaire</span><span class="sxs-lookup"><span data-stu-id="de391-151">Form fields</span></span>
1. <span data-ttu-id="de391-152">Corps de la requête (pour les [contrôleurs ayant l’attribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="de391-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="de391-153">Données de routage</span><span class="sxs-lookup"><span data-stu-id="de391-153">Route data</span></span>
1. <span data-ttu-id="de391-154">Paramètres de chaîne de requête</span><span class="sxs-lookup"><span data-stu-id="de391-154">Query string parameters</span></span>
1. <span data-ttu-id="de391-155">Fichiers chargés</span><span class="sxs-lookup"><span data-stu-id="de391-155">Uploaded files</span></span>

<span data-ttu-id="de391-156">Pour chaque paramètre ou propriété cible, les sources sont analysées dans l’ordre indiqué dans la liste précédente.</span><span class="sxs-lookup"><span data-stu-id="de391-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="de391-157">Il existe quelques exceptions :</span><span class="sxs-lookup"><span data-stu-id="de391-157">There are a few exceptions:</span></span>

* <span data-ttu-id="de391-158">Les données de routage et les valeurs de chaîne de requête sont utilisées uniquement pour les types simples.</span><span class="sxs-lookup"><span data-stu-id="de391-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="de391-159">Les fichiers chargés sont liés uniquement aux types cibles qui implémentent `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="de391-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="de391-160">Si la source par défaut n’est pas correcte, utilisez l’un des attributs suivants pour spécifier la source :</span><span class="sxs-lookup"><span data-stu-id="de391-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="de391-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Obtient des valeurs à partir de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="de391-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="de391-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Obtient des valeurs à partir des données d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="de391-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="de391-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Obtient des valeurs à partir de champs de formulaire publiés.</span><span class="sxs-lookup"><span data-stu-id="de391-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="de391-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Obtient les valeurs du corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="de391-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="de391-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Obtient des valeurs à partir des en-têtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="de391-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="de391-166">Ces attributs :</span><span class="sxs-lookup"><span data-stu-id="de391-166">These attributes:</span></span>

* <span data-ttu-id="de391-167">Sont ajoutés aux propriétés du modèle individuellement (et non à la classe de modèle), comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="de391-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="de391-168">Acceptent éventuellement une valeur de nom de modèle dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="de391-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="de391-169">Cette option est fournie au cas où le nom de propriété ne correspondrait pas à la valeur de la requête.</span><span class="sxs-lookup"><span data-stu-id="de391-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="de391-170">Par exemple, la valeur de la requête peut être un en-tête avec un trait d’union dans son nom, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="de391-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="de391-171">Attribut [FromBody]</span><span class="sxs-lookup"><span data-stu-id="de391-171">[FromBody] attribute</span></span>

<span data-ttu-id="de391-172">Appliquez l' `[FromBody]` attribut à un paramètre pour remplir ses propriétés à partir du corps d’une requête http.</span><span class="sxs-lookup"><span data-stu-id="de391-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="de391-173">Le runtime ASP.NET Core délègue la responsabilité de lire le corps dans un formateur d’entrée.</span><span class="sxs-lookup"><span data-stu-id="de391-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="de391-174">Les formateurs d’entrée sont décrits [plus loin dans cet article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="de391-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="de391-175">Lorsque `[FromBody]` est appliqué à un paramètre de type complexe, tous les attributs de source de liaison appliqués à ses propriétés sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="de391-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="de391-176">Par exemple, l' `Create` action suivante spécifie que son `pet` paramètre est rempli à partir du corps :</span><span class="sxs-lookup"><span data-stu-id="de391-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="de391-177">La `Pet` classe spécifie que sa `Breed` propriété est remplie à partir d’un paramètre de chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="de391-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="de391-178">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="de391-178">In the preceding example:</span></span>

* <span data-ttu-id="de391-179">L' `[FromQuery]` attribut est ignoré.</span><span class="sxs-lookup"><span data-stu-id="de391-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="de391-180">La `Breed` propriété n’est pas remplie à partir d’un paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="de391-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="de391-181">Les formateurs d’entrée lisent uniquement le corps et ne comprennent pas les attributs de source de liaison.</span><span class="sxs-lookup"><span data-stu-id="de391-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="de391-182">Si une valeur appropriée est trouvée dans le corps, cette valeur est utilisée pour remplir la `Breed` propriété.</span><span class="sxs-lookup"><span data-stu-id="de391-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="de391-183">N’appliquez pas `[FromBody]` à plus d’un paramètre par méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="de391-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="de391-184">Une fois que le flux de requête est lu par un formateur d’entrée, il ne peut plus être lu pour la liaison d’autres `[FromBody]` paramètres.</span><span class="sxs-lookup"><span data-stu-id="de391-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="de391-185">Sources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="de391-185">Additional sources</span></span>

<span data-ttu-id="de391-186">Les données sources sont fournies au système de liaison de modèle par les *fournisseurs de valeurs* .</span><span class="sxs-lookup"><span data-stu-id="de391-186">Source data is provided to the model binding system by *value providers* .</span></span> <span data-ttu-id="de391-187">Vous pouvez écrire et inscrire des fournisseurs de valeurs personnalisés qui obtiennent des données de liaison de modèle à partir d’autres sources.</span><span class="sxs-lookup"><span data-stu-id="de391-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="de391-188">Par exemple, vous pouvez obtenir des données à partir de ou de l' :::no-loc(cookie)::: État de session.</span><span class="sxs-lookup"><span data-stu-id="de391-188">For example, you might want data from :::no-loc(cookie):::s or session state.</span></span> <span data-ttu-id="de391-189">Pour obtenir des données provenant d’une nouvelle source :</span><span class="sxs-lookup"><span data-stu-id="de391-189">To get data from a new source:</span></span>

* <span data-ttu-id="de391-190">Créez une classe qui implémente `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="de391-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="de391-191">Créez une classe qui implémente `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="de391-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="de391-192">Inscrivez la classe de fabrique dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="de391-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="de391-193">L’exemple d’application comprend un [fournisseur de valeur](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) et un exemple d' [usine](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) qui obtient des valeurs de :::no-loc(cookie)::: s.</span><span class="sxs-lookup"><span data-stu-id="de391-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) example that gets values from :::no-loc(cookie):::s.</span></span> <span data-ttu-id="de391-194">Voici le code d’inscription dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="de391-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="de391-195">Le code affiché place le fournisseur de valeurs personnalisé après tous les fournisseurs de valeurs intégrés.</span><span class="sxs-lookup"><span data-stu-id="de391-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="de391-196">Pour en faire le premier fournisseur de la liste, appelez `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` à la place de `Add`.</span><span class="sxs-lookup"><span data-stu-id="de391-196">To make it the first in the list, call `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="de391-197">Aucune source pour une propriété de modèle</span><span class="sxs-lookup"><span data-stu-id="de391-197">No source for a model property</span></span>

<span data-ttu-id="de391-198">Par défaut, aucune erreur d’état de modèle n’est créée, s’il n’existe aucune valeur de propriété de modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="de391-199">La propriété a une valeur null ou une valeur par défaut :</span><span class="sxs-lookup"><span data-stu-id="de391-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="de391-200">Les types simples Nullable ont une valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="de391-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="de391-201">Les types valeur non Nullable ont la valeur `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="de391-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="de391-202">Par exemple, un paramètre `int id` a la valeur 0.</span><span class="sxs-lookup"><span data-stu-id="de391-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="de391-203">Pour les types complexes, la liaison de modèle crée une instance à l’aide du constructeur par défaut, sans définir de propriétés.</span><span class="sxs-lookup"><span data-stu-id="de391-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="de391-204">Les tableaux ont la valeur `Array.Empty<T>()`, sauf les tableaux `byte[]` qui ont une valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="de391-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="de391-205">Si l’état du modèle doit être invalidé lorsque rien n’est trouvé dans les champs de formulaire d’une propriété de modèle, utilisez l' [`[BindRequired]`](#bindrequired-attribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="de391-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="de391-206">Notez que ce comportement de `[BindRequired]` s’applique à la liaison de modèle des données de formulaire postées, et non aux données JSON ou XML d’un corps de requête.</span><span class="sxs-lookup"><span data-stu-id="de391-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="de391-207">Les données du corps de requête sont prises en charge par les [formateurs d’entrée](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="de391-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="de391-208">Erreurs de conversion de type</span><span class="sxs-lookup"><span data-stu-id="de391-208">Type conversion errors</span></span>

<span data-ttu-id="de391-209">Si une source est localisée mais qu’elle ne peut pas être convertie vers le type cible, l’état du modèle est marqué comme étant non valide.</span><span class="sxs-lookup"><span data-stu-id="de391-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="de391-210">Le paramètre ou la propriété cible a une valeur null ou une valeur par défaut, comme indiqué dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="de391-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="de391-211">Dans un contrôleur d’API ayant l’attribut `[ApiController]`, un état de modèle non valide entraîne une réponse HTTP 400 automatique.</span><span class="sxs-lookup"><span data-stu-id="de391-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="de391-212">Dans une :::no-loc(Razor)::: page, réaffichez la page avec un message d’erreur :</span><span class="sxs-lookup"><span data-stu-id="de391-212">In a :::no-loc(Razor)::: page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="de391-213">La validation côté client intercepte la plupart des données incorrectes qui seraient autrement soumises à un :::no-loc(Razor)::: formulaire de pages.</span><span class="sxs-lookup"><span data-stu-id="de391-213">Client-side validation catches most bad data that would otherwise be submitted to a :::no-loc(Razor)::: Pages form.</span></span> <span data-ttu-id="de391-214">Cette validation rend difficile le déclenchement du code en surbrillance indiqué plus haut.</span><span class="sxs-lookup"><span data-stu-id="de391-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="de391-215">L’exemple d’application comprend un bouton **Submit with Invalid Date** (Envoyer avec une date non valide), qui place les données incorrectes dans le champ **Hire Date** (Date d’embauche) et envoie le formulaire.</span><span class="sxs-lookup"><span data-stu-id="de391-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="de391-216">Ce bouton montre comment fonctionne le code permettant de réafficher la page quand des erreurs de conversion de données se produisent.</span><span class="sxs-lookup"><span data-stu-id="de391-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="de391-217">Quand la page est réaffichée par le code précédent, l’entrée non valide n’est pas visible dans le champ de formulaire.</span><span class="sxs-lookup"><span data-stu-id="de391-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="de391-218">En effet, la propriété de modèle à une valeur null ou une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="de391-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="de391-219">L’entrée non valide apparaît dans un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="de391-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="de391-220">Toutefois, si vous souhaitez réafficher les données incorrectes dans le champ de formulaire, transformez la propriété de modèle en chaîne et procédez à la conversion des données manuellement.</span><span class="sxs-lookup"><span data-stu-id="de391-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="de391-221">La même stratégie est recommandée si vous ne souhaitez pas que les erreurs de conversion de type entraînent des erreurs d’état de modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="de391-222">Dans ce cas, transformez la propriété de modèle en chaîne.</span><span class="sxs-lookup"><span data-stu-id="de391-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="de391-223">Types simples</span><span class="sxs-lookup"><span data-stu-id="de391-223">Simple types</span></span>

<span data-ttu-id="de391-224">Les types simples que le lieur de modèle peut convertir en chaînes sources sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="de391-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="de391-225">Booléen</span><span class="sxs-lookup"><span data-stu-id="de391-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="de391-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="de391-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="de391-227">Char</span><span class="sxs-lookup"><span data-stu-id="de391-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="de391-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="de391-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="de391-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="de391-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="de391-230">Décimal</span><span class="sxs-lookup"><span data-stu-id="de391-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="de391-231">Double</span><span class="sxs-lookup"><span data-stu-id="de391-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="de391-232">Énumération</span><span class="sxs-lookup"><span data-stu-id="de391-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="de391-233">Uniques</span><span class="sxs-lookup"><span data-stu-id="de391-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="de391-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="de391-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="de391-235">Unique</span><span class="sxs-lookup"><span data-stu-id="de391-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="de391-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="de391-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="de391-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="de391-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="de391-238">Uri</span><span class="sxs-lookup"><span data-stu-id="de391-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="de391-239">Version</span><span class="sxs-lookup"><span data-stu-id="de391-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="de391-240">Types complexes</span><span class="sxs-lookup"><span data-stu-id="de391-240">Complex types</span></span>

<span data-ttu-id="de391-241">Un type complexe doit avoir un constructeur public par défaut et des propriétés publiques accessibles en écriture à lier.</span><span class="sxs-lookup"><span data-stu-id="de391-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="de391-242">Quand la liaison de modèle se produit, la classe est instanciée à l’aide du constructeur public par défaut.</span><span class="sxs-lookup"><span data-stu-id="de391-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="de391-243">Pour chaque propriété du type complexe, la liaison de modèle recherche dans les sources le modèle de nom *préfixe.nom_propriété* .</span><span class="sxs-lookup"><span data-stu-id="de391-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name* .</span></span> <span data-ttu-id="de391-244">Si rien n’est trouvé, elle recherche uniquement *nom_propriété* sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="de391-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="de391-245">Dans le cas d’une liaison à un paramètre, le préfixe représente le nom du paramètre.</span><span class="sxs-lookup"><span data-stu-id="de391-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="de391-246">Dans le cas d’une liaison à une propriété publique `PageModel`, le préfixe représente le nom de la propriété publique.</span><span class="sxs-lookup"><span data-stu-id="de391-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="de391-247">Certains attributs ont une propriété `Prefix` qui vous permet de remplacer l’utilisation par défaut du nom de paramètre ou de propriété.</span><span class="sxs-lookup"><span data-stu-id="de391-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="de391-248">Par exemple, supposons que le type complexe corresponde à la classe `Instructor` suivante :</span><span class="sxs-lookup"><span data-stu-id="de391-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="de391-249">Préfixe = nom de paramètre</span><span class="sxs-lookup"><span data-stu-id="de391-249">Prefix = parameter name</span></span>

<span data-ttu-id="de391-250">Si le modèle à lier est un paramètre nommé `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="de391-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="de391-251">La liaison de modèle commence par rechercher dans les sources la clé `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="de391-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="de391-252">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="de391-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="de391-253">Préfixe = nom de propriété</span><span class="sxs-lookup"><span data-stu-id="de391-253">Prefix = property name</span></span>

<span data-ttu-id="de391-254">Si le modèle à lier est une propriété nommée `Instructor` du contrôleur ou de la classe `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="de391-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="de391-255">La liaison de modèle commence par rechercher dans les sources la clé `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="de391-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="de391-256">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="de391-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="de391-257">Préfixe personnalisé</span><span class="sxs-lookup"><span data-stu-id="de391-257">Custom prefix</span></span>

<span data-ttu-id="de391-258">Si le modèle à lier est un paramètre nommé `instructorToUpdate` et si un attribut `Bind` spécifie `Instructor` en tant que préfixe :</span><span class="sxs-lookup"><span data-stu-id="de391-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="de391-259">La liaison de modèle commence par rechercher dans les sources la clé `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="de391-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="de391-260">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="de391-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="de391-261">Attributs des cibles de type complexe</span><span class="sxs-lookup"><span data-stu-id="de391-261">Attributes for complex type targets</span></span>

<span data-ttu-id="de391-262">Plusieurs attributs intégrés sont disponibles pour contrôler la liaison de modèle des types complexes :</span><span class="sxs-lookup"><span data-stu-id="de391-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="de391-263">Ces attributs affectent la liaison de modèle quand les données de formulaire postées représentent la source des valeurs.</span><span class="sxs-lookup"><span data-stu-id="de391-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="de391-264">Ils ne \* **pas** _ affectent les formateurs d’entrée qui traitent les corps de requête JSON et XML publiés.</span><span class="sxs-lookup"><span data-stu-id="de391-264">They do \* **not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="de391-265">Les formateurs d’entrée sont décrits [plus loin dans cet article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="de391-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="de391-266">Attribut [Bind]</span><span class="sxs-lookup"><span data-stu-id="de391-266">[Bind] attribute</span></span>

<span data-ttu-id="de391-267">Il peut être appliqué à une classe ou à un paramètre de méthode.</span><span class="sxs-lookup"><span data-stu-id="de391-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="de391-268">Il spécifie les propriétés d’un modèle à inclure dans la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="de391-269">`[Bind]` n’affecte _*_pas_*_ les formateurs d’entrée.</span><span class="sxs-lookup"><span data-stu-id="de391-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="de391-270">Dans l’exemple suivant, seules les propriétés spécifiées du modèle `Instructor` sont liées quand une méthode de gestionnaire ou une méthode d’action est appelée :</span><span class="sxs-lookup"><span data-stu-id="de391-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="de391-271">Dans l’exemple suivant, seules les propriétés spécifiées du modèle `Instructor` sont liées quand la méthode `OnPost` est appelée :</span><span class="sxs-lookup"><span data-stu-id="de391-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="de391-272">L' `[Bind]` attribut peut être utilisé pour empêcher la survalidation dans les scénarios _create \*.</span><span class="sxs-lookup"><span data-stu-id="de391-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="de391-273">Il ne fonctionne pas bien dans les scénarios de modification, car les propriétés exclues ont une valeur null ou une valeur par défaut au lieu de rester inchangées.</span><span class="sxs-lookup"><span data-stu-id="de391-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="de391-274">Pour empêcher le surpostage, il est recommandé d’utiliser des modèles de vues à la place de l’attribut `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="de391-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="de391-275">Pour plus d’informations, consultez [Remarque sur la sécurité concernant le surpostage](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="de391-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="de391-276">Attribut [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="de391-276">[BindRequired] attribute</span></span>

<span data-ttu-id="de391-277">Il s’applique uniquement aux propriétés de modèle, pas aux paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="de391-277">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="de391-278">Il oblige la liaison de modèle à ajouter une erreur d’état de modèle si la liaison est impossible pour la propriété d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-278">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="de391-279">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="de391-279">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="de391-280">Consultez également la discussion sur l’attribut `[Required]` dans [Validation de modèle](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="de391-280">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="de391-281">Attribut [BindNever]</span><span class="sxs-lookup"><span data-stu-id="de391-281">[BindNever] attribute</span></span>

<span data-ttu-id="de391-282">Il s’applique uniquement aux propriétés de modèle, pas aux paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="de391-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="de391-283">Il empêche la liaison de modèle de définir la propriété d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-283">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="de391-284">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="de391-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="de391-285">Collections</span><span class="sxs-lookup"><span data-stu-id="de391-285">Collections</span></span>

<span data-ttu-id="de391-286">Pour les cibles qui sont des collections de types simples, la liaison de modèle recherche les correspondances avec *nom_paramètre* ou *nom_propriété* .</span><span class="sxs-lookup"><span data-stu-id="de391-286">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name* .</span></span> <span data-ttu-id="de391-287">Si aucune correspondance n’est localisée, elle recherche l’un des formats pris en charge sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="de391-287">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="de391-288">Exemple :</span><span class="sxs-lookup"><span data-stu-id="de391-288">For example:</span></span>

* <span data-ttu-id="de391-289">Supposons que le paramètre à lier soit un tableau nommé `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="de391-289">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="de391-290">Les données de formulaire ou de chaîne de requête peuvent avoir l’un des formats suivants :</span><span class="sxs-lookup"><span data-stu-id="de391-290">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="de391-291">Le format suivant est pris en charge uniquement dans les données de formulaire :</span><span class="sxs-lookup"><span data-stu-id="de391-291">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="de391-292">Pour tous les exemples de formats précédents, la liaison de modèle passe un tableau de deux éléments au paramètre `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="de391-292">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="de391-293">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="de391-293">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="de391-294">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="de391-294">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="de391-295">Les formats de données qui utilisent des nombres en indice (... [0] ... [1] ...) doivent être impérativement numérotés de manière séquentielle à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="de391-295">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="de391-296">S’il existe des vides dans la numérotation en indice, tous les éléments suivants sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="de391-296">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="de391-297">Par exemple, si les indices sont 0 et 2 au lieu de 0 et 1, le second élément est ignoré.</span><span class="sxs-lookup"><span data-stu-id="de391-297">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="de391-298">Dictionnaires</span><span class="sxs-lookup"><span data-stu-id="de391-298">Dictionaries</span></span>

<span data-ttu-id="de391-299">Pour les cibles `Dictionary`, la liaison de modèle recherche les correspondances avec *nom_paramètre* ou *nom_propriété* .</span><span class="sxs-lookup"><span data-stu-id="de391-299">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name* .</span></span> <span data-ttu-id="de391-300">Si aucune correspondance n’est localisée, elle recherche l’un des formats pris en charge sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="de391-300">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="de391-301">Exemple :</span><span class="sxs-lookup"><span data-stu-id="de391-301">For example:</span></span>

* <span data-ttu-id="de391-302">Supposons que le paramètre cible soit un `Dictionary<int, string>` nommé `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="de391-302">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="de391-303">Les données de chaîne de requête ou de formulaire posté peuvent ressembler à l’un des exemples suivants :</span><span class="sxs-lookup"><span data-stu-id="de391-303">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="de391-304">Pour tous les exemples de formats précédents, la liaison de modèle passe un dictionnaire de deux éléments au paramètre `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="de391-304">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="de391-305">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="de391-305">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="de391-306">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="de391-306">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="de391-307">Liaison de constructeur et types d’enregistrements</span><span class="sxs-lookup"><span data-stu-id="de391-307">Constructor binding and record types</span></span>

<span data-ttu-id="de391-308">La liaison de modèle requiert que les types complexes aient un constructeur sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="de391-308">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="de391-309">`System.Text.Json`Et les `Newtonsoft.Json` formateurs d’entrée de base prennent en charge la désérialisation des classes qui n’ont pas de constructeur sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="de391-309">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="de391-310">C# 9 introduit les types d’enregistrements, qui constituent un excellent moyen de représenter succinctement les données sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="de391-310">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="de391-311">ASP.NET Core ajoute la prise en charge de la liaison de modèle et de la validation des types d’enregistrements avec un constructeur unique :</span><span class="sxs-lookup"><span data-stu-id="de391-311">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="de391-312">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="de391-312">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="de391-313">Lors de la validation des types d’enregistrements, le runtime recherche les métadonnées de validation spécifiquement sur les paramètres plutôt que sur les propriétés.</span><span class="sxs-lookup"><span data-stu-id="de391-313">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="de391-314">Comportement de globalisation des données de routage de liaison de modèle et des chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="de391-314">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="de391-315">Fournisseur de valeurs d’itinéraire ASP.NET Core et fournisseur de valeur de chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="de391-315">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="de391-316">Traitez les valeurs comme une culture dite indifférente.</span><span class="sxs-lookup"><span data-stu-id="de391-316">Treat values as invariant culture.</span></span>
* <span data-ttu-id="de391-317">Attendez-vous à ce que les URL soient invariantes de culture.</span><span class="sxs-lookup"><span data-stu-id="de391-317">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="de391-318">En revanche, les valeurs provenant des données de formulaire subissent une conversion dépendante de la culture.</span><span class="sxs-lookup"><span data-stu-id="de391-318">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="de391-319">Cela est dû au fait que les URL peuvent être partagées entre les paramètres régionaux.</span><span class="sxs-lookup"><span data-stu-id="de391-319">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="de391-320">Pour que le fournisseur de valeurs d’itinéraire ASP.NET Core et le fournisseur de valeurs de chaîne de requête soient soumis à une conversion dépendante de la culture :</span><span class="sxs-lookup"><span data-stu-id="de391-320">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="de391-321">Héritent de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="de391-321">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="de391-322">Copiez le code à partir de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="de391-322">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="de391-323">Remplacer la [valeur de culture](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passée au constructeur de fournisseur de valeur par [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="de391-323">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="de391-324">Remplacez la fabrique de fournisseur de valeur par défaut dans les options MVC par la nouvelle.</span><span class="sxs-lookup"><span data-stu-id="de391-324">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="de391-325">Types de données spéciaux</span><span class="sxs-lookup"><span data-stu-id="de391-325">Special data types</span></span>

<span data-ttu-id="de391-326">Certains types de données spéciaux peuvent être pris en charge par la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-326">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="de391-327">IFormFile et IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="de391-327">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="de391-328">Fichier chargé inclus dans la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="de391-328">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="de391-329">`IEnumerable<IFormFile>` est également pris en charge pour plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="de391-329">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="de391-330">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="de391-330">CancellationToken</span></span>

<span data-ttu-id="de391-331">utilisé pour annuler l’activité dans les contrôleurs asynchrones.</span><span class="sxs-lookup"><span data-stu-id="de391-331">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="de391-332">FormCollection</span><span class="sxs-lookup"><span data-stu-id="de391-332">FormCollection</span></span>

<span data-ttu-id="de391-333">Permet de récupérer toutes les valeurs des données de formulaire posté.</span><span class="sxs-lookup"><span data-stu-id="de391-333">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="de391-334">Formateurs d’entrée</span><span class="sxs-lookup"><span data-stu-id="de391-334">Input formatters</span></span>

<span data-ttu-id="de391-335">Les données contenues dans le corps de la requête peuvent être au format JSON, XML ou tout autre format.</span><span class="sxs-lookup"><span data-stu-id="de391-335">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="de391-336">Pour analyser ces données, la liaison de modèle utilise un *formateur d’entrée* configuré pour prendre en charge un type de contenu particulier.</span><span class="sxs-lookup"><span data-stu-id="de391-336">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="de391-337">Par défaut, ASP.NET Core inclut des formateurs d’entrée basés sur le format JSON pour prendre en charge les données JSON.</span><span class="sxs-lookup"><span data-stu-id="de391-337">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="de391-338">Vous pouvez ajouter d’autres formateurs pour d’autres types de contenu.</span><span class="sxs-lookup"><span data-stu-id="de391-338">You can add other formatters for other content types.</span></span>

<span data-ttu-id="de391-339">ASP.NET Core sélectionne les formateurs d’entrée en fonction de l’attribut [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="de391-339">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="de391-340">Si aucun attribut n’est présent, il utilise l’[en-tête Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="de391-340">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="de391-341">Pour utiliser les formateurs d’entrée XML intégrés :</span><span class="sxs-lookup"><span data-stu-id="de391-341">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="de391-342">Installez le package NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="de391-342">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="de391-343">Dans `Startup.ConfigureServices`, appelez <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="de391-343">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="de391-344">Appliquez l’attribut `Consumes` aux classes de contrôleur ou aux méthodes d’action devant contenir des données XML dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="de391-344">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="de391-345">Pour plus d’informations, consultez [Introduction à la sérialisation XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="de391-345">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="de391-346">Personnaliser la liaison de modèle avec des formateurs d’entrée</span><span class="sxs-lookup"><span data-stu-id="de391-346">Customize model binding with input formatters</span></span>

<span data-ttu-id="de391-347">Un formateur d’entrée est entièrement chargé de lire les données dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="de391-347">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="de391-348">Pour personnaliser ce processus, configurez les API utilisées par le formateur d’entrée.</span><span class="sxs-lookup"><span data-stu-id="de391-348">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="de391-349">Cette section décrit comment personnaliser le `System.Text.Json` formateur d’entrée basé sur pour comprendre un type personnalisé nommé `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="de391-349">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="de391-350">Prenons le modèle suivant, qui contient une `ObjectId` propriété personnalisée nommée `Id` :</span><span class="sxs-lookup"><span data-stu-id="de391-350">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="de391-351">Pour personnaliser le processus de liaison de modèle lors de l’utilisation de `System.Text.Json` , créez une classe dérivée de <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="de391-351">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="de391-352">Pour utiliser un convertisseur personnalisé, appliquez l' <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribut au type.</span><span class="sxs-lookup"><span data-stu-id="de391-352">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="de391-353">Dans l’exemple suivant, le `ObjectId` type est configuré avec `ObjectIdConverter` comme son convertisseur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="de391-353">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="de391-354">Pour plus d’informations, consultez [Comment écrire des convertisseurs personnalisés](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="de391-354">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="de391-355">Exclure les types spécifiés de la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="de391-355">Exclude specified types from model binding</span></span>

<span data-ttu-id="de391-356">Le comportement de la liaison de modèle et du système de validation est régi par [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="de391-356">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="de391-357">Vous pouvez personnaliser `ModelMetadata` en ajoutant un fournisseur de détails à [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="de391-357">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="de391-358">Des fournisseurs de détails intégrés sont disponibles pour désactiver la liaison de modèle ou la validation des types spécifiés.</span><span class="sxs-lookup"><span data-stu-id="de391-358">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="de391-359">Pour désactiver la liaison de modèle sur tous les modèles d’un type spécifique, ajoutez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="de391-359">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="de391-360">Par exemple, pour désactiver la liaison de modèle sur tous les modèles de type `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="de391-360">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="de391-361">Pour désactiver la validation des propriétés d’un type spécifique, ajoutez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="de391-361">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="de391-362">Par exemple, pour désactiver la validation sur les propriétés de type `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="de391-362">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="de391-363">Lieurs de modèles personnalisés</span><span class="sxs-lookup"><span data-stu-id="de391-363">Custom model binders</span></span>

<span data-ttu-id="de391-364">Vous pouvez étendre la liaison de modèle en écrivant un lieur de modèle personnalisé et en utilisant l’attribut `[ModelBinder]` afin de le sélectionner pour une cible donnée.</span><span class="sxs-lookup"><span data-stu-id="de391-364">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="de391-365">Découvrez plus d’informations sur la [liaison de modèle personnalisée](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="de391-365">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="de391-366">Liaison de modèle manuelle</span><span class="sxs-lookup"><span data-stu-id="de391-366">Manual model binding</span></span> 

<span data-ttu-id="de391-367">Vous pouvez appeler la liaison de modèle manuellement à l’aide de la méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="de391-367">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="de391-368">La méthode est définie sur les classes `ControllerBase` et `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="de391-368">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="de391-369">Les surcharges de méthode vous permettent de spécifier le préfixe et le fournisseur de valeurs à utiliser.</span><span class="sxs-lookup"><span data-stu-id="de391-369">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="de391-370">La méthode retourne `false` en cas d’échec de la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-370">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="de391-371">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="de391-371">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="de391-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  utilise des fournisseurs de valeurs pour obtenir des données à partir du corps du formulaire, la chaîne de requête et les données d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="de391-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="de391-373">`TryUpdateModelAsync` est généralement :</span><span class="sxs-lookup"><span data-stu-id="de391-373">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="de391-374">Utilisé avec les :::no-loc(Razor)::: pages et les applications MVC à l’aide de contrôleurs et de vues pour empêcher la survalidation.</span><span class="sxs-lookup"><span data-stu-id="de391-374">Used with :::no-loc(Razor)::: Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="de391-375">Non utilisé avec une API Web, sauf s’il est consommé à partir des données de formulaire, des chaînes de requête et des données de routage.</span><span class="sxs-lookup"><span data-stu-id="de391-375">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="de391-376">Les points de terminaison de l’API Web qui utilisent JSON utilisent des [formateurs d’entrée](#input-formatters) pour désérialiser le corps de la requête dans un objet.</span><span class="sxs-lookup"><span data-stu-id="de391-376">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="de391-377">Pour plus d’informations, consultez [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="de391-377">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="de391-378">Attribut [FromServices]</span><span class="sxs-lookup"><span data-stu-id="de391-378">[FromServices] attribute</span></span>

<span data-ttu-id="de391-379">Le nom de cet attribut suit le modèle des attributs de liaison de modèle qui spécifient une source de données.</span><span class="sxs-lookup"><span data-stu-id="de391-379">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="de391-380">Toutefois, il ne permet pas de lier les données d’un fournisseur de valeurs.</span><span class="sxs-lookup"><span data-stu-id="de391-380">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="de391-381">Il obtient une instance d’un type à partir du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="de391-381">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="de391-382">Son objectif est de fournir une alternative à l’injection de constructeurs quand vous avez besoin d’un service uniquement si une méthode particulière est appelée.</span><span class="sxs-lookup"><span data-stu-id="de391-382">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="de391-383">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="de391-383">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="de391-384">Cet article explique ce qu’est la liaison de modèle, comment elle fonctionne et comment personnaliser son comportement.</span><span class="sxs-lookup"><span data-stu-id="de391-384">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="de391-385">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="de391-385">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="de391-386">Description de la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="de391-386">What is Model binding</span></span>

<span data-ttu-id="de391-387">Les contrôleurs et les :::no-loc(Razor)::: pages fonctionnent avec des données provenant de requêtes http.</span><span class="sxs-lookup"><span data-stu-id="de391-387">Controllers and :::no-loc(Razor)::: pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="de391-388">Par exemple, les données de routage peuvent fournir une clé d’enregistrement, et les champs de formulaire posté peuvent fournir des valeurs pour les propriétés du modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-388">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="de391-389">L’écriture du code permettant de récupérer chacune de ces valeurs et de les convertir en types .NET à partir de chaînes est fastidieuse et source d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="de391-389">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="de391-390">La liaison de modèle automatise ce processus.</span><span class="sxs-lookup"><span data-stu-id="de391-390">Model binding automates this process.</span></span> <span data-ttu-id="de391-391">Le système de liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="de391-391">The model binding system:</span></span>

* <span data-ttu-id="de391-392">Récupère les données de diverses sources telles que les données de routage, les champs de formulaire et les chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="de391-392">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="de391-393">Fournit les données aux contrôleurs et aux :::no-loc(Razor)::: pages dans les paramètres de méthode et les propriétés publiques.</span><span class="sxs-lookup"><span data-stu-id="de391-393">Provides the data to controllers and :::no-loc(Razor)::: pages in method parameters and public properties.</span></span>
* <span data-ttu-id="de391-394">Convertit les données de chaîne en types .NET</span><span class="sxs-lookup"><span data-stu-id="de391-394">Converts string data to .NET types.</span></span>
* <span data-ttu-id="de391-395">Met à jour les propriétés des types complexes</span><span class="sxs-lookup"><span data-stu-id="de391-395">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="de391-396">Exemple</span><span class="sxs-lookup"><span data-stu-id="de391-396">Example</span></span>

<span data-ttu-id="de391-397">Supposons que vous ayez la méthode d’action suivante :</span><span class="sxs-lookup"><span data-stu-id="de391-397">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="de391-398">Et que l’application reçoive une requête avec l’URL suivante :</span><span class="sxs-lookup"><span data-stu-id="de391-398">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="de391-399">La liaison de modèle passe par les étapes suivantes après que le système de routage a sélectionné la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="de391-399">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="de391-400">Elle recherche le premier paramètre de `GetByID`, un entier nommé `id`.</span><span class="sxs-lookup"><span data-stu-id="de391-400">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="de391-401">Elle parcourt les sources disponibles dans la requête HTTP et trouve `id` = « 2 » dans les données de routage.</span><span class="sxs-lookup"><span data-stu-id="de391-401">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="de391-402">Elle convertit la chaîne « 2 » en entier 2.</span><span class="sxs-lookup"><span data-stu-id="de391-402">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="de391-403">Elle recherche le paramètre suivant de `GetByID`, un booléen nommé `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="de391-403">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="de391-404">Elle parcourt les sources et trouve « DogsOnly=true » dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="de391-404">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="de391-405">La mise en correspondance des noms ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="de391-405">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="de391-406">Elle convertit la chaîne « true » en booléen `true`.</span><span class="sxs-lookup"><span data-stu-id="de391-406">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="de391-407">Le framework appelle ensuite la méthode `GetById`, en passant 2 pour le paramètre `id`, et `true` pour le paramètre `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="de391-407">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="de391-408">Dans l’exemple précédent, les cibles de liaison de modèle sont des paramètres de méthode qui sont des types simples.</span><span class="sxs-lookup"><span data-stu-id="de391-408">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="de391-409">Les cibles peuvent être également les propriétés d’un type complexe.</span><span class="sxs-lookup"><span data-stu-id="de391-409">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="de391-410">Une fois chaque propriété correctement liée, la [validation du modèle](xref:mvc/models/validation) est effectuée pour la propriété concernée.</span><span class="sxs-lookup"><span data-stu-id="de391-410">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="de391-411">L’enregistrement des données liées au modèle (ainsi que des erreurs de liaison ou de validation) est stocké dans [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="de391-411">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="de391-412">Pour savoir si ce processus a abouti, l’application vérifie l’indicateur [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="de391-412">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="de391-413">Cibles</span><span class="sxs-lookup"><span data-stu-id="de391-413">Targets</span></span>

<span data-ttu-id="de391-414">La liaison de modèle tente de trouver des valeurs pour les genres de cible suivants :</span><span class="sxs-lookup"><span data-stu-id="de391-414">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="de391-415">Paramètres de la méthode d’action de contrôleur vers laquelle une requête est routée.</span><span class="sxs-lookup"><span data-stu-id="de391-415">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="de391-416">Paramètres de la :::no-loc(Razor)::: méthode de gestionnaire de pages vers laquelle une requête est routée.</span><span class="sxs-lookup"><span data-stu-id="de391-416">Parameters of the :::no-loc(Razor)::: Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="de391-417">Propriétés publiques d’un contrôleur ou d’une classe `PageModel`, si elles sont spécifiées par des attributs.</span><span class="sxs-lookup"><span data-stu-id="de391-417">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="de391-418">Attribut [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="de391-418">[BindProperty] attribute</span></span>

<span data-ttu-id="de391-419">Peut être appliqué à une propriété publique d’un contrôleur ou à une classe `PageModel` pour que la liaison de modèle cible cette propriété :</span><span class="sxs-lookup"><span data-stu-id="de391-419">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="de391-420">Attribut [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="de391-420">[BindProperties] attribute</span></span>

<span data-ttu-id="de391-421">Disponible avec ASP.NET Core 2.1 et les versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="de391-421">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="de391-422">Peut être appliqué à un contrôleur ou à une classe `PageModel` pour indiquer à la liaison de modèle de cibler toutes les propriétés publiques de la classe :</span><span class="sxs-lookup"><span data-stu-id="de391-422">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="de391-423">Liaison de modèle pour les requêtes HTTP GET</span><span class="sxs-lookup"><span data-stu-id="de391-423">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="de391-424">Par défaut, les propriétés ne sont pas liées pour les requêtes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="de391-424">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="de391-425">En règle générale, le paramètre ID d’un enregistrement est tout ce dont vous avez besoin pour une requête GET.</span><span class="sxs-lookup"><span data-stu-id="de391-425">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="de391-426">L’ID d’enregistrement est utilisé pour rechercher l’élément dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="de391-426">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="de391-427">Il n’est donc pas nécessaire de lier une propriété qui contient une instance du modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-427">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="de391-428">Pour les scénarios dans lesquels vous souhaitez que les propriétés soient liées aux données provenant de requêtes GET, affectez à la propriété `SupportsGet` la valeur `true` :</span><span class="sxs-lookup"><span data-stu-id="de391-428">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="de391-429">Sources</span><span class="sxs-lookup"><span data-stu-id="de391-429">Sources</span></span>

<span data-ttu-id="de391-430">Par défaut, la liaison de modèle obtient des données sous la forme de paires clé-valeur à partir des sources suivantes dans une requête HTTP :</span><span class="sxs-lookup"><span data-stu-id="de391-430">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="de391-431">Champs de formulaire</span><span class="sxs-lookup"><span data-stu-id="de391-431">Form fields</span></span>
1. <span data-ttu-id="de391-432">Corps de la requête (pour les [contrôleurs ayant l’attribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="de391-432">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="de391-433">Données de routage</span><span class="sxs-lookup"><span data-stu-id="de391-433">Route data</span></span>
1. <span data-ttu-id="de391-434">Paramètres de chaîne de requête</span><span class="sxs-lookup"><span data-stu-id="de391-434">Query string parameters</span></span>
1. <span data-ttu-id="de391-435">Fichiers chargés</span><span class="sxs-lookup"><span data-stu-id="de391-435">Uploaded files</span></span>

<span data-ttu-id="de391-436">Pour chaque paramètre ou propriété cible, les sources sont analysées dans l’ordre indiqué dans la liste précédente.</span><span class="sxs-lookup"><span data-stu-id="de391-436">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="de391-437">Il existe quelques exceptions :</span><span class="sxs-lookup"><span data-stu-id="de391-437">There are a few exceptions:</span></span>

* <span data-ttu-id="de391-438">Les données de routage et les valeurs de chaîne de requête sont utilisées uniquement pour les types simples.</span><span class="sxs-lookup"><span data-stu-id="de391-438">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="de391-439">Les fichiers chargés sont liés uniquement aux types cibles qui implémentent `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="de391-439">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="de391-440">Si la source par défaut n’est pas correcte, utilisez l’un des attributs suivants pour spécifier la source :</span><span class="sxs-lookup"><span data-stu-id="de391-440">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="de391-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Obtient des valeurs à partir de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="de391-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="de391-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Obtient des valeurs à partir des données d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="de391-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="de391-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Obtient des valeurs à partir de champs de formulaire publiés.</span><span class="sxs-lookup"><span data-stu-id="de391-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="de391-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Obtient les valeurs du corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="de391-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="de391-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Obtient des valeurs à partir des en-têtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="de391-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="de391-446">Ces attributs :</span><span class="sxs-lookup"><span data-stu-id="de391-446">These attributes:</span></span>

* <span data-ttu-id="de391-447">Sont ajoutés aux propriétés du modèle individuellement (et non à la classe de modèle), comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="de391-447">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="de391-448">Acceptent éventuellement une valeur de nom de modèle dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="de391-448">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="de391-449">Cette option est fournie au cas où le nom de propriété ne correspondrait pas à la valeur de la requête.</span><span class="sxs-lookup"><span data-stu-id="de391-449">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="de391-450">Par exemple, la valeur de la requête peut être un en-tête avec un trait d’union dans son nom, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="de391-450">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="de391-451">Attribut [FromBody]</span><span class="sxs-lookup"><span data-stu-id="de391-451">[FromBody] attribute</span></span>

<span data-ttu-id="de391-452">Appliquez l' `[FromBody]` attribut à un paramètre pour remplir ses propriétés à partir du corps d’une requête http.</span><span class="sxs-lookup"><span data-stu-id="de391-452">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="de391-453">Le runtime ASP.NET Core délègue la responsabilité de lire le corps dans un formateur d’entrée.</span><span class="sxs-lookup"><span data-stu-id="de391-453">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="de391-454">Les formateurs d’entrée sont décrits [plus loin dans cet article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="de391-454">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="de391-455">Lorsque `[FromBody]` est appliqué à un paramètre de type complexe, tous les attributs de source de liaison appliqués à ses propriétés sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="de391-455">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="de391-456">Par exemple, l' `Create` action suivante spécifie que son `pet` paramètre est rempli à partir du corps :</span><span class="sxs-lookup"><span data-stu-id="de391-456">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="de391-457">La `Pet` classe spécifie que sa `Breed` propriété est remplie à partir d’un paramètre de chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="de391-457">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="de391-458">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="de391-458">In the preceding example:</span></span>

* <span data-ttu-id="de391-459">L' `[FromQuery]` attribut est ignoré.</span><span class="sxs-lookup"><span data-stu-id="de391-459">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="de391-460">La `Breed` propriété n’est pas remplie à partir d’un paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="de391-460">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="de391-461">Les formateurs d’entrée lisent uniquement le corps et ne comprennent pas les attributs de source de liaison.</span><span class="sxs-lookup"><span data-stu-id="de391-461">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="de391-462">Si une valeur appropriée est trouvée dans le corps, cette valeur est utilisée pour remplir la `Breed` propriété.</span><span class="sxs-lookup"><span data-stu-id="de391-462">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="de391-463">N’appliquez pas `[FromBody]` à plus d’un paramètre par méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="de391-463">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="de391-464">Une fois que le flux de requête est lu par un formateur d’entrée, il ne peut plus être lu pour la liaison d’autres `[FromBody]` paramètres.</span><span class="sxs-lookup"><span data-stu-id="de391-464">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="de391-465">Sources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="de391-465">Additional sources</span></span>

<span data-ttu-id="de391-466">Les données sources sont fournies au système de liaison de modèle par les *fournisseurs de valeurs* .</span><span class="sxs-lookup"><span data-stu-id="de391-466">Source data is provided to the model binding system by *value providers* .</span></span> <span data-ttu-id="de391-467">Vous pouvez écrire et inscrire des fournisseurs de valeurs personnalisés qui obtiennent des données de liaison de modèle à partir d’autres sources.</span><span class="sxs-lookup"><span data-stu-id="de391-467">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="de391-468">Par exemple, vous pouvez obtenir des données à partir de ou de l' :::no-loc(cookie)::: État de session.</span><span class="sxs-lookup"><span data-stu-id="de391-468">For example, you might want data from :::no-loc(cookie):::s or session state.</span></span> <span data-ttu-id="de391-469">Pour obtenir des données provenant d’une nouvelle source :</span><span class="sxs-lookup"><span data-stu-id="de391-469">To get data from a new source:</span></span>

* <span data-ttu-id="de391-470">Créez une classe qui implémente `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="de391-470">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="de391-471">Créez une classe qui implémente `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="de391-471">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="de391-472">Inscrivez la classe de fabrique dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="de391-472">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="de391-473">L’exemple d’application comprend un [fournisseur de valeur](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) et un exemple d' [usine](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) qui obtient des valeurs de :::no-loc(cookie)::: s.</span><span class="sxs-lookup"><span data-stu-id="de391-473">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) example that gets values from :::no-loc(cookie):::s.</span></span> <span data-ttu-id="de391-474">Voici le code d’inscription dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="de391-474">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="de391-475">Le code affiché place le fournisseur de valeurs personnalisé après tous les fournisseurs de valeurs intégrés.</span><span class="sxs-lookup"><span data-stu-id="de391-475">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="de391-476">Pour en faire le premier fournisseur de la liste, appelez `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` à la place de `Add`.</span><span class="sxs-lookup"><span data-stu-id="de391-476">To make it the first in the list, call `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="de391-477">Aucune source pour une propriété de modèle</span><span class="sxs-lookup"><span data-stu-id="de391-477">No source for a model property</span></span>

<span data-ttu-id="de391-478">Par défaut, aucune erreur d’état de modèle n’est créée, s’il n’existe aucune valeur de propriété de modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-478">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="de391-479">La propriété a une valeur null ou une valeur par défaut :</span><span class="sxs-lookup"><span data-stu-id="de391-479">The property is set to null or a default value:</span></span>

* <span data-ttu-id="de391-480">Les types simples Nullable ont une valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="de391-480">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="de391-481">Les types valeur non Nullable ont la valeur `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="de391-481">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="de391-482">Par exemple, un paramètre `int id` a la valeur 0.</span><span class="sxs-lookup"><span data-stu-id="de391-482">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="de391-483">Pour les types complexes, la liaison de modèle crée une instance à l’aide du constructeur par défaut, sans définir de propriétés.</span><span class="sxs-lookup"><span data-stu-id="de391-483">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="de391-484">Les tableaux ont la valeur `Array.Empty<T>()`, sauf les tableaux `byte[]` qui ont une valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="de391-484">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="de391-485">Si l’état du modèle doit être invalidé lorsque rien n’est trouvé dans les champs de formulaire d’une propriété de modèle, utilisez l' [`[BindRequired]`](#bindrequired-attribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="de391-485">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="de391-486">Notez que ce comportement de `[BindRequired]` s’applique à la liaison de modèle des données de formulaire postées, et non aux données JSON ou XML d’un corps de requête.</span><span class="sxs-lookup"><span data-stu-id="de391-486">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="de391-487">Les données du corps de requête sont prises en charge par les [formateurs d’entrée](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="de391-487">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="de391-488">Erreurs de conversion de type</span><span class="sxs-lookup"><span data-stu-id="de391-488">Type conversion errors</span></span>

<span data-ttu-id="de391-489">Si une source est localisée mais qu’elle ne peut pas être convertie vers le type cible, l’état du modèle est marqué comme étant non valide.</span><span class="sxs-lookup"><span data-stu-id="de391-489">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="de391-490">Le paramètre ou la propriété cible a une valeur null ou une valeur par défaut, comme indiqué dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="de391-490">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="de391-491">Dans un contrôleur d’API ayant l’attribut `[ApiController]`, un état de modèle non valide entraîne une réponse HTTP 400 automatique.</span><span class="sxs-lookup"><span data-stu-id="de391-491">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="de391-492">Dans une :::no-loc(Razor)::: page, réaffichez la page avec un message d’erreur :</span><span class="sxs-lookup"><span data-stu-id="de391-492">In a :::no-loc(Razor)::: page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="de391-493">La validation côté client intercepte la plupart des données incorrectes qui seraient autrement soumises à un :::no-loc(Razor)::: formulaire de pages.</span><span class="sxs-lookup"><span data-stu-id="de391-493">Client-side validation catches most bad data that would otherwise be submitted to a :::no-loc(Razor)::: Pages form.</span></span> <span data-ttu-id="de391-494">Cette validation rend difficile le déclenchement du code en surbrillance indiqué plus haut.</span><span class="sxs-lookup"><span data-stu-id="de391-494">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="de391-495">L’exemple d’application comprend un bouton **Submit with Invalid Date** (Envoyer avec une date non valide), qui place les données incorrectes dans le champ **Hire Date** (Date d’embauche) et envoie le formulaire.</span><span class="sxs-lookup"><span data-stu-id="de391-495">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="de391-496">Ce bouton montre comment fonctionne le code permettant de réafficher la page quand des erreurs de conversion de données se produisent.</span><span class="sxs-lookup"><span data-stu-id="de391-496">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="de391-497">Quand la page est réaffichée par le code précédent, l’entrée non valide n’est pas visible dans le champ de formulaire.</span><span class="sxs-lookup"><span data-stu-id="de391-497">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="de391-498">En effet, la propriété de modèle à une valeur null ou une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="de391-498">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="de391-499">L’entrée non valide apparaît dans un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="de391-499">The invalid input does appear in an error message.</span></span> <span data-ttu-id="de391-500">Toutefois, si vous souhaitez réafficher les données incorrectes dans le champ de formulaire, transformez la propriété de modèle en chaîne et procédez à la conversion des données manuellement.</span><span class="sxs-lookup"><span data-stu-id="de391-500">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="de391-501">La même stratégie est recommandée si vous ne souhaitez pas que les erreurs de conversion de type entraînent des erreurs d’état de modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-501">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="de391-502">Dans ce cas, transformez la propriété de modèle en chaîne.</span><span class="sxs-lookup"><span data-stu-id="de391-502">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="de391-503">Types simples</span><span class="sxs-lookup"><span data-stu-id="de391-503">Simple types</span></span>

<span data-ttu-id="de391-504">Les types simples que le lieur de modèle peut convertir en chaînes sources sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="de391-504">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="de391-505">Booléen</span><span class="sxs-lookup"><span data-stu-id="de391-505">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="de391-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="de391-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="de391-507">Char</span><span class="sxs-lookup"><span data-stu-id="de391-507">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="de391-508">DateTime</span><span class="sxs-lookup"><span data-stu-id="de391-508">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="de391-509">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="de391-509">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="de391-510">Décimal</span><span class="sxs-lookup"><span data-stu-id="de391-510">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="de391-511">Double</span><span class="sxs-lookup"><span data-stu-id="de391-511">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="de391-512">Énumération</span><span class="sxs-lookup"><span data-stu-id="de391-512">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="de391-513">Uniques</span><span class="sxs-lookup"><span data-stu-id="de391-513">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="de391-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="de391-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="de391-515">Unique</span><span class="sxs-lookup"><span data-stu-id="de391-515">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="de391-516">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="de391-516">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="de391-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="de391-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="de391-518">Uri</span><span class="sxs-lookup"><span data-stu-id="de391-518">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="de391-519">Version</span><span class="sxs-lookup"><span data-stu-id="de391-519">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="de391-520">Types complexes</span><span class="sxs-lookup"><span data-stu-id="de391-520">Complex types</span></span>

<span data-ttu-id="de391-521">Un type complexe doit avoir un constructeur public par défaut et des propriétés publiques accessibles en écriture à lier.</span><span class="sxs-lookup"><span data-stu-id="de391-521">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="de391-522">Quand la liaison de modèle se produit, la classe est instanciée à l’aide du constructeur public par défaut.</span><span class="sxs-lookup"><span data-stu-id="de391-522">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="de391-523">Pour chaque propriété du type complexe, la liaison de modèle recherche dans les sources le modèle de nom *préfixe.nom_propriété* .</span><span class="sxs-lookup"><span data-stu-id="de391-523">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name* .</span></span> <span data-ttu-id="de391-524">Si rien n’est trouvé, elle recherche uniquement *nom_propriété* sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="de391-524">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="de391-525">Dans le cas d’une liaison à un paramètre, le préfixe représente le nom du paramètre.</span><span class="sxs-lookup"><span data-stu-id="de391-525">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="de391-526">Dans le cas d’une liaison à une propriété publique `PageModel`, le préfixe représente le nom de la propriété publique.</span><span class="sxs-lookup"><span data-stu-id="de391-526">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="de391-527">Certains attributs ont une propriété `Prefix` qui vous permet de remplacer l’utilisation par défaut du nom de paramètre ou de propriété.</span><span class="sxs-lookup"><span data-stu-id="de391-527">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="de391-528">Par exemple, supposons que le type complexe corresponde à la classe `Instructor` suivante :</span><span class="sxs-lookup"><span data-stu-id="de391-528">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="de391-529">Préfixe = nom de paramètre</span><span class="sxs-lookup"><span data-stu-id="de391-529">Prefix = parameter name</span></span>

<span data-ttu-id="de391-530">Si le modèle à lier est un paramètre nommé `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="de391-530">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="de391-531">La liaison de modèle commence par rechercher dans les sources la clé `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="de391-531">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="de391-532">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="de391-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="de391-533">Préfixe = nom de propriété</span><span class="sxs-lookup"><span data-stu-id="de391-533">Prefix = property name</span></span>

<span data-ttu-id="de391-534">Si le modèle à lier est une propriété nommée `Instructor` du contrôleur ou de la classe `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="de391-534">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="de391-535">La liaison de modèle commence par rechercher dans les sources la clé `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="de391-535">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="de391-536">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="de391-536">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="de391-537">Préfixe personnalisé</span><span class="sxs-lookup"><span data-stu-id="de391-537">Custom prefix</span></span>

<span data-ttu-id="de391-538">Si le modèle à lier est un paramètre nommé `instructorToUpdate` et si un attribut `Bind` spécifie `Instructor` en tant que préfixe :</span><span class="sxs-lookup"><span data-stu-id="de391-538">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="de391-539">La liaison de modèle commence par rechercher dans les sources la clé `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="de391-539">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="de391-540">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="de391-540">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="de391-541">Attributs des cibles de type complexe</span><span class="sxs-lookup"><span data-stu-id="de391-541">Attributes for complex type targets</span></span>

<span data-ttu-id="de391-542">Plusieurs attributs intégrés sont disponibles pour contrôler la liaison de modèle des types complexes :</span><span class="sxs-lookup"><span data-stu-id="de391-542">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="de391-543">Ces attributs affectent la liaison de modèle quand les données de formulaire postées représentent la source des valeurs.</span><span class="sxs-lookup"><span data-stu-id="de391-543">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="de391-544">Ils n’affectent pas les formateurs d’entrée, qui traitent les corps de requête JSON et XML postés.</span><span class="sxs-lookup"><span data-stu-id="de391-544">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="de391-545">Les formateurs d’entrée sont décrits [plus loin dans cet article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="de391-545">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="de391-546">Consultez également la discussion sur l’attribut `[Required]` dans [Validation de modèle](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="de391-546">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="de391-547">Attribut [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="de391-547">[BindRequired] attribute</span></span>

<span data-ttu-id="de391-548">Il s’applique uniquement aux propriétés de modèle, pas aux paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="de391-548">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="de391-549">Il oblige la liaison de modèle à ajouter une erreur d’état de modèle si la liaison est impossible pour la propriété d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-549">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="de391-550">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="de391-550">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="de391-551">Attribut [BindNever]</span><span class="sxs-lookup"><span data-stu-id="de391-551">[BindNever] attribute</span></span>

<span data-ttu-id="de391-552">Il s’applique uniquement aux propriétés de modèle, pas aux paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="de391-552">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="de391-553">Il empêche la liaison de modèle de définir la propriété d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-553">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="de391-554">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="de391-554">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="de391-555">Attribut [Bind]</span><span class="sxs-lookup"><span data-stu-id="de391-555">[Bind] attribute</span></span>

<span data-ttu-id="de391-556">Il peut être appliqué à une classe ou à un paramètre de méthode.</span><span class="sxs-lookup"><span data-stu-id="de391-556">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="de391-557">Il spécifie les propriétés d’un modèle à inclure dans la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-557">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="de391-558">Dans l’exemple suivant, seules les propriétés spécifiées du modèle `Instructor` sont liées quand une méthode de gestionnaire ou une méthode d’action est appelée :</span><span class="sxs-lookup"><span data-stu-id="de391-558">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="de391-559">Dans l’exemple suivant, seules les propriétés spécifiées du modèle `Instructor` sont liées quand la méthode `OnPost` est appelée :</span><span class="sxs-lookup"><span data-stu-id="de391-559">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="de391-560">Vous pouvez utiliser l’attribut `[Bind]` pour éviter le surpostage dans les scénarios de *création* .</span><span class="sxs-lookup"><span data-stu-id="de391-560">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="de391-561">Il ne fonctionne pas bien dans les scénarios de modification, car les propriétés exclues ont une valeur null ou une valeur par défaut au lieu de rester inchangées.</span><span class="sxs-lookup"><span data-stu-id="de391-561">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="de391-562">Pour empêcher le surpostage, il est recommandé d’utiliser des modèles de vues à la place de l’attribut `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="de391-562">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="de391-563">Pour plus d’informations, consultez [Remarque sur la sécurité concernant le surpostage](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="de391-563">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="de391-564">Collections</span><span class="sxs-lookup"><span data-stu-id="de391-564">Collections</span></span>

<span data-ttu-id="de391-565">Pour les cibles qui sont des collections de types simples, la liaison de modèle recherche les correspondances avec *nom_paramètre* ou *nom_propriété* .</span><span class="sxs-lookup"><span data-stu-id="de391-565">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name* .</span></span> <span data-ttu-id="de391-566">Si aucune correspondance n’est localisée, elle recherche l’un des formats pris en charge sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="de391-566">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="de391-567">Exemple :</span><span class="sxs-lookup"><span data-stu-id="de391-567">For example:</span></span>

* <span data-ttu-id="de391-568">Supposons que le paramètre à lier soit un tableau nommé `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="de391-568">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="de391-569">Les données de formulaire ou de chaîne de requête peuvent avoir l’un des formats suivants :</span><span class="sxs-lookup"><span data-stu-id="de391-569">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="de391-570">Le format suivant est pris en charge uniquement dans les données de formulaire :</span><span class="sxs-lookup"><span data-stu-id="de391-570">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="de391-571">Pour tous les exemples de formats précédents, la liaison de modèle passe un tableau de deux éléments au paramètre `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="de391-571">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="de391-572">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="de391-572">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="de391-573">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="de391-573">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="de391-574">Les formats de données qui utilisent des nombres en indice (... [0] ... [1] ...) doivent être impérativement numérotés de manière séquentielle à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="de391-574">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="de391-575">S’il existe des vides dans la numérotation en indice, tous les éléments suivants sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="de391-575">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="de391-576">Par exemple, si les indices sont 0 et 2 au lieu de 0 et 1, le second élément est ignoré.</span><span class="sxs-lookup"><span data-stu-id="de391-576">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="de391-577">Dictionnaires</span><span class="sxs-lookup"><span data-stu-id="de391-577">Dictionaries</span></span>

<span data-ttu-id="de391-578">Pour les cibles `Dictionary`, la liaison de modèle recherche les correspondances avec *nom_paramètre* ou *nom_propriété* .</span><span class="sxs-lookup"><span data-stu-id="de391-578">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name* .</span></span> <span data-ttu-id="de391-579">Si aucune correspondance n’est localisée, elle recherche l’un des formats pris en charge sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="de391-579">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="de391-580">Exemple :</span><span class="sxs-lookup"><span data-stu-id="de391-580">For example:</span></span>

* <span data-ttu-id="de391-581">Supposons que le paramètre cible soit un `Dictionary<int, string>` nommé `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="de391-581">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="de391-582">Les données de chaîne de requête ou de formulaire posté peuvent ressembler à l’un des exemples suivants :</span><span class="sxs-lookup"><span data-stu-id="de391-582">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="de391-583">Pour tous les exemples de formats précédents, la liaison de modèle passe un dictionnaire de deux éléments au paramètre `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="de391-583">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="de391-584">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="de391-584">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="de391-585">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="de391-585">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="de391-586">Comportement de globalisation des données de routage de liaison de modèle et des chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="de391-586">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="de391-587">Fournisseur de valeurs d’itinéraire ASP.NET Core et fournisseur de valeur de chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="de391-587">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="de391-588">Traitez les valeurs comme une culture dite indifférente.</span><span class="sxs-lookup"><span data-stu-id="de391-588">Treat values as invariant culture.</span></span>
* <span data-ttu-id="de391-589">Attendez-vous à ce que les URL soient invariantes de culture.</span><span class="sxs-lookup"><span data-stu-id="de391-589">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="de391-590">En revanche, les valeurs provenant des données de formulaire subissent une conversion dépendante de la culture.</span><span class="sxs-lookup"><span data-stu-id="de391-590">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="de391-591">Cela est dû au fait que les URL peuvent être partagées entre les paramètres régionaux.</span><span class="sxs-lookup"><span data-stu-id="de391-591">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="de391-592">Pour que le fournisseur de valeurs d’itinéraire ASP.NET Core et le fournisseur de valeurs de chaîne de requête soient soumis à une conversion dépendante de la culture :</span><span class="sxs-lookup"><span data-stu-id="de391-592">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="de391-593">Héritent de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="de391-593">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="de391-594">Copiez le code à partir de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="de391-594">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="de391-595">Remplacer la [valeur de culture](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passée au constructeur de fournisseur de valeur par [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="de391-595">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="de391-596">Remplacez la fabrique de fournisseur de valeur par défaut dans les options MVC par la nouvelle.</span><span class="sxs-lookup"><span data-stu-id="de391-596">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="de391-597">Types de données spéciaux</span><span class="sxs-lookup"><span data-stu-id="de391-597">Special data types</span></span>

<span data-ttu-id="de391-598">Certains types de données spéciaux peuvent être pris en charge par la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-598">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="de391-599">IFormFile et IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="de391-599">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="de391-600">Fichier chargé inclus dans la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="de391-600">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="de391-601">`IEnumerable<IFormFile>` est également pris en charge pour plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="de391-601">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="de391-602">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="de391-602">CancellationToken</span></span>

<span data-ttu-id="de391-603">utilisé pour annuler l’activité dans les contrôleurs asynchrones.</span><span class="sxs-lookup"><span data-stu-id="de391-603">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="de391-604">FormCollection</span><span class="sxs-lookup"><span data-stu-id="de391-604">FormCollection</span></span>

<span data-ttu-id="de391-605">Permet de récupérer toutes les valeurs des données de formulaire posté.</span><span class="sxs-lookup"><span data-stu-id="de391-605">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="de391-606">Formateurs d’entrée</span><span class="sxs-lookup"><span data-stu-id="de391-606">Input formatters</span></span>

<span data-ttu-id="de391-607">Les données contenues dans le corps de la requête peuvent être au format JSON, XML ou tout autre format.</span><span class="sxs-lookup"><span data-stu-id="de391-607">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="de391-608">Pour analyser ces données, la liaison de modèle utilise un *formateur d’entrée* configuré pour prendre en charge un type de contenu particulier.</span><span class="sxs-lookup"><span data-stu-id="de391-608">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="de391-609">Par défaut, ASP.NET Core inclut des formateurs d’entrée basés sur le format JSON pour prendre en charge les données JSON.</span><span class="sxs-lookup"><span data-stu-id="de391-609">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="de391-610">Vous pouvez ajouter d’autres formateurs pour d’autres types de contenu.</span><span class="sxs-lookup"><span data-stu-id="de391-610">You can add other formatters for other content types.</span></span>

<span data-ttu-id="de391-611">ASP.NET Core sélectionne les formateurs d’entrée en fonction de l’attribut [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="de391-611">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="de391-612">Si aucun attribut n’est présent, il utilise l’[en-tête Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="de391-612">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="de391-613">Pour utiliser les formateurs d’entrée XML intégrés :</span><span class="sxs-lookup"><span data-stu-id="de391-613">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="de391-614">Installez le package NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="de391-614">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="de391-615">Dans `Startup.ConfigureServices`, appelez <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="de391-615">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="de391-616">Appliquez l’attribut `Consumes` aux classes de contrôleur ou aux méthodes d’action devant contenir des données XML dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="de391-616">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="de391-617">Pour plus d’informations, consultez [Introduction à la sérialisation XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="de391-617">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="de391-618">Exclure les types spécifiés de la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="de391-618">Exclude specified types from model binding</span></span>

<span data-ttu-id="de391-619">Le comportement de la liaison de modèle et du système de validation est régi par [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="de391-619">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="de391-620">Vous pouvez personnaliser `ModelMetadata` en ajoutant un fournisseur de détails à [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="de391-620">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="de391-621">Des fournisseurs de détails intégrés sont disponibles pour désactiver la liaison de modèle ou la validation des types spécifiés.</span><span class="sxs-lookup"><span data-stu-id="de391-621">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="de391-622">Pour désactiver la liaison de modèle sur tous les modèles d’un type spécifique, ajoutez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="de391-622">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="de391-623">Par exemple, pour désactiver la liaison de modèle sur tous les modèles de type `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="de391-623">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="de391-624">Pour désactiver la validation des propriétés d’un type spécifique, ajoutez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="de391-624">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="de391-625">Par exemple, pour désactiver la validation sur les propriétés de type `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="de391-625">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="de391-626">Lieurs de modèles personnalisés</span><span class="sxs-lookup"><span data-stu-id="de391-626">Custom model binders</span></span>

<span data-ttu-id="de391-627">Vous pouvez étendre la liaison de modèle en écrivant un lieur de modèle personnalisé et en utilisant l’attribut `[ModelBinder]` afin de le sélectionner pour une cible donnée.</span><span class="sxs-lookup"><span data-stu-id="de391-627">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="de391-628">Découvrez plus d’informations sur la [liaison de modèle personnalisée](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="de391-628">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="de391-629">Liaison de modèle manuelle</span><span class="sxs-lookup"><span data-stu-id="de391-629">Manual model binding</span></span>

<span data-ttu-id="de391-630">Vous pouvez appeler la liaison de modèle manuellement à l’aide de la méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="de391-630">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="de391-631">La méthode est définie sur les classes `ControllerBase` et `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="de391-631">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="de391-632">Les surcharges de méthode vous permettent de spécifier le préfixe et le fournisseur de valeurs à utiliser.</span><span class="sxs-lookup"><span data-stu-id="de391-632">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="de391-633">La méthode retourne `false` en cas d’échec de la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="de391-633">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="de391-634">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="de391-634">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="de391-635">Attribut [FromServices]</span><span class="sxs-lookup"><span data-stu-id="de391-635">[FromServices] attribute</span></span>

<span data-ttu-id="de391-636">Le nom de cet attribut suit le modèle des attributs de liaison de modèle qui spécifient une source de données.</span><span class="sxs-lookup"><span data-stu-id="de391-636">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="de391-637">Toutefois, il ne permet pas de lier les données d’un fournisseur de valeurs.</span><span class="sxs-lookup"><span data-stu-id="de391-637">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="de391-638">Il obtient une instance d’un type à partir du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="de391-638">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="de391-639">Son objectif est de fournir une alternative à l’injection de constructeurs quand vous avez besoin d’un service uniquement si une méthode particulière est appelée.</span><span class="sxs-lookup"><span data-stu-id="de391-639">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="de391-640">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="de391-640">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
