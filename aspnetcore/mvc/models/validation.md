---
title: Validation de modèle dans ASP.NET Core MVC
author: rick-anderson
description: 'En savoir plus sur la validation de modèle dans ASP.NET Core MVC et les :::no-loc(Razor)::: pages.'
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
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
uid: mvc/models/validation
ms.openlocfilehash: 77d49710b9d69f6fbbe92970f1c455de32489444
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056957"
---
# <a name="model-validation-in-aspnet-core-mvc-and-no-locrazor-pages"></a><span data-ttu-id="87993-103">Validation de modèle dans ASP.NET Core MVC et les :::no-loc(Razor)::: pages</span><span class="sxs-lookup"><span data-stu-id="87993-103">Model validation in ASP.NET Core MVC and :::no-loc(Razor)::: Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="87993-104">Par [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="87993-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="87993-105">Cet article explique comment valider une entrée d’utilisateur dans une application ASP.NET Core MVC ou :::no-loc(Razor)::: pages.</span><span class="sxs-lookup"><span data-stu-id="87993-105">This article explains how to validate user input in an ASP.NET Core MVC or :::no-loc(Razor)::: Pages app.</span></span>

<span data-ttu-id="87993-106">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="87993-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="87993-107">État du modèle</span><span class="sxs-lookup"><span data-stu-id="87993-107">Model state</span></span>

<span data-ttu-id="87993-108">L’état du modèle représente les erreurs qui proviennent de deux sous-systèmes : liaison de modèle et validation de modèle.</span><span class="sxs-lookup"><span data-stu-id="87993-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="87993-109">Les erreurs qui proviennent de la [liaison de modèle](model-binding.md) sont généralement des erreurs de conversion de données.</span><span class="sxs-lookup"><span data-stu-id="87993-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="87993-110">Par exemple, un « x » est entré dans un champ de type entier.</span><span class="sxs-lookup"><span data-stu-id="87993-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="87993-111">La validation de modèle se produit après la liaison de modèle et signale des erreurs où les données ne sont pas conformes aux règles d’entreprise.</span><span class="sxs-lookup"><span data-stu-id="87993-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="87993-112">Par exemple, un 0 est entré dans un champ qui attend une évaluation comprise entre 1 et 5.</span><span class="sxs-lookup"><span data-stu-id="87993-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="87993-113">La liaison de modèle et la validation de modèle se produisent avant l’exécution d’une action de contrôleur ou d’une :::no-loc(Razor)::: méthode de gestionnaire de pages.</span><span class="sxs-lookup"><span data-stu-id="87993-113">Both model binding and model validation occur before the execution of a controller action or a :::no-loc(Razor)::: Pages handler method.</span></span> <span data-ttu-id="87993-114">Pour les applications web, il incombe à l’application d’inspecter `ModelState.IsValid` et de réagir de façon appropriée.</span><span class="sxs-lookup"><span data-stu-id="87993-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="87993-115">En règle générale, les applications web réaffichent la page avec un message d’erreur :</span><span class="sxs-lookup"><span data-stu-id="87993-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="87993-116">Les contrôleurs d’API web ne sont pas obligés de vérifier `ModelState.IsValid` s’ils ont l’attribut `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="87993-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="87993-117">Dans ce cas, une réponse HTTP 400 automatique contenant les détails de l’erreur est retournée lorsque l’état du modèle n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="87993-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="87993-118">Pour plus d’informations, consultez [Réponses HTTP 400 automatiques](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="87993-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="87993-119">Réexécuter la validation</span><span class="sxs-lookup"><span data-stu-id="87993-119">Rerun validation</span></span>

<span data-ttu-id="87993-120">La validation est automatique, mais vous souhaiterez peut-être la répéter manuellement.</span><span class="sxs-lookup"><span data-stu-id="87993-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="87993-121">Par exemple, vous pourriez calculer une valeur pour une propriété, et souhaiter réexécuter la validation après avoir affecté la valeur calculée comme valeur de la propriété.</span><span class="sxs-lookup"><span data-stu-id="87993-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="87993-122">Pour réexécuter la validation, appelez la méthode `TryValidateModel`, comme indiqué ici :</span><span class="sxs-lookup"><span data-stu-id="87993-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="87993-123">Attributs de validation</span><span class="sxs-lookup"><span data-stu-id="87993-123">Validation attributes</span></span>

<span data-ttu-id="87993-124">Les attributs de validation vous permettent de spécifier des règles de validation pour des propriétés de modèle.</span><span class="sxs-lookup"><span data-stu-id="87993-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="87993-125">L’exemple suivant tiré de l’exemple d’application montre une classe de modèle qui est annotée avec des attributs de validation.</span><span class="sxs-lookup"><span data-stu-id="87993-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="87993-126">L’attribut `[ClassicMovie]` est un attribut de validation personnalisé, et les autres sont prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="87993-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="87993-127">Non affiché est `[ClassicMovieWithClientValidator]` .</span><span class="sxs-lookup"><span data-stu-id="87993-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="87993-128">`[ClassicMovieWithClientValidator]` montre une autre façon d’implémenter un attribut personnalisé.</span><span class="sxs-lookup"><span data-stu-id="87993-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="87993-129">Attributs prédéfinis</span><span class="sxs-lookup"><span data-stu-id="87993-129">Built-in attributes</span></span>

<span data-ttu-id="87993-130">Voici certains des attributs de validation prédéfinis :</span><span class="sxs-lookup"><span data-stu-id="87993-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="87993-131">`[CreditCard]`: Vérifie que la propriété a un format de carte de crédit.</span><span class="sxs-lookup"><span data-stu-id="87993-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span> <span data-ttu-id="87993-132">Requiert [des méthodes supplémentaires de validation jQuery](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span><span class="sxs-lookup"><span data-stu-id="87993-132">Requires [jQuery Validation Additional Methods](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span></span>
* <span data-ttu-id="87993-133">`[Compare]`: Valide que deux propriétés d’un modèle correspondent.</span><span class="sxs-lookup"><span data-stu-id="87993-133">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="87993-134">`[EmailAddress]`: Vérifie que la propriété a un format d’e-mail.</span><span class="sxs-lookup"><span data-stu-id="87993-134">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="87993-135">`[Phone]`: Vérifie que la propriété a un format de numéro de téléphone.</span><span class="sxs-lookup"><span data-stu-id="87993-135">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="87993-136">`[Range]`: Vérifie que la valeur de la propriété est comprise dans une plage spécifiée.</span><span class="sxs-lookup"><span data-stu-id="87993-136">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="87993-137">`[RegularExpression]`: Valide le fait que la valeur de propriété corresponde à une expression régulière spécifiée.</span><span class="sxs-lookup"><span data-stu-id="87993-137">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="87993-138">`[Required]`: Vérifie que le champ n’a pas la valeur null.</span><span class="sxs-lookup"><span data-stu-id="87993-138">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="87993-139">Pour plus d’informations sur le comportement de cet attribut, consultez [ `[Required]` attribut](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="87993-139">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="87993-140">`[StringLength]`: Valide le fait qu’une valeur de propriété de type chaîne ne dépasse pas une limite de longueur spécifiée.</span><span class="sxs-lookup"><span data-stu-id="87993-140">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="87993-141">`[Url]`: Vérifie que la propriété a un format d’URL.</span><span class="sxs-lookup"><span data-stu-id="87993-141">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="87993-142">`[Remote]`: Valide l’entrée sur le client en appelant une méthode d’action sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="87993-142">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="87993-143">Pour plus d’informations sur le comportement de cet attribut, consultez [ `[Remote]` attribut](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="87993-143">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="87993-144">Vous trouverez la liste complète des attributs de validation dans l’espace de noms [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="87993-144">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="87993-145">Messages d’erreur</span><span class="sxs-lookup"><span data-stu-id="87993-145">Error messages</span></span>

<span data-ttu-id="87993-146">Les attributs de validation vous permettent de spécifier le message d’erreur à afficher pour l’entrée non valide.</span><span class="sxs-lookup"><span data-stu-id="87993-146">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="87993-147">Exemple :</span><span class="sxs-lookup"><span data-stu-id="87993-147">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="87993-148">En interne, les attributs appellent `String.Format` avec un espace réservé pour le nom de champ et parfois d’autres espaces réservés.</span><span class="sxs-lookup"><span data-stu-id="87993-148">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="87993-149">Exemple :</span><span class="sxs-lookup"><span data-stu-id="87993-149">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="87993-150">Appliqué à une propriété `Name`, le message d’erreur créé par le code précédent serait « Name length must be between 6 and 8 ».</span><span class="sxs-lookup"><span data-stu-id="87993-150">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="87993-151">Pour savoir quels paramètres sont passés à `String.Format` pour le message d’erreur d’un attribut particulier, consultez le [code source de DataAnnotations](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="87993-151">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="87993-152">Attribut [Required]</span><span class="sxs-lookup"><span data-stu-id="87993-152">[Required] attribute</span></span>

<span data-ttu-id="87993-153">Le système de validation dans .NET Core 3,0 et versions ultérieures traite les paramètres non Nullable ou les propriétés liées comme s’ils avaient un `[Required]` attribut.</span><span class="sxs-lookup"><span data-stu-id="87993-153">The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="87993-154">Les [types valeur](/dotnet/csharp/language-reference/keywords/value-types) comme `decimal` et `int` n’acceptent pas les valeurs Null.</span><span class="sxs-lookup"><span data-stu-id="87993-154">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span> <span data-ttu-id="87993-155">Vous pouvez désactiver ce comportement en configurant <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="87993-155">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a><span data-ttu-id="87993-156">Validation de [Required] sur le serveur</span><span class="sxs-lookup"><span data-stu-id="87993-156">[Required] validation on the server</span></span>

<span data-ttu-id="87993-157">Sur le serveur, une valeur obligatoire est considérée comme manquante si la propriété est Null.</span><span class="sxs-lookup"><span data-stu-id="87993-157">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="87993-158">Un champ qui n’accepte pas les valeurs NULL est toujours valide et le `[Required]` message d’erreur de l’attribut n’est jamais affiché.</span><span class="sxs-lookup"><span data-stu-id="87993-158">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="87993-159">Toutefois, la liaison de modèle pour une propriété n’acceptant pas les valeurs Null peut échouer, entraînant l’affichage d’un message d’erreur tel que `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="87993-159">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="87993-160">Pour spécifier un message d’erreur personnalisé pour la validation côté serveur des types n’acceptant pas les valeurs Null, vous disposez des options suivantes :</span><span class="sxs-lookup"><span data-stu-id="87993-160">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="87993-161">Rendre le champ Nullable (par exemple, `decimal?` au lieu de `decimal`).</span><span class="sxs-lookup"><span data-stu-id="87993-161">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="87993-162">[Valeur \<T> null](/dotnet/csharp/programming-guide/nullable-types/) les types valeur sont traités comme des types Nullable standard.</span><span class="sxs-lookup"><span data-stu-id="87993-162">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="87993-163">Spécifier le message d’erreur par défaut devant être utilisé par la liaison de modèle, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="87993-163">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="87993-164">Pour plus d’informations sur les erreurs de liaison de modèle pour lesquelles vous pouvez définir des messages par défaut, consultez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="87993-164">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="87993-165">Validation de [Required] sur le client</span><span class="sxs-lookup"><span data-stu-id="87993-165">[Required] validation on the client</span></span>

<span data-ttu-id="87993-166">Les chaînes et les types n’acceptant pas les valeurs Null sont gérés différemment sur le client et sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="87993-166">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="87993-167">Sur le client :</span><span class="sxs-lookup"><span data-stu-id="87993-167">On the client:</span></span>

* <span data-ttu-id="87993-168">Une valeur est considérée comme présente uniquement si une entrée est tapée pour celle-ci.</span><span class="sxs-lookup"><span data-stu-id="87993-168">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="87993-169">Par conséquent, la validation côté client gère les types n’acceptant pas les valeurs Null de la même façon que les types Nullable</span><span class="sxs-lookup"><span data-stu-id="87993-169">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="87993-170">Un espace blanc dans un champ de chaîne est considéré comme une entrée valide par la méthode [required](https://jqueryvalidation.org/required-method/) jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="87993-170">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="87993-171">La validation côté serveur considère qu’un champ de chaîne obligatoire est non valide si seul un espace blanc est entré.</span><span class="sxs-lookup"><span data-stu-id="87993-171">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="87993-172">Comme indiqué précédemment, les types n’acceptant pas les valeurs Null sont traités comme s’ils avaient un attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="87993-172">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="87993-173">Cela signifie que vous bénéficiez d’une validation côté client même si vous n’appliquez pas l’attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="87993-173">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="87993-174">En revanche, si vous n’utilisez pas l’attribut, vous recevez un message d’erreur par défaut.</span><span class="sxs-lookup"><span data-stu-id="87993-174">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="87993-175">Pour spécifier un message d’erreur personnalisé, utilisez l’attribut.</span><span class="sxs-lookup"><span data-stu-id="87993-175">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="87993-176">Attribut [Remote]</span><span class="sxs-lookup"><span data-stu-id="87993-176">[Remote] attribute</span></span>

<span data-ttu-id="87993-177">L’attribut `[Remote]` implémente la validation côté client qui nécessite l’appel d’une méthode sur le serveur pour déterminer si le champ d’entrée est valide.</span><span class="sxs-lookup"><span data-stu-id="87993-177">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="87993-178">Par exemple, l’application peut avoir besoin de vérifier si un nom d’utilisateur est déjà en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="87993-178">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="87993-179">Pour implémenter la validation à distance</span><span class="sxs-lookup"><span data-stu-id="87993-179">To implement remote validation:</span></span>

1. <span data-ttu-id="87993-180">Créez une méthode d’action devant être appelée par JavaScript.</span><span class="sxs-lookup"><span data-stu-id="87993-180">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="87993-181">La méthode [distante](https://jqueryvalidation.org/remote-method/) de validation jQuery attend une réponse JSON :</span><span class="sxs-lookup"><span data-stu-id="87993-181">The jQuery Validation [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="87993-182">`true` signifie que les données d’entrée sont valides.</span><span class="sxs-lookup"><span data-stu-id="87993-182">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="87993-183">`false`, `undefined` ou `null` signifie que l’entrée n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="87993-183">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="87993-184">Affichez le message d’erreur par défaut.</span><span class="sxs-lookup"><span data-stu-id="87993-184">Display the default error message.</span></span>
   * <span data-ttu-id="87993-185">Toute autre chaîne signifie que l’entrée n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="87993-185">Any other string means the input is invalid.</span></span> <span data-ttu-id="87993-186">Affichez la chaîne en tant que message d’erreur personnalisé.</span><span class="sxs-lookup"><span data-stu-id="87993-186">Display the string as a custom error message.</span></span>

   <span data-ttu-id="87993-187">Voici un exemple de méthode d’action qui retourne un message d’erreur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="87993-187">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="87993-188">Dans la classe de modèle, annotez la propriété avec un attribut `[Remote]` qui pointe vers la méthode d’action de validation, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="87993-188">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="87993-189">L’attribut `[Remote]` se trouve dans l’espace de noms `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="87993-189">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="87993-190">Champs supplémentaires</span><span class="sxs-lookup"><span data-stu-id="87993-190">Additional fields</span></span>

<span data-ttu-id="87993-191">La propriété `AdditionalFields` de l’attribut `[Remote]` vous permet de valider des combinaisons de champs relativement à des données présentes sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="87993-191">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="87993-192">Par exemple, si le modèle `User` a des propriétés `FirstName` et `LastName`, vous pouvez vérifier qu’aucun utilisateur existant n’a déjà cette paire de noms.</span><span class="sxs-lookup"><span data-stu-id="87993-192">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="87993-193">L'exemple suivant montre comment utiliser `AdditionalFields` :</span><span class="sxs-lookup"><span data-stu-id="87993-193">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="87993-194">`AdditionalFields` peut être défini explicitement avec les chaînes « FirstName » et « LastName », mais l’utilisation de l’opérateur [nameof](/dotnet/csharp/language-reference/keywords/nameof) simplifie la refactorisation ultérieure.</span><span class="sxs-lookup"><span data-stu-id="87993-194">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="87993-195">La méthode d’action pour cette validation doit accepter `firstName` les `lastName` arguments et :</span><span class="sxs-lookup"><span data-stu-id="87993-195">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="87993-196">Quand l’utilisateur entre un nom ou un prénom, JavaScript effectue un appel à distance pour vérifier si cette paire de noms est déjà utilisée.</span><span class="sxs-lookup"><span data-stu-id="87993-196">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="87993-197">Pour valider deux champs supplémentaires ou plus, spécifiez-les sous la forme d’une liste délimitée par des virgules.</span><span class="sxs-lookup"><span data-stu-id="87993-197">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="87993-198">Par exemple, pour ajouter une propriété `MiddleName` au modèle, définissez l’attribut `[Remote]` comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="87993-198">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="87993-199">`AdditionalFields`, comme tous les arguments d’attribut, doit être une expression constante.</span><span class="sxs-lookup"><span data-stu-id="87993-199">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="87993-200">Vous ne devez donc pas utiliser une [chaîne interpolée](/dotnet/csharp/language-reference/keywords/interpolated-strings) ou appeler <xref:System.String.Join*> pour initialiser `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="87993-200">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="87993-201">Alternatives aux attributs prédéfinis</span><span class="sxs-lookup"><span data-stu-id="87993-201">Alternatives to built-in attributes</span></span>

<span data-ttu-id="87993-202">Si vous avez besoin d’une validation non fournie par les attributs prédéfinis, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="87993-202">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="87993-203">[Créer des attributs personnalisés](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="87993-203">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="87993-204">[Implémenter IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="87993-204">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="87993-205">Attributs personnalisés</span><span class="sxs-lookup"><span data-stu-id="87993-205">Custom attributes</span></span>

<span data-ttu-id="87993-206">Pour les scénarios non gérés par les attributs de validation prédéfinis, vous pouvez créer des attributs de validation personnalisés.</span><span class="sxs-lookup"><span data-stu-id="87993-206">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="87993-207">Créez une classe qui hérite de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> et substituez la méthode <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="87993-207">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="87993-208">La méthode `IsValid` accepte un objet nommé *value* , qui est l’entrée à valider.</span><span class="sxs-lookup"><span data-stu-id="87993-208">The `IsValid` method accepts an object named *value* , which is the input to be validated.</span></span> <span data-ttu-id="87993-209">Une surcharge accepte également un objet `ValidationContext`, qui fournit des informations supplémentaires telles que l’instance de modèle créée par la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="87993-209">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="87993-210">L’exemple suivant vérifie que la date de sortie d’un film appartenant au genre *Classic* n’est pas ultérieure à une année spécifiée.</span><span class="sxs-lookup"><span data-stu-id="87993-210">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="87993-211">L' `[ClassicMovie]` attribut :</span><span class="sxs-lookup"><span data-stu-id="87993-211">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="87993-212">S’exécute uniquement sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="87993-212">Is only run on the server.</span></span>
* <span data-ttu-id="87993-213">Pour les films classiques, valide la date de publication :</span><span class="sxs-lookup"><span data-stu-id="87993-213">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="87993-214">La variable `movie` de l’exemple précédent représente un objet `Movie` qui contient les données de l’envoi du formulaire.</span><span class="sxs-lookup"><span data-stu-id="87993-214">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="87993-215">Quand la validation échoue, un `ValidationResult` avec un message d’erreur est retourné.</span><span class="sxs-lookup"><span data-stu-id="87993-215">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="87993-216">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="87993-216">IValidatableObject</span></span>

<span data-ttu-id="87993-217">L’exemple précédent fonctionne uniquement avec les types `Movie`.</span><span class="sxs-lookup"><span data-stu-id="87993-217">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="87993-218">Une autre option de validation au niveau de la classe consiste à implémenter `IValidatableObject` dans la classe de modèle, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="87993-218">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="87993-219">Validation du nœud de niveau supérieur</span><span class="sxs-lookup"><span data-stu-id="87993-219">Top-level node validation</span></span>

<span data-ttu-id="87993-220">Les nœuds de niveau supérieur incluent les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="87993-220">Top-level nodes include:</span></span>

* <span data-ttu-id="87993-221">Paramètres d’action</span><span class="sxs-lookup"><span data-stu-id="87993-221">Action parameters</span></span>
* <span data-ttu-id="87993-222">Propriétés du contrôleur</span><span class="sxs-lookup"><span data-stu-id="87993-222">Controller properties</span></span>
* <span data-ttu-id="87993-223">Paramètres du gestionnaire de page</span><span class="sxs-lookup"><span data-stu-id="87993-223">Page handler parameters</span></span>
* <span data-ttu-id="87993-224">Propriétés du modèle de page</span><span class="sxs-lookup"><span data-stu-id="87993-224">Page model properties</span></span>

<span data-ttu-id="87993-225">Les nœuds de niveau supérieur liés au modèle sont validés en plus de la validation des propriétés du modèle.</span><span class="sxs-lookup"><span data-stu-id="87993-225">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="87993-226">Dans l’exemple suivant tiré de l’exemple d’application, la méthode `VerifyPhone` utilise <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> pour valider le paramètre d’action `phone` :</span><span class="sxs-lookup"><span data-stu-id="87993-226">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="87993-227">Les nœuds de niveau supérieur peuvent utiliser <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> avec des attributs de validation.</span><span class="sxs-lookup"><span data-stu-id="87993-227">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="87993-228">Dans l’exemple suivant de l’exemple d’application, la méthode `CheckAge` spécifie que le paramètre `age` doit être lié à partir de la chaîne de requête au moment de l’envoi du formulaire :</span><span class="sxs-lookup"><span data-stu-id="87993-228">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="87993-229">Dans la page de vérification de l’âge ( *CheckAge.cshtml* ), il existe deux formulaires.</span><span class="sxs-lookup"><span data-stu-id="87993-229">In the Check Age page ( *CheckAge.cshtml* ), there are two forms.</span></span> <span data-ttu-id="87993-230">Le premier formulaire envoie une `Age` valeur sous la `99` forme d’un paramètre de chaîne de requête : `https://localhost:5001/Users/CheckAge?Age=99` .</span><span class="sxs-lookup"><span data-stu-id="87993-230">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="87993-231">Quand un paramètre `age` au format approprié est envoyé à partir de la chaîne de requête, le formulaire est validé.</span><span class="sxs-lookup"><span data-stu-id="87993-231">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="87993-232">Le second formulaire de la page de vérification de l’âge envoie la valeur `Age` dans le corps de la requête, ce qui entraîne un échec de la validation.</span><span class="sxs-lookup"><span data-stu-id="87993-232">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="87993-233">L’échec de la liaison est dû au fait que le paramètre `age` doit provenir d’une chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="87993-233">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="87993-234">Quantité maximale d’erreurs</span><span class="sxs-lookup"><span data-stu-id="87993-234">Maximum errors</span></span>

<span data-ttu-id="87993-235">La validation s’arrête quand le nombre maximal d’erreurs est atteint (200 par défaut).</span><span class="sxs-lookup"><span data-stu-id="87993-235">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="87993-236">Vous pouvez configurer ce nombre avec le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="87993-236">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="87993-237">Récursivité maximale</span><span class="sxs-lookup"><span data-stu-id="87993-237">Maximum recursion</span></span>

<span data-ttu-id="87993-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> parcourt le graphe d’objet du modèle en cours de validation.</span><span class="sxs-lookup"><span data-stu-id="87993-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="87993-239">Pour les modèles en profondeur ou récursifs à l’infini, la validation peut entraîner un dépassement de capacité de la pile.</span><span class="sxs-lookup"><span data-stu-id="87993-239">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="87993-240">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) fournit un moyen d’interrompre la validation de manière anticipée si la récursivité du visiteur dépasse une profondeur configurée.</span><span class="sxs-lookup"><span data-stu-id="87993-240">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="87993-241">La valeur par défaut de `MvcOptions.MaxValidationDepth` est 32.</span><span class="sxs-lookup"><span data-stu-id="87993-241">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="87993-242">Court-circuit automatique</span><span class="sxs-lookup"><span data-stu-id="87993-242">Automatic short-circuit</span></span>

<span data-ttu-id="87993-243">La validation est automatiquement court-circuitée (ignorée) si le graphe du modèle ne nécessite pas de validation.</span><span class="sxs-lookup"><span data-stu-id="87993-243">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="87993-244">Les objets pour lesquels le runtime ignore la validation comprennent les collections de primitives (telles que `byte[]`, `string[]`, `Dictionary<string, string>`) et les graphes d’objets complexes qui n’ont pas de validateur.</span><span class="sxs-lookup"><span data-stu-id="87993-244">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="87993-245">Désactiver la validation</span><span class="sxs-lookup"><span data-stu-id="87993-245">Disable validation</span></span>

<span data-ttu-id="87993-246">Pour désactiver la validation</span><span class="sxs-lookup"><span data-stu-id="87993-246">To disable validation:</span></span>

1. <span data-ttu-id="87993-247">Créez une implémentation de `IObjectModelValidator` qui ne marque aucun champ comme étant non valide.</span><span class="sxs-lookup"><span data-stu-id="87993-247">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="87993-248">Ajoutez le code suivant à `Startup.ConfigureServices` pour remplacer l’implémentation par défaut de `IObjectModelValidator` dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="87993-248">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="87993-249">Vous risquez toujours de voir des erreurs d’état du modèle provenant de la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="87993-249">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="87993-250">Validation côté client</span><span class="sxs-lookup"><span data-stu-id="87993-250">Client-side validation</span></span>

<span data-ttu-id="87993-251">La validation côté client empêche l’envoi jusqu’à ce que le formulaire soit valide.</span><span class="sxs-lookup"><span data-stu-id="87993-251">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="87993-252">Le bouton Submit exécute le code JavaScript qui envoie le formulaire ou qui affiche des messages d’erreur.</span><span class="sxs-lookup"><span data-stu-id="87993-252">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="87993-253">La validation côté client permet d’éviter un aller-retour inutile vers le serveur quand il existe des erreurs d’entrée sur un formulaire.</span><span class="sxs-lookup"><span data-stu-id="87993-253">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="87993-254">Les références de script suivantes dans *_Layout.cshtml* et *_ValidationScriptsPartial.cshtml* prennent en charge la validation côté client :</span><span class="sxs-lookup"><span data-stu-id="87993-254">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="87993-255">Le script de [validation jQuery discrète](https://github.com/aspnet/jquery-validation-unobtrusive) est une bibliothèque Microsoft frontale personnalisée qui s’appuie sur le plug-in de [validation jQuery](https://jqueryvalidation.org/) populaire.</span><span class="sxs-lookup"><span data-stu-id="87993-255">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validation](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="87993-256">Sans jQuery Unobtrusive Validation, vous devriez coder la même logique de validation à deux endroits : une fois dans les attributs de validation côté serveur sur les propriétés du modèle, puis à nouveau dans les scripts côté client.</span><span class="sxs-lookup"><span data-stu-id="87993-256">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="87993-257">Au lieu de cela, les [Tag Helpers](xref:mvc/views/tag-helpers/intro) et les [helpers HTML](xref:mvc/views/overview) utilisent les attributs de validation et les métadonnées de type des propriétés du modèle afin de restituer les attributs `data-` HTML 5 pour les éléments de formulaire nécessitant une validation.</span><span class="sxs-lookup"><span data-stu-id="87993-257">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="87993-258">la validation jQuery discrète analyse les `data-` attributs et passe la logique à la validation jQuery, en « copiant » en fait la logique de validation côté serveur vers le client.</span><span class="sxs-lookup"><span data-stu-id="87993-258">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validation, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="87993-259">Vous pouvez afficher les erreurs de validation sur le client en utilisant des Tag Helpers, comme indiqué ici :</span><span class="sxs-lookup"><span data-stu-id="87993-259">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="87993-260">Les balises d’assistance précédentes affichent le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="87993-260">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="87993-261">Notez que les attributs `data-` dans la sortie HTML correspondent aux attributs de validation pour la propriété `Movie.ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="87993-261">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="87993-262">L’attribut `data-val-required` contient un message d’erreur à afficher si l’utilisateur ne renseigne pas le champ correspondant à la date de sortie.</span><span class="sxs-lookup"><span data-stu-id="87993-262">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="87993-263">la validation jQuery discrète passe cette valeur à la méthode jQuery validation [Required ()](https://jqueryvalidation.org/required-method/) , qui affiche ensuite ce message dans l' **\<span>** élément associé.</span><span class="sxs-lookup"><span data-stu-id="87993-263">jQuery Unobtrusive Validation passes this value to the jQuery Validation [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="87993-264">La validation du type de données est basée sur le type .NET d’une propriété, sauf en cas de substitution par un attribut `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="87993-264">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="87993-265">Les navigateurs ont leurs propres messages d’erreur par défaut, mais le package jQuery Validation Unobtrusive Validation peut remplacer ces messages.</span><span class="sxs-lookup"><span data-stu-id="87993-265">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="87993-266">Les attributs `[DataType]` et les sous-classes comme `[EmailAddress]` vous permettent de spécifier le message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="87993-266">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="87993-267">Validation discrète</span><span class="sxs-lookup"><span data-stu-id="87993-267">Unobtrusive validation</span></span>

<span data-ttu-id="87993-268">Pour plus d’informations sur la validation discrète, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span><span class="sxs-lookup"><span data-stu-id="87993-268">For information on unobtrusive validation, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="87993-269">Ajouter une validation à des formulaires dynamiques</span><span class="sxs-lookup"><span data-stu-id="87993-269">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="87993-270">la validation jQuery discrète passe la logique de validation et les paramètres à la validation jQuery lors du premier chargement de la page.</span><span class="sxs-lookup"><span data-stu-id="87993-270">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validation when the page first loads.</span></span> <span data-ttu-id="87993-271">Par conséquent, la validation ne fonctionne pas automatiquement sur les formulaires générés de manière dynamique.</span><span class="sxs-lookup"><span data-stu-id="87993-271">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="87993-272">Pour activer la validation, vous devez faire en sorte que jQuery Validate analyse le formulaire dynamique immédiatement après l’avoir créé.</span><span class="sxs-lookup"><span data-stu-id="87993-272">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="87993-273">Par exemple, le code suivant définit la validation côté client sur un formulaire ajouté par le biais d’AJAX.</span><span class="sxs-lookup"><span data-stu-id="87993-273">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="87993-274">La méthode `$.validator.unobtrusive.parse()` accepte un sélecteur jQuery comme argument.</span><span class="sxs-lookup"><span data-stu-id="87993-274">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="87993-275">Cette méthode indique à jQuery Unobtrusive Validation d’analyser les attributs `data-` des formulaires dans ce sélecteur.</span><span class="sxs-lookup"><span data-stu-id="87993-275">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="87993-276">Les valeurs de ces attributs sont ensuite transmises au plug-in de validation jQuery.</span><span class="sxs-lookup"><span data-stu-id="87993-276">The values of those attributes are then passed to the jQuery Validation plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="87993-277">Ajouter une validation à des contrôles dynamiques</span><span class="sxs-lookup"><span data-stu-id="87993-277">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="87993-278">La méthode `$.validator.unobtrusive.parse()` opère sur un formulaire entier, et non sur des contrôles individuels générés de manière dynamique tels que `<input>` et `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="87993-278">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="87993-279">Pour réanalyser le formulaire, supprimez les données de validation qui ont été ajoutées quand le formulaire a été analysé précédemment, comme illustré dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="87993-279">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="87993-280">Validation personnalisée côté client</span><span class="sxs-lookup"><span data-stu-id="87993-280">Custom client-side validation</span></span>

<span data-ttu-id="87993-281">La validation côté client personnalisée s’effectue en générant `data-` des attributs HTML qui fonctionnent avec un adaptateur de validation jQuery personnalisé.</span><span class="sxs-lookup"><span data-stu-id="87993-281">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validation adapter.</span></span> <span data-ttu-id="87993-282">L’exemple de code d’adaptateur suivant a été écrit pour les attributs `[ClassicMovie]` et `[ClassicMovieWithClientValidator]` qui ont été introduits plus haut dans cet article :</span><span class="sxs-lookup"><span data-stu-id="87993-282">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="87993-283">Pour plus d’informations sur l’écriture des adaptateurs, consultez la documentation sur la [validation jQuery](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="87993-283">For information about how to write adapters, see the [jQuery Validation documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="87993-284">L’utilisation d’un adaptateur pour un champ donné est déclenchée par des attributs `data-` qui :</span><span class="sxs-lookup"><span data-stu-id="87993-284">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="87993-285">Marquent le champ comme étant soumis à validation (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="87993-285">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="87993-286">Identifient un nom de règle de validation et un texte de message d’erreur (par exemple, `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="87993-286">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="87993-287">Fournissent les éventuels paramètres supplémentaires dont le validateur a besoin (par exemple, `data-val-rulename-param1="value"`).</span><span class="sxs-lookup"><span data-stu-id="87993-287">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="87993-288">L’exemple suivant montre les attributs `data-` pour l’attribut `ClassicMovie` de l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="87993-288">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="87993-289">Comme mentionné plus haut, les [Tag Helpers](xref:mvc/views/tag-helpers/intro) et les [helpers HTML](xref:mvc/views/overview) utilisent les informations des attributs de validation pour restituer les attributs `data-`.</span><span class="sxs-lookup"><span data-stu-id="87993-289">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="87993-290">Il existe deux options pour l’écriture de code qui entraîne la création d’attributs HTML `data-` personnalisés :</span><span class="sxs-lookup"><span data-stu-id="87993-290">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="87993-291">Créez une classe qui dérive de `AttributeAdapterBase<TAttribute>` et une classe qui implémente `IValidationAttributeAdapterProvider`, et inscrivez votre attribut et son adaptateur dans l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="87993-291">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="87993-292">Cette méthode respecte le [principe de responsabilité unique](https://wikipedia.org/wiki/Single_responsibility_principle), dans la mesure où le code de validation lié au serveur et celui lié au client se trouvent dans des classes distinctes.</span><span class="sxs-lookup"><span data-stu-id="87993-292">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="87993-293">Il existe un autre avantage : l’adaptateur étant inscrit dans l’injection de dépendances, les autres services dans l’injection de dépendances lui sont accessibles si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="87993-293">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="87993-294">Implémentez `IClientModelValidator` dans votre classe `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="87993-294">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="87993-295">Cette méthode peut être appropriée si l’attribut n’effectue aucune validation côté serveur et n’a besoin d’aucun service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="87993-295">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="87993-296">AttributeAdapter pour la validation côté client</span><span class="sxs-lookup"><span data-stu-id="87993-296">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="87993-297">Cette méthode de rendu des attributs `data-` en HTML est utilisée par l’attribut `ClassicMovie` dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="87993-297">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="87993-298">Pour ajouter la validation côté client à l’aide de cette méthode</span><span class="sxs-lookup"><span data-stu-id="87993-298">To add client validation by using this method:</span></span>

1. <span data-ttu-id="87993-299">Créez une classe d’adaptateurs d’attributs pour l’attribut de validation personnalisé.</span><span class="sxs-lookup"><span data-stu-id="87993-299">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="87993-300">Dérivez la classe de [AttributeAdapterBase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="87993-300">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="87993-301">Créez une méthode `AddValidation` qui ajoute des attributs `data-` à la sortie restituée, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="87993-301">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="87993-302">Créez une classe de fournisseurs d’adaptateurs qui implémente <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="87993-302">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="87993-303">Dans la méthode `GetAttributeAdapter`, passez l’attribut personnalisé au constructeur de l’adaptateur, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="87993-303">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="87993-304">Inscrivez le fournisseur d’adaptateurs auprès de l’injection de dépendances dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="87993-304">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="87993-305">IClientModelValidator pour la validation côté client</span><span class="sxs-lookup"><span data-stu-id="87993-305">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="87993-306">Cette méthode de rendu des attributs `data-` en HTML est utilisée par l’attribut `ClassicMovieWithClientValidator` dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="87993-306">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="87993-307">Pour ajouter la validation côté client à l’aide de cette méthode</span><span class="sxs-lookup"><span data-stu-id="87993-307">To add client validation by using this method:</span></span>

* <span data-ttu-id="87993-308">Dans l’attribut de validation personnalisé, implémentez l’interface `IClientModelValidator` et créez une méthode `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="87993-308">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="87993-309">Dans la méthode `AddValidation`, ajoutez des attributs `data-` pour la validation, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="87993-309">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="87993-310">Désactiver la validation côté client</span><span class="sxs-lookup"><span data-stu-id="87993-310">Disable client-side validation</span></span>

<span data-ttu-id="87993-311">Le code suivant désactive la validation client dans les :::no-loc(Razor)::: pages :</span><span class="sxs-lookup"><span data-stu-id="87993-311">The following code disables client validation in :::no-loc(Razor)::: Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="87993-312">Autres options pour désactiver la validation côté client :</span><span class="sxs-lookup"><span data-stu-id="87993-312">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="87993-313">Commentez la référence à `_ValidationScriptsPartial` dans tous les fichiers *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="87993-313">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="87993-314">Supprimez le contenu du fichier *Pages\Shared \_ ValidationScriptsPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="87993-314">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="87993-315">L’approche précédente n’empêchera pas la validation côté client de la :::no-loc(ASP.NET Core Identity)::: :::no-loc(Razor)::: bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="87993-315">The preceding approach won't prevent client side validation of :::no-loc(ASP.NET Core Identity)::: :::no-loc(Razor)::: Class Library.</span></span> <span data-ttu-id="87993-316">Pour plus d'informations, consultez <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="87993-316">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87993-317">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="87993-317">Additional resources</span></span>

* [<span data-ttu-id="87993-318">Espace de noms System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="87993-318">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="87993-319">Liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="87993-319">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="87993-320">Cet article explique comment valider une entrée d’utilisateur dans une application ASP.NET Core MVC ou :::no-loc(Razor)::: pages.</span><span class="sxs-lookup"><span data-stu-id="87993-320">This article explains how to validate user input in an ASP.NET Core MVC or :::no-loc(Razor)::: Pages app.</span></span>

<span data-ttu-id="87993-321">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="87993-321">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="87993-322">État du modèle</span><span class="sxs-lookup"><span data-stu-id="87993-322">Model state</span></span>

<span data-ttu-id="87993-323">L’état du modèle représente les erreurs qui proviennent de deux sous-systèmes : liaison de modèle et validation de modèle.</span><span class="sxs-lookup"><span data-stu-id="87993-323">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="87993-324">Les erreurs qui proviennent de la [liaison de modèle](model-binding.md) sont généralement des erreurs de conversion de données (par exemple, un « x » est entré dans un champ qui attend un nombre entier).</span><span class="sxs-lookup"><span data-stu-id="87993-324">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="87993-325">La validation de modèle se produit après la liaison de modèle, et signale les erreurs où les données ne sont pas conformes aux règles d’entreprise (par exemple, un 0 est entré dans un champ qui attend une évaluation comprise entre 1 et 5).</span><span class="sxs-lookup"><span data-stu-id="87993-325">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="87993-326">La liaison de modèle et la validation se produisent avant l’exécution d’une action de contrôleur ou d’une :::no-loc(Razor)::: méthode de gestionnaire de pages.</span><span class="sxs-lookup"><span data-stu-id="87993-326">Both model binding and validation occur before the execution of a controller action or a :::no-loc(Razor)::: Pages handler method.</span></span> <span data-ttu-id="87993-327">Pour les applications web, il incombe à l’application d’inspecter `ModelState.IsValid` et de réagir de façon appropriée.</span><span class="sxs-lookup"><span data-stu-id="87993-327">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="87993-328">En règle générale, les applications web réaffichent la page avec un message d’erreur :</span><span class="sxs-lookup"><span data-stu-id="87993-328">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="87993-329">Les contrôleurs d’API web ne sont pas obligés de vérifier `ModelState.IsValid` s’ils ont l’attribut `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="87993-329">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="87993-330">Dans ce cas, une réponse HTTP 400 automatique contenant les détails de l’erreur est retournée lorsque l’état du modèle n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="87993-330">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="87993-331">Pour plus d’informations, consultez [Réponses HTTP 400 automatiques](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="87993-331">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="87993-332">Réexécuter la validation</span><span class="sxs-lookup"><span data-stu-id="87993-332">Rerun validation</span></span>

<span data-ttu-id="87993-333">La validation est automatique, mais vous souhaiterez peut-être la répéter manuellement.</span><span class="sxs-lookup"><span data-stu-id="87993-333">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="87993-334">Par exemple, vous pourriez calculer une valeur pour une propriété, et souhaiter réexécuter la validation après avoir affecté la valeur calculée comme valeur de la propriété.</span><span class="sxs-lookup"><span data-stu-id="87993-334">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="87993-335">Pour réexécuter la validation, appelez la méthode `TryValidateModel`, comme indiqué ici :</span><span class="sxs-lookup"><span data-stu-id="87993-335">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="87993-336">Attributs de validation</span><span class="sxs-lookup"><span data-stu-id="87993-336">Validation attributes</span></span>

<span data-ttu-id="87993-337">Les attributs de validation vous permettent de spécifier des règles de validation pour des propriétés de modèle.</span><span class="sxs-lookup"><span data-stu-id="87993-337">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="87993-338">L’exemple suivant tiré de l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) montre une classe de modèle qui est annotée avec des attributs de validation.</span><span class="sxs-lookup"><span data-stu-id="87993-338">The following example from [the sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="87993-339">L’attribut `[ClassicMovie]` est un attribut de validation personnalisé, et les autres sont prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="87993-339">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="87993-340">Non affiché est `[ClassicMovie2]` , qui illustre une autre façon d’implémenter un attribut personnalisé.</span><span class="sxs-lookup"><span data-stu-id="87993-340">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="87993-341">Attributs prédéfinis</span><span class="sxs-lookup"><span data-stu-id="87993-341">Built-in attributes</span></span>

<span data-ttu-id="87993-342">Les attributs de validation intégrés sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="87993-342">Built-in validation attributes include:</span></span>

* <span data-ttu-id="87993-343">`[CreditCard]`: Vérifie que la propriété a un format de carte de crédit.</span><span class="sxs-lookup"><span data-stu-id="87993-343">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="87993-344">`[Compare]`: Valide que deux propriétés d’un modèle correspondent.</span><span class="sxs-lookup"><span data-stu-id="87993-344">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="87993-345">Par exemple, le fichier *Register.cshtml.cs* utilise `[Compare]` pour valider les deux correspondances de mots de passe entrées.</span><span class="sxs-lookup"><span data-stu-id="87993-345">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="87993-346">[Structure :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity) pour afficher le code d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="87993-346">[Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="87993-347">`[EmailAddress]`: Vérifie que la propriété a un format d’e-mail.</span><span class="sxs-lookup"><span data-stu-id="87993-347">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="87993-348">`[Phone]`: Vérifie que la propriété a un format de numéro de téléphone.</span><span class="sxs-lookup"><span data-stu-id="87993-348">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="87993-349">`[Range]`: Vérifie que la valeur de la propriété est comprise dans une plage spécifiée.</span><span class="sxs-lookup"><span data-stu-id="87993-349">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="87993-350">`[RegularExpression]`: Valide le fait que la valeur de propriété corresponde à une expression régulière spécifiée.</span><span class="sxs-lookup"><span data-stu-id="87993-350">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="87993-351">`[Required]`: Vérifie que le champ n’a pas la valeur null.</span><span class="sxs-lookup"><span data-stu-id="87993-351">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="87993-352">Pour plus d’informations sur le comportement de cet attribut, consultez [ `[Required]` attribut](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="87993-352">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="87993-353">`[StringLength]`: Valide le fait qu’une valeur de propriété de type chaîne ne dépasse pas une limite de longueur spécifiée.</span><span class="sxs-lookup"><span data-stu-id="87993-353">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="87993-354">`[Url]`: Vérifie que la propriété a un format d’URL.</span><span class="sxs-lookup"><span data-stu-id="87993-354">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="87993-355">`[Remote]`: Valide l’entrée sur le client en appelant une méthode d’action sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="87993-355">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="87993-356">Pour plus d’informations sur le comportement de cet attribut, consultez [ `[Remote]` attribut](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="87993-356">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="87993-357">Lors de l’utilisation de l' `[RegularExpression]` attribut avec la validation côté client, l’expression régulière est exécutée dans JavaScript sur le client.</span><span class="sxs-lookup"><span data-stu-id="87993-357">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="87993-358">Cela signifie que le comportement de correspondance [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) sera utilisé.</span><span class="sxs-lookup"><span data-stu-id="87993-358">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="87993-359">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/corefx/issues/42487).</span><span class="sxs-lookup"><span data-stu-id="87993-359">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="87993-360">Vous trouverez la liste complète des attributs de validation dans l’espace de noms [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="87993-360">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="87993-361">Messages d’erreur</span><span class="sxs-lookup"><span data-stu-id="87993-361">Error messages</span></span>

<span data-ttu-id="87993-362">Les attributs de validation vous permettent de spécifier le message d’erreur à afficher pour l’entrée non valide.</span><span class="sxs-lookup"><span data-stu-id="87993-362">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="87993-363">Exemple :</span><span class="sxs-lookup"><span data-stu-id="87993-363">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="87993-364">En interne, les attributs appellent `String.Format` avec un espace réservé pour le nom de champ et parfois d’autres espaces réservés.</span><span class="sxs-lookup"><span data-stu-id="87993-364">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="87993-365">Exemple :</span><span class="sxs-lookup"><span data-stu-id="87993-365">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="87993-366">Appliqué à une propriété `Name`, le message d’erreur créé par le code précédent serait « Name length must be between 6 and 8 ».</span><span class="sxs-lookup"><span data-stu-id="87993-366">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="87993-367">Pour savoir quels paramètres sont passés à `String.Format` pour le message d’erreur d’un attribut particulier, consultez le [code source de DataAnnotations](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="87993-367">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="87993-368">Attribut [Required]</span><span class="sxs-lookup"><span data-stu-id="87993-368">[Required] attribute</span></span>

<span data-ttu-id="87993-369">Par défaut, le système de validation traite les propriétés ou paramètres n’acceptant pas les valeurs Null comme s’ils avaient un attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="87993-369">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="87993-370">Les [types valeur](/dotnet/csharp/language-reference/keywords/value-types) comme `decimal` et `int` n’acceptent pas les valeurs Null.</span><span class="sxs-lookup"><span data-stu-id="87993-370">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="87993-371">Validation de [Required] sur le serveur</span><span class="sxs-lookup"><span data-stu-id="87993-371">[Required] validation on the server</span></span>

<span data-ttu-id="87993-372">Sur le serveur, une valeur obligatoire est considérée comme manquante si la propriété est Null.</span><span class="sxs-lookup"><span data-stu-id="87993-372">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="87993-373">Un champ n’acceptant pas les valeurs Null est toujours valide, et le message d’erreur de l’attribut [Required] n’est jamais affiché.</span><span class="sxs-lookup"><span data-stu-id="87993-373">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="87993-374">Toutefois, la liaison de modèle pour une propriété n’acceptant pas les valeurs Null peut échouer, entraînant l’affichage d’un message d’erreur tel que `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="87993-374">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="87993-375">Pour spécifier un message d’erreur personnalisé pour la validation côté serveur des types n’acceptant pas les valeurs Null, vous disposez des options suivantes :</span><span class="sxs-lookup"><span data-stu-id="87993-375">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="87993-376">Rendre le champ Nullable (par exemple, `decimal?` au lieu de `decimal`).</span><span class="sxs-lookup"><span data-stu-id="87993-376">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="87993-377">[Valeur \<T> null](/dotnet/csharp/programming-guide/nullable-types/) les types valeur sont traités comme des types Nullable standard.</span><span class="sxs-lookup"><span data-stu-id="87993-377">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="87993-378">Spécifier le message d’erreur par défaut devant être utilisé par la liaison de modèle, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="87993-378">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="87993-379">Pour plus d’informations sur les erreurs de liaison de modèle pour lesquelles vous pouvez définir des messages par défaut, consultez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="87993-379">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="87993-380">Validation de [Required] sur le client</span><span class="sxs-lookup"><span data-stu-id="87993-380">[Required] validation on the client</span></span>

<span data-ttu-id="87993-381">Les chaînes et les types n’acceptant pas les valeurs Null sont gérés différemment sur le client et sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="87993-381">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="87993-382">Sur le client :</span><span class="sxs-lookup"><span data-stu-id="87993-382">On the client:</span></span>

* <span data-ttu-id="87993-383">Une valeur est considérée comme présente uniquement si une entrée est tapée pour celle-ci.</span><span class="sxs-lookup"><span data-stu-id="87993-383">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="87993-384">Par conséquent, la validation côté client gère les types n’acceptant pas les valeurs Null de la même façon que les types Nullable</span><span class="sxs-lookup"><span data-stu-id="87993-384">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="87993-385">Un espace blanc dans un champ de chaîne est considéré comme une entrée valide par la méthode [required](https://jqueryvalidation.org/required-method/) jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="87993-385">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="87993-386">La validation côté serveur considère qu’un champ de chaîne obligatoire est non valide si seul un espace blanc est entré.</span><span class="sxs-lookup"><span data-stu-id="87993-386">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="87993-387">Comme indiqué précédemment, les types n’acceptant pas les valeurs Null sont traités comme s’ils avaient un attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="87993-387">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="87993-388">Cela signifie que vous bénéficiez d’une validation côté client même si vous n’appliquez pas l’attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="87993-388">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="87993-389">En revanche, si vous n’utilisez pas l’attribut, vous recevez un message d’erreur par défaut.</span><span class="sxs-lookup"><span data-stu-id="87993-389">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="87993-390">Pour spécifier un message d’erreur personnalisé, utilisez l’attribut.</span><span class="sxs-lookup"><span data-stu-id="87993-390">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="87993-391">Attribut [Remote]</span><span class="sxs-lookup"><span data-stu-id="87993-391">[Remote] attribute</span></span>

<span data-ttu-id="87993-392">L’attribut `[Remote]` implémente la validation côté client qui nécessite l’appel d’une méthode sur le serveur pour déterminer si le champ d’entrée est valide.</span><span class="sxs-lookup"><span data-stu-id="87993-392">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="87993-393">Par exemple, l’application peut avoir besoin de vérifier si un nom d’utilisateur est déjà en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="87993-393">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="87993-394">Pour implémenter la validation à distance</span><span class="sxs-lookup"><span data-stu-id="87993-394">To implement remote validation:</span></span>

1. <span data-ttu-id="87993-395">Créez une méthode d’action devant être appelée par JavaScript.</span><span class="sxs-lookup"><span data-stu-id="87993-395">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="87993-396">La méthode [remote](https://jqueryvalidation.org/remote-method/) jQuery Validate attend une réponse JSON :</span><span class="sxs-lookup"><span data-stu-id="87993-396">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="87993-397">`"true"` signifie que les données d’entrée sont valides.</span><span class="sxs-lookup"><span data-stu-id="87993-397">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="87993-398">`"false"`, `undefined` ou `null` signifie que l’entrée n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="87993-398">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="87993-399">Affichez le message d’erreur par défaut.</span><span class="sxs-lookup"><span data-stu-id="87993-399">Display the default error message.</span></span>
   * <span data-ttu-id="87993-400">Toute autre chaîne signifie que l’entrée n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="87993-400">Any other string means the input is invalid.</span></span> <span data-ttu-id="87993-401">Affichez la chaîne en tant que message d’erreur personnalisé.</span><span class="sxs-lookup"><span data-stu-id="87993-401">Display the string as a custom error message.</span></span>

   <span data-ttu-id="87993-402">Voici un exemple de méthode d’action qui retourne un message d’erreur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="87993-402">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="87993-403">Dans la classe de modèle, annotez la propriété avec un attribut `[Remote]` qui pointe vers la méthode d’action de validation, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="87993-403">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="87993-404">L’attribut `[Remote]` se trouve dans l’espace de noms `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="87993-404">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="87993-405">Installez le package NuGet [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures), si vous n’utilisez pas le métapaquet `Microsoft.AspNetCore.App` ou `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="87993-405">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="87993-406">Champs supplémentaires</span><span class="sxs-lookup"><span data-stu-id="87993-406">Additional fields</span></span>

<span data-ttu-id="87993-407">La propriété `AdditionalFields` de l’attribut `[Remote]` vous permet de valider des combinaisons de champs relativement à des données présentes sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="87993-407">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="87993-408">Par exemple, si le modèle `User` a des propriétés `FirstName` et `LastName`, vous pouvez vérifier qu’aucun utilisateur existant n’a déjà cette paire de noms.</span><span class="sxs-lookup"><span data-stu-id="87993-408">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="87993-409">L'exemple suivant montre comment utiliser `AdditionalFields` :</span><span class="sxs-lookup"><span data-stu-id="87993-409">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="87993-410">`AdditionalFields` peut être défini explicitement sur les chaînes `"FirstName"` et `"LastName"` , mais l’utilisation de l’opérateur [nameof](/dotnet/csharp/language-reference/keywords/nameof) simplifie la refactorisation ultérieure.</span><span class="sxs-lookup"><span data-stu-id="87993-410">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="87993-411">La méthode d’action pour cette validation doit accepter les arguments de nom et de prénom :</span><span class="sxs-lookup"><span data-stu-id="87993-411">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="87993-412">Quand l’utilisateur entre un nom ou un prénom, JavaScript effectue un appel à distance pour vérifier si cette paire de noms est déjà utilisée.</span><span class="sxs-lookup"><span data-stu-id="87993-412">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="87993-413">Pour valider deux champs supplémentaires ou plus, spécifiez-les sous la forme d’une liste délimitée par des virgules.</span><span class="sxs-lookup"><span data-stu-id="87993-413">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="87993-414">Par exemple, pour ajouter une propriété `MiddleName` au modèle, définissez l’attribut `[Remote]` comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="87993-414">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="87993-415">`AdditionalFields`, comme tous les arguments d’attribut, doit être une expression constante.</span><span class="sxs-lookup"><span data-stu-id="87993-415">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="87993-416">Vous ne devez donc pas utiliser une [chaîne interpolée](/dotnet/csharp/language-reference/keywords/interpolated-strings) ou appeler <xref:System.String.Join*> pour initialiser `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="87993-416">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="87993-417">Alternatives aux attributs prédéfinis</span><span class="sxs-lookup"><span data-stu-id="87993-417">Alternatives to built-in attributes</span></span>

<span data-ttu-id="87993-418">Si vous avez besoin d’une validation non fournie par les attributs prédéfinis, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="87993-418">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="87993-419">[Créer des attributs personnalisés](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="87993-419">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="87993-420">[Implémenter IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="87993-420">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="87993-421">Attributs personnalisés</span><span class="sxs-lookup"><span data-stu-id="87993-421">Custom attributes</span></span>

<span data-ttu-id="87993-422">Pour les scénarios non gérés par les attributs de validation prédéfinis, vous pouvez créer des attributs de validation personnalisés.</span><span class="sxs-lookup"><span data-stu-id="87993-422">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="87993-423">Créez une classe qui hérite de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> et substituez la méthode <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="87993-423">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="87993-424">La méthode `IsValid` accepte un objet nommé *value* , qui est l’entrée à valider.</span><span class="sxs-lookup"><span data-stu-id="87993-424">The `IsValid` method accepts an object named *value* , which is the input to be validated.</span></span> <span data-ttu-id="87993-425">Une surcharge accepte également un objet `ValidationContext`, qui fournit des informations supplémentaires telles que l’instance de modèle créée par la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="87993-425">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="87993-426">L’exemple suivant vérifie que la date de sortie d’un film appartenant au genre *Classic* n’est pas ultérieure à une année spécifiée.</span><span class="sxs-lookup"><span data-stu-id="87993-426">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="87993-427">L’attribut `[ClassicMovie2]` vérifie d’abord le genre, et continue uniquement s’il s’agit de *Classic* .</span><span class="sxs-lookup"><span data-stu-id="87993-427">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic* .</span></span> <span data-ttu-id="87993-428">Pour les films identifiés comme des classiques, il vérifie si la date de sortie n’est pas ultérieure à la limite passée au constructeur d’attribut.</span><span class="sxs-lookup"><span data-stu-id="87993-428">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="87993-429">La variable `movie` de l’exemple précédent représente un objet `Movie` qui contient les données de l’envoi du formulaire.</span><span class="sxs-lookup"><span data-stu-id="87993-429">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="87993-430">La méthode `IsValid` vérifie la date et le genre.</span><span class="sxs-lookup"><span data-stu-id="87993-430">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="87993-431">Si la validation réussit, `IsValid` retourne un code `ValidationResult.Success`.</span><span class="sxs-lookup"><span data-stu-id="87993-431">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="87993-432">Quand la validation échoue, un `ValidationResult` avec un message d’erreur est retourné.</span><span class="sxs-lookup"><span data-stu-id="87993-432">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="87993-433">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="87993-433">IValidatableObject</span></span>

<span data-ttu-id="87993-434">L’exemple précédent fonctionne uniquement avec les types `Movie`.</span><span class="sxs-lookup"><span data-stu-id="87993-434">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="87993-435">Une autre option de validation au niveau de la classe consiste à implémenter `IValidatableObject` dans la classe de modèle, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="87993-435">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="87993-436">Validation du nœud de niveau supérieur</span><span class="sxs-lookup"><span data-stu-id="87993-436">Top-level node validation</span></span>

<span data-ttu-id="87993-437">Les nœuds de niveau supérieur incluent les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="87993-437">Top-level nodes include:</span></span>

* <span data-ttu-id="87993-438">Paramètres d’action</span><span class="sxs-lookup"><span data-stu-id="87993-438">Action parameters</span></span>
* <span data-ttu-id="87993-439">Propriétés du contrôleur</span><span class="sxs-lookup"><span data-stu-id="87993-439">Controller properties</span></span>
* <span data-ttu-id="87993-440">Paramètres du gestionnaire de page</span><span class="sxs-lookup"><span data-stu-id="87993-440">Page handler parameters</span></span>
* <span data-ttu-id="87993-441">Propriétés du modèle de page</span><span class="sxs-lookup"><span data-stu-id="87993-441">Page model properties</span></span>

<span data-ttu-id="87993-442">Les nœuds de niveau supérieur liés au modèle sont validés en plus de la validation des propriétés du modèle.</span><span class="sxs-lookup"><span data-stu-id="87993-442">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="87993-443">Dans l’exemple suivant tiré de l’exemple d’application, la méthode `VerifyPhone` utilise <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> pour valider le paramètre d’action `phone` :</span><span class="sxs-lookup"><span data-stu-id="87993-443">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="87993-444">Les nœuds de niveau supérieur peuvent utiliser <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> avec des attributs de validation.</span><span class="sxs-lookup"><span data-stu-id="87993-444">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="87993-445">Dans l’exemple suivant de l’exemple d’application, la méthode `CheckAge` spécifie que le paramètre `age` doit être lié à partir de la chaîne de requête au moment de l’envoi du formulaire :</span><span class="sxs-lookup"><span data-stu-id="87993-445">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="87993-446">Dans la page de vérification de l’âge ( *CheckAge.cshtml* ), il existe deux formulaires.</span><span class="sxs-lookup"><span data-stu-id="87993-446">In the Check Age page ( *CheckAge.cshtml* ), there are two forms.</span></span> <span data-ttu-id="87993-447">Le premier formulaire envoie une valeur `Age` égale à `99` en tant que chaîne de requête : `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="87993-447">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="87993-448">Quand un paramètre `age` au format approprié est envoyé à partir de la chaîne de requête, le formulaire est validé.</span><span class="sxs-lookup"><span data-stu-id="87993-448">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="87993-449">Le second formulaire de la page de vérification de l’âge envoie la valeur `Age` dans le corps de la requête, ce qui entraîne un échec de la validation.</span><span class="sxs-lookup"><span data-stu-id="87993-449">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="87993-450">L’échec de la liaison est dû au fait que le paramètre `age` doit provenir d’une chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="87993-450">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="87993-451">Lors de l’exécution avec `CompatibilityVersion.Version_2_1` ou version ultérieure, la validation du nœud de niveau supérieur est activée par défaut.</span><span class="sxs-lookup"><span data-stu-id="87993-451">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="87993-452">Autrement, la validation du nœud de niveau supérieur est désactivée.</span><span class="sxs-lookup"><span data-stu-id="87993-452">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="87993-453">L’option par défaut peut être remplacée en définissant la propriété <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> dans (`Startup.ConfigureServices`), comme illustré ici :</span><span class="sxs-lookup"><span data-stu-id="87993-453">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="87993-454">Quantité maximale d’erreurs</span><span class="sxs-lookup"><span data-stu-id="87993-454">Maximum errors</span></span>

<span data-ttu-id="87993-455">La validation s’arrête quand le nombre maximal d’erreurs est atteint (200 par défaut).</span><span class="sxs-lookup"><span data-stu-id="87993-455">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="87993-456">Vous pouvez configurer ce nombre avec le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="87993-456">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="87993-457">Récursivité maximale</span><span class="sxs-lookup"><span data-stu-id="87993-457">Maximum recursion</span></span>

<span data-ttu-id="87993-458"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> parcourt le graphe d’objet du modèle en cours de validation.</span><span class="sxs-lookup"><span data-stu-id="87993-458"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="87993-459">Pour les modèles très profonds ou infiniment récursifs, la validation peut entraîner un dépassement de la capacité de la pile.</span><span class="sxs-lookup"><span data-stu-id="87993-459">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="87993-460">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) fournit un moyen d’interrompre la validation de manière anticipée si la récursivité du visiteur dépasse une profondeur configurée.</span><span class="sxs-lookup"><span data-stu-id="87993-460">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="87993-461">La valeur par défaut de `MvcOptions.MaxValidationDepth` est 32 lors de l’exécution avec `CompatibilityVersion.Version_2_2` ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="87993-461">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="87993-462">Pour les versions antérieures, la valeur est Null, ce qui signifie qu’il n’y a aucune contrainte de profondeur.</span><span class="sxs-lookup"><span data-stu-id="87993-462">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="87993-463">Court-circuit automatique</span><span class="sxs-lookup"><span data-stu-id="87993-463">Automatic short-circuit</span></span>

<span data-ttu-id="87993-464">La validation est automatiquement court-circuitée (ignorée) si le graphe du modèle ne nécessite pas de validation.</span><span class="sxs-lookup"><span data-stu-id="87993-464">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="87993-465">Les objets pour lesquels le runtime ignore la validation comprennent les collections de primitives (telles que `byte[]`, `string[]`, `Dictionary<string, string>`) et les graphes d’objets complexes qui n’ont pas de validateur.</span><span class="sxs-lookup"><span data-stu-id="87993-465">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="87993-466">Désactiver la validation</span><span class="sxs-lookup"><span data-stu-id="87993-466">Disable validation</span></span>

<span data-ttu-id="87993-467">Pour désactiver la validation</span><span class="sxs-lookup"><span data-stu-id="87993-467">To disable validation:</span></span>

1. <span data-ttu-id="87993-468">Créez une implémentation de `IObjectModelValidator` qui ne marque aucun champ comme étant non valide.</span><span class="sxs-lookup"><span data-stu-id="87993-468">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="87993-469">Ajoutez le code suivant à `Startup.ConfigureServices` pour remplacer l’implémentation par défaut de `IObjectModelValidator` dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="87993-469">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="87993-470">Vous risquez toujours de voir des erreurs d’état du modèle provenant de la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="87993-470">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="87993-471">Validation côté client</span><span class="sxs-lookup"><span data-stu-id="87993-471">Client-side validation</span></span>

<span data-ttu-id="87993-472">La validation côté client empêche l’envoi jusqu’à ce que le formulaire soit valide.</span><span class="sxs-lookup"><span data-stu-id="87993-472">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="87993-473">Le bouton Submit exécute le code JavaScript qui envoie le formulaire ou qui affiche des messages d’erreur.</span><span class="sxs-lookup"><span data-stu-id="87993-473">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="87993-474">La validation côté client permet d’éviter un aller-retour inutile vers le serveur quand il existe des erreurs d’entrée sur un formulaire.</span><span class="sxs-lookup"><span data-stu-id="87993-474">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="87993-475">Les références de script suivantes dans *_Layout.cshtml* et *_ValidationScriptsPartial.cshtml* prennent en charge la validation côté client :</span><span class="sxs-lookup"><span data-stu-id="87993-475">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="87993-476">Le script [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) est une bibliothèque frontale personnalisée de Microsoft qui s’appuie sur le plug-in bien connu [jQuery Validate](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="87993-476">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="87993-477">Sans jQuery Unobtrusive Validation, vous devriez coder la même logique de validation à deux endroits : une fois dans les attributs de validation côté serveur sur les propriétés du modèle, puis à nouveau dans les scripts côté client.</span><span class="sxs-lookup"><span data-stu-id="87993-477">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="87993-478">Au lieu de cela, les [Tag Helpers](xref:mvc/views/tag-helpers/intro) et les [helpers HTML](xref:mvc/views/overview) utilisent les attributs de validation et les métadonnées de type des propriétés du modèle afin de restituer les attributs `data-` HTML 5 pour les éléments de formulaire nécessitant une validation.</span><span class="sxs-lookup"><span data-stu-id="87993-478">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="87993-479">jQuery Unobtrusive Validation analyse les attributs `data-` et passe la logique à jQuery Validate, en « copiant » la logique de validation côté serveur vers le client.</span><span class="sxs-lookup"><span data-stu-id="87993-479">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="87993-480">Vous pouvez afficher les erreurs de validation sur le client en utilisant des Tag Helpers, comme indiqué ici :</span><span class="sxs-lookup"><span data-stu-id="87993-480">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="87993-481">Les Tag Helpers précédents restituent le code HTML suivant.</span><span class="sxs-lookup"><span data-stu-id="87993-481">The preceding tag helpers render the following HTML.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="87993-482">Notez que les attributs `data-` dans la sortie HTML correspondent aux attributs de validation pour la propriété `ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="87993-482">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="87993-483">L’attribut `data-val-required` contient un message d’erreur à afficher si l’utilisateur ne renseigne pas le champ correspondant à la date de sortie.</span><span class="sxs-lookup"><span data-stu-id="87993-483">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="87993-484">la validation jQuery discrète passe cette valeur à la méthode jQuery Validate [Required ()](https://jqueryvalidation.org/required-method/) , qui affiche ensuite ce message dans l' **\<span>** élément associé.</span><span class="sxs-lookup"><span data-stu-id="87993-484">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="87993-485">La validation du type de données est basée sur le type .NET d’une propriété, sauf en cas de substitution par un attribut `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="87993-485">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="87993-486">Les navigateurs ont leurs propres messages d’erreur par défaut, mais le package jQuery Validation Unobtrusive Validation peut remplacer ces messages.</span><span class="sxs-lookup"><span data-stu-id="87993-486">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="87993-487">Les attributs `[DataType]` et les sous-classes comme `[EmailAddress]` vous permettent de spécifier le message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="87993-487">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="87993-488">Ajouter une validation à des formulaires dynamiques</span><span class="sxs-lookup"><span data-stu-id="87993-488">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="87993-489">jQuery Unobtrusive Validation passe la logique et les paramètres de validation à jQuery Validate lors du premier chargement de la page.</span><span class="sxs-lookup"><span data-stu-id="87993-489">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="87993-490">Par conséquent, la validation ne fonctionne pas automatiquement sur les formulaires générés de manière dynamique.</span><span class="sxs-lookup"><span data-stu-id="87993-490">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="87993-491">Pour activer la validation, vous devez faire en sorte que jQuery Validate analyse le formulaire dynamique immédiatement après l’avoir créé.</span><span class="sxs-lookup"><span data-stu-id="87993-491">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="87993-492">Par exemple, le code suivant définit la validation côté client sur un formulaire ajouté par le biais d’AJAX.</span><span class="sxs-lookup"><span data-stu-id="87993-492">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="87993-493">La méthode `$.validator.unobtrusive.parse()` accepte un sélecteur jQuery comme argument.</span><span class="sxs-lookup"><span data-stu-id="87993-493">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="87993-494">Cette méthode indique à jQuery Unobtrusive Validation d’analyser les attributs `data-` des formulaires dans ce sélecteur.</span><span class="sxs-lookup"><span data-stu-id="87993-494">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="87993-495">Les valeurs de ces attributs sont ensuite passées au plug-in jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="87993-495">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="87993-496">Ajouter une validation à des contrôles dynamiques</span><span class="sxs-lookup"><span data-stu-id="87993-496">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="87993-497">La méthode `$.validator.unobtrusive.parse()` opère sur un formulaire entier, et non sur des contrôles individuels générés de manière dynamique tels que `<input>` et `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="87993-497">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="87993-498">Pour réanalyser le formulaire, supprimez les données de validation qui ont été ajoutées quand le formulaire a été analysé précédemment, comme illustré dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="87993-498">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="87993-499">Validation personnalisée côté client</span><span class="sxs-lookup"><span data-stu-id="87993-499">Custom client-side validation</span></span>

<span data-ttu-id="87993-500">La validation personnalisée côté client s’effectue en générant des attributs HTML `data-` qui fonctionnent avec un adaptateur jQuery Validate personnalisé.</span><span class="sxs-lookup"><span data-stu-id="87993-500">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="87993-501">L’exemple de code d’adaptateur suivant a été écrit pour les attributs `ClassicMovie` et `ClassicMovie2` qui ont été introduits plus haut dans cet article :</span><span class="sxs-lookup"><span data-stu-id="87993-501">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="87993-502">Pour plus d’informations sur la façon d’écrire des adaptateurs, consultez la [documentation de jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="87993-502">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="87993-503">L’utilisation d’un adaptateur pour un champ donné est déclenchée par des attributs `data-` qui :</span><span class="sxs-lookup"><span data-stu-id="87993-503">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="87993-504">Marquent le champ comme étant soumis à validation (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="87993-504">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="87993-505">Identifient un nom de règle de validation et un texte de message d’erreur (par exemple, `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="87993-505">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="87993-506">Fournissent les éventuels paramètres supplémentaires dont le validateur a besoin (par exemple, `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="87993-506">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="87993-507">L’exemple suivant montre les attributs `data-` pour l’attribut `ClassicMovie` de l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="87993-507">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="87993-508">Comme mentionné plus haut, les [Tag Helpers](xref:mvc/views/tag-helpers/intro) et les [helpers HTML](xref:mvc/views/overview) utilisent les informations des attributs de validation pour restituer les attributs `data-`.</span><span class="sxs-lookup"><span data-stu-id="87993-508">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="87993-509">Il existe deux options pour l’écriture de code qui entraîne la création d’attributs HTML `data-` personnalisés :</span><span class="sxs-lookup"><span data-stu-id="87993-509">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="87993-510">Créez une classe qui dérive de `AttributeAdapterBase<TAttribute>` et une classe qui implémente `IValidationAttributeAdapterProvider`, et inscrivez votre attribut et son adaptateur dans l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="87993-510">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="87993-511">Cette méthode respecte le [principe de responsabilité unique](https://wikipedia.org/wiki/Single_responsibility_principle), dans la mesure où le code de validation lié au serveur et celui lié au client se trouvent dans des classes distinctes.</span><span class="sxs-lookup"><span data-stu-id="87993-511">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="87993-512">Il existe un autre avantage : l’adaptateur étant inscrit dans l’injection de dépendances, les autres services dans l’injection de dépendances lui sont accessibles si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="87993-512">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="87993-513">Implémentez `IClientModelValidator` dans votre classe `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="87993-513">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="87993-514">Cette méthode peut être appropriée si l’attribut n’effectue aucune validation côté serveur et n’a besoin d’aucun service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="87993-514">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="87993-515">AttributeAdapter pour la validation côté client</span><span class="sxs-lookup"><span data-stu-id="87993-515">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="87993-516">Cette méthode de rendu des attributs `data-` en HTML est utilisée par l’attribut `ClassicMovie` dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="87993-516">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="87993-517">Pour ajouter la validation côté client à l’aide de cette méthode</span><span class="sxs-lookup"><span data-stu-id="87993-517">To add client validation by using this method:</span></span>

1. <span data-ttu-id="87993-518">Créez une classe d’adaptateurs d’attributs pour l’attribut de validation personnalisé.</span><span class="sxs-lookup"><span data-stu-id="87993-518">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="87993-519">Dérivez la classe de [AttributeAdapterBase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="87993-519">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="87993-520">Créez une méthode `AddValidation` qui ajoute des attributs `data-` à la sortie restituée, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="87993-520">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="87993-521">Créez une classe de fournisseurs d’adaptateurs qui implémente <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="87993-521">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="87993-522">Dans la méthode `GetAttributeAdapter`, passez l’attribut personnalisé au constructeur de l’adaptateur, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="87993-522">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="87993-523">Inscrivez le fournisseur d’adaptateurs auprès de l’injection de dépendances dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="87993-523">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="87993-524">IClientModelValidator pour la validation côté client</span><span class="sxs-lookup"><span data-stu-id="87993-524">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="87993-525">Cette méthode de rendu des attributs `data-` en HTML est utilisée par l’attribut `ClassicMovie2` dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="87993-525">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="87993-526">Pour ajouter la validation côté client à l’aide de cette méthode</span><span class="sxs-lookup"><span data-stu-id="87993-526">To add client validation by using this method:</span></span>

* <span data-ttu-id="87993-527">Dans l’attribut de validation personnalisé, implémentez l’interface `IClientModelValidator` et créez une méthode `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="87993-527">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="87993-528">Dans la méthode `AddValidation`, ajoutez des attributs `data-` pour la validation, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="87993-528">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="87993-529">Désactiver la validation côté client</span><span class="sxs-lookup"><span data-stu-id="87993-529">Disable client-side validation</span></span>

<span data-ttu-id="87993-530">Le code suivant désactive la validation côté client dans les vues MVC :</span><span class="sxs-lookup"><span data-stu-id="87993-530">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="87993-531">Et dans les :::no-loc(Razor)::: pages :</span><span class="sxs-lookup"><span data-stu-id="87993-531">And in :::no-loc(Razor)::: Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="87993-532">Une autre option permettant de désactiver la validation côté client consiste à commenter la référence à `_ValidationScriptsPartial` dans votre fichier *.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="87993-532">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87993-533">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="87993-533">Additional resources</span></span>

* [<span data-ttu-id="87993-534">Espace de noms System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="87993-534">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="87993-535">Liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="87993-535">Model Binding</span></span>](model-binding.md)

::: moniker-end
