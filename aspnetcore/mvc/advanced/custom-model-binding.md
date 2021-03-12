---
title: Liaison de données personnalisée dans ASP.NET Core
author: ardalis
description: Découvrez comment la liaison de données permet aux actions du contrôleur de fonctionner directement avec des types de modèle dans ASP.NET Core.
ms.author: riande
ms.date: 01/06/2020
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
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: b674d3a034124a26e2e76f2a7f0220e3f0ecea56
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588768"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="017da-103">Liaison de données personnalisée dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="017da-103">Custom Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="017da-104">Par [Steve Smith](https://ardalis.com/) et [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="017da-104">By [Steve Smith](https://ardalis.com/) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="017da-105">La liaison de données permet aux actions du contrôleur de fonctionner directement avec des types de modèle (passés en tant qu’arguments de méthode), plutôt qu’avec des requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="017da-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="017da-106">Le mappage entre les données de requête entrantes et les modèles d’application est pris en charge par les classeurs de modèles.</span><span class="sxs-lookup"><span data-stu-id="017da-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="017da-107">Les développeurs peuvent étendre la fonctionnalité de liaison de données intégrée en implémentant des classeurs de modèles personnalisés (même si, en règle générale, vous n’avez pas besoin d’écrire votre propre fournisseur).</span><span class="sxs-lookup"><span data-stu-id="017da-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="017da-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="017da-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="017da-109">Limitations du classeur de modèles par défaut</span><span class="sxs-lookup"><span data-stu-id="017da-109">Default model binder limitations</span></span>

<span data-ttu-id="017da-110">Les classeurs de modèles par défaut prennent en charge la plupart des types de données .NET Core usuels et doivent répondre aux besoins de la majorité des développeurs.</span><span class="sxs-lookup"><span data-stu-id="017da-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="017da-111">Ils sont censés lier directement les entrées textuelles de la requête aux types de modèle.</span><span class="sxs-lookup"><span data-stu-id="017da-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="017da-112">Vous devrez peut-être transformer l’entrée avant de la lier.</span><span class="sxs-lookup"><span data-stu-id="017da-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="017da-113">Par exemple, quand vous avez une clé qui peut être utilisée pour rechercher des données de modèle.</span><span class="sxs-lookup"><span data-stu-id="017da-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="017da-114">Vous pouvez utiliser un classeur de modèles personnalisé pour récupérer (fetch) les données en fonction de la clé.</span><span class="sxs-lookup"><span data-stu-id="017da-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="017da-115">Vérification de la liaison de données</span><span class="sxs-lookup"><span data-stu-id="017da-115">Model binding review</span></span>

<span data-ttu-id="017da-116">La liaison de données utilise des définitions spécifiques pour les types sur lesquels elle opère.</span><span class="sxs-lookup"><span data-stu-id="017da-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="017da-117">Un *type simple* est converti à partir d’une seule chaîne dans l’entrée.</span><span class="sxs-lookup"><span data-stu-id="017da-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="017da-118">Un *type complexe* est converti à partir de plusieurs valeurs d’entrée.</span><span class="sxs-lookup"><span data-stu-id="017da-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="017da-119">Le framework détermine la différence en fonction de l’existence de `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="017da-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="017da-120">Nous vous recommandons de créer un convertisseur de type si vous disposez d’un mappage `string` -> `SomeType` simple qui ne nécessite pas de ressources externes.</span><span class="sxs-lookup"><span data-stu-id="017da-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="017da-121">Avant de créer votre propre classeur de modèles personnalisé, vérifiez la façon dont les classeurs de modèles existants sont implémentés.</span><span class="sxs-lookup"><span data-stu-id="017da-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="017da-122">Considérez le <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> qui peut être utilisé pour convertir des chaînes encodées en base64 en tableaux d’octets.</span><span class="sxs-lookup"><span data-stu-id="017da-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="017da-123">Les tableaux d’octets sont souvent stockés sous forme de fichiers ou de champs BLOB de base de données.</span><span class="sxs-lookup"><span data-stu-id="017da-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="017da-124">Utilisation de ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="017da-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="017da-125">Les chaînes encodées au format Base64 peuvent être utilisées pour représenter des données binaires.</span><span class="sxs-lookup"><span data-stu-id="017da-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="017da-126">Par exemple, une image peut être encodée sous forme de chaîne.</span><span class="sxs-lookup"><span data-stu-id="017da-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="017da-127">L’exemple comprend une image sous forme de chaîne encodée en Base64 dans [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="017da-127">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="017da-128">ASP.NET Core MVC peut accepter une chaîne encodée au format base64 et utiliser `ByteArrayModelBinder` pour la convertir en tableau d’octets.</span><span class="sxs-lookup"><span data-stu-id="017da-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="017da-129">Les <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> `byte[]` arguments Maps pour `ByteArrayModelBinder` :</span><span class="sxs-lookup"><span data-stu-id="017da-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

<span data-ttu-id="017da-130">Quand vous créez votre propre classeur de modèles personnalisé, vous pouvez implémenter votre propre `IModelBinderProvider` type ou utiliser le <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .</span><span class="sxs-lookup"><span data-stu-id="017da-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="017da-131">L’exemple suivant montre comment utiliser `ByteArrayModelBinder` pour convertir une chaîne encodée au format base64 en `byte[]`, et comment enregistrer le résultat dans un fichier :</span><span class="sxs-lookup"><span data-stu-id="017da-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="017da-132">Vous pouvez envoyer (POST) une chaîne encodée au format base64 à cette méthode d’API à l’aide d’un outil tel que [Postman](https://www.getpostman.com/) :</span><span class="sxs-lookup"><span data-stu-id="017da-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="017da-133">![Postman](custom-model-binding/images/postman.png "Postman")</span><span class="sxs-lookup"><span data-stu-id="017da-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="017da-134">Tant que le classeur peut lier les données de requête à des propriétés ou des arguments nommés de manière appropriée, la liaison de données s’effectue correctement.</span><span class="sxs-lookup"><span data-stu-id="017da-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="017da-135">L’exemple suivant montre comment utiliser `ByteArrayModelBinder` avec un modèle de vue :</span><span class="sxs-lookup"><span data-stu-id="017da-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="017da-136">Exemple de classeur de modèles personnalisé</span><span class="sxs-lookup"><span data-stu-id="017da-136">Custom model binder sample</span></span>

<span data-ttu-id="017da-137">Dans cette section, nous allons implémenter un classeur de modèles personnalisé qui :</span><span class="sxs-lookup"><span data-stu-id="017da-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="017da-138">Convertit les données de requête entrantes en arguments clés fortement typés.</span><span class="sxs-lookup"><span data-stu-id="017da-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="017da-139">Utilise Entity Framework Core pour récupérer (fetch) l’entité associée.</span><span class="sxs-lookup"><span data-stu-id="017da-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="017da-140">Passe l’entité associée en tant qu’argument à la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="017da-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="017da-141">L’exemple suivant utilise l’attribut `ModelBinder` pour le modèle `Author` :</span><span class="sxs-lookup"><span data-stu-id="017da-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="017da-142">Dans le code précédent, l’attribut `ModelBinder` spécifie le type de `IModelBinder` à utiliser pour lier les paramètres d’action de `Author`.</span><span class="sxs-lookup"><span data-stu-id="017da-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="017da-143">La classe `AuthorEntityBinder` suivante est utilisée pour lier un paramètre `Author` en récupérant (fetch) l’entité à partir d’une source de données via Entity Framework Core et `authorId` :</span><span class="sxs-lookup"><span data-stu-id="017da-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> <span data-ttu-id="017da-144">La classe `AuthorEntityBinder` précédente est destinée à illustrer un classeur de modèles personnalisé.</span><span class="sxs-lookup"><span data-stu-id="017da-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="017da-145">La classe n’est pas destinée à illustrer les bonnes pratiques pour un scénario de recherche.</span><span class="sxs-lookup"><span data-stu-id="017da-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="017da-146">Pour la recherche, liez `authorId` et interrogez la base de données dans une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="017da-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="017da-147">Cette approche sépare les échecs de liaison des modèles des cas `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="017da-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="017da-148">Le code suivant montre comment utiliser `AuthorEntityBinder` dans une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="017da-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

<span data-ttu-id="017da-149">L’attribut `ModelBinder` permet d’appliquer `AuthorEntityBinder` aux paramètres qui n’utilisent pas les conventions par défaut :</span><span class="sxs-lookup"><span data-stu-id="017da-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

<span data-ttu-id="017da-150">Dans cet exemple, comme le nom de l’argument n’est pas le `authorId` par défaut, il est spécifié dans le paramètre à l’aide de l’attribut `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="017da-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="017da-151">Le contrôleur et la méthode d’action sont simplifiés par rapport à la recherche de l’entité dans la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="017da-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="017da-152">La logique permettant de récupérer (fetch) l’auteur à l’aide d’Entity Framework Core est déplacée vers le classeur de modèles.</span><span class="sxs-lookup"><span data-stu-id="017da-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="017da-153">Cela peut représenter une simplification considérable quand vous avez plusieurs méthodes qui sont liées au modèle `Author`.</span><span class="sxs-lookup"><span data-stu-id="017da-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="017da-154">Vous pouvez appliquer l’attribut `ModelBinder` à des propriétés de modèle individuelles (par exemple viewmodel) ou à des paramètres de méthode d’action afin de spécifier un classeur de modèles ou un nom de modèle particulier pour ce type ou cette action uniquement.</span><span class="sxs-lookup"><span data-stu-id="017da-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="017da-155">Implémentation de ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="017da-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="017da-156">Au lieu d’appliquer un attribut, vous pouvez implémenter `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="017da-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="017da-157">C’est ainsi que les classeurs de framework intégrés sont implémentés.</span><span class="sxs-lookup"><span data-stu-id="017da-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="017da-158">Quand vous spécifiez le type sur lequel votre classeur opère, vous spécifiez le type d’argument qu’il produit, et **non** l’entrée que votre classeur accepte.</span><span class="sxs-lookup"><span data-stu-id="017da-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="017da-159">Le fournisseur de classeurs suivant fonctionne avec `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="017da-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="017da-160">Quand il est ajouté à la collection de fournisseurs de MVC, vous n’avez pas besoin d’utiliser l’attribut `ModelBinder` sur `Author` ou sur les paramètres typés de `Author`.</span><span class="sxs-lookup"><span data-stu-id="017da-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="017da-161">Remarque : Le code précédent retourne `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="017da-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="017da-162">`BinderTypeModelBinder` sert de fabrique pour les classeurs de modèles et permet l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="017da-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="017da-163">`AuthorEntityBinder` a besoin de l’injection de dépendances pour accéder à EF Core.</span><span class="sxs-lookup"><span data-stu-id="017da-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="017da-164">Utilisez `BinderTypeModelBinder`, si votre classeur de modèles nécessite des services liés à l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="017da-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="017da-165">Pour utiliser un fournisseur de classeurs de modèles personnalisé, ajoutez-le à `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="017da-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

<span data-ttu-id="017da-166">Durant l’évaluation des classeurs de modèles, la collection de fournisseurs est examinée dans un certain ordre.</span><span class="sxs-lookup"><span data-stu-id="017da-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="017da-167">Le premier fournisseur qui retourne un Binder qui correspond au modèle d’entrée est utilisé.</span><span class="sxs-lookup"><span data-stu-id="017da-167">The first provider that returns a binder that matches the input model is used.</span></span> <span data-ttu-id="017da-168">L’ajout de votre fournisseur à la fin de la collection peut donc entraîner l’appel d’un classeur de modèles intégré avant que votre Binder personnalisé ait une chance.</span><span class="sxs-lookup"><span data-stu-id="017da-168">Adding your provider to the end of the collection may thus result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="017da-169">Dans cet exemple, le fournisseur personnalisé est ajouté au début de la collection pour garantir qu’il est toujours utilisé pour les `Author` arguments d’action.</span><span class="sxs-lookup"><span data-stu-id="017da-169">In this example, the custom provider is added to the beginning of the collection to ensure it's always used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="017da-170">Liaison de modèle polymorphe</span><span class="sxs-lookup"><span data-stu-id="017da-170">Polymorphic model binding</span></span>

<span data-ttu-id="017da-171">La liaison à différents modèles de types dérivés porte le nom de liaison de modèle polymorphe.</span><span class="sxs-lookup"><span data-stu-id="017da-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="017da-172">La liaison de modèle personnalisé polymorphe est requise lorsque la valeur de la demande doit être liée au type de modèle dérivé spécifique.</span><span class="sxs-lookup"><span data-stu-id="017da-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="017da-173">Liaison de modèle polymorphe :</span><span class="sxs-lookup"><span data-stu-id="017da-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="017da-174">N’est pas typique d’une API REST conçue pour interagir avec toutes les langues.</span><span class="sxs-lookup"><span data-stu-id="017da-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="017da-175">Rend difficile la raison des modèles liés.</span><span class="sxs-lookup"><span data-stu-id="017da-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="017da-176">Toutefois, si une application requiert une liaison de modèle polymorphe, une implémentation peut se présenter comme le code suivant :</span><span class="sxs-lookup"><span data-stu-id="017da-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="017da-177">Recommandations et bonnes pratiques</span><span class="sxs-lookup"><span data-stu-id="017da-177">Recommendations and best practices</span></span>

<span data-ttu-id="017da-178">Les classeurs de modèles personnalisés :</span><span class="sxs-lookup"><span data-stu-id="017da-178">Custom model binders:</span></span>

- <span data-ttu-id="017da-179">Ne doivent pas tenter de définir des codes d’état ou de retourner des résultats (par exemple, 404 Introuvable).</span><span class="sxs-lookup"><span data-stu-id="017da-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="017da-180">En cas d’échec de la liaison de données, un [filtre d’action](xref:mvc/controllers/filters) ou une logique située dans la méthode d’action elle-même doit prendre en charge l’erreur.</span><span class="sxs-lookup"><span data-stu-id="017da-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="017da-181">Sont surtout utiles pour éliminer le code répétitif et les problèmes transversaux des méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="017da-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="017da-182">Ne doivent pas être utilisés pour convertir une chaîne en type personnalisé. En règle générale, <xref:System.ComponentModel.TypeConverter> est une meilleure option.</span><span class="sxs-lookup"><span data-stu-id="017da-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="017da-183">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="017da-183">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="017da-184">La liaison de données permet aux actions du contrôleur de fonctionner directement avec des types de modèle (passés en tant qu’arguments de méthode), plutôt qu’avec des requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="017da-184">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="017da-185">Le mappage entre les données de requête entrantes et les modèles d’application est pris en charge par les classeurs de modèles.</span><span class="sxs-lookup"><span data-stu-id="017da-185">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="017da-186">Les développeurs peuvent étendre la fonctionnalité de liaison de données intégrée en implémentant des classeurs de modèles personnalisés (même si, en règle générale, vous n’avez pas besoin d’écrire votre propre fournisseur).</span><span class="sxs-lookup"><span data-stu-id="017da-186">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="017da-187">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="017da-187">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="017da-188">Limitations du classeur de modèles par défaut</span><span class="sxs-lookup"><span data-stu-id="017da-188">Default model binder limitations</span></span>

<span data-ttu-id="017da-189">Les classeurs de modèles par défaut prennent en charge la plupart des types de données .NET Core usuels et doivent répondre aux besoins de la majorité des développeurs.</span><span class="sxs-lookup"><span data-stu-id="017da-189">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="017da-190">Ils sont censés lier directement les entrées textuelles de la requête aux types de modèle.</span><span class="sxs-lookup"><span data-stu-id="017da-190">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="017da-191">Vous devrez peut-être transformer l’entrée avant de la lier.</span><span class="sxs-lookup"><span data-stu-id="017da-191">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="017da-192">Par exemple, quand vous avez une clé qui peut être utilisée pour rechercher des données de modèle.</span><span class="sxs-lookup"><span data-stu-id="017da-192">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="017da-193">Vous pouvez utiliser un classeur de modèles personnalisé pour récupérer (fetch) les données en fonction de la clé.</span><span class="sxs-lookup"><span data-stu-id="017da-193">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="017da-194">Vérification de la liaison de données</span><span class="sxs-lookup"><span data-stu-id="017da-194">Model binding review</span></span>

<span data-ttu-id="017da-195">La liaison de données utilise des définitions spécifiques pour les types sur lesquels elle opère.</span><span class="sxs-lookup"><span data-stu-id="017da-195">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="017da-196">Un *type simple* est converti à partir d’une seule chaîne dans l’entrée.</span><span class="sxs-lookup"><span data-stu-id="017da-196">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="017da-197">Un *type complexe* est converti à partir de plusieurs valeurs d’entrée.</span><span class="sxs-lookup"><span data-stu-id="017da-197">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="017da-198">Le framework détermine la différence en fonction de l’existence de `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="017da-198">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="017da-199">Nous vous recommandons de créer un convertisseur de type si vous disposez d’un mappage `string` -> `SomeType` simple qui ne nécessite pas de ressources externes.</span><span class="sxs-lookup"><span data-stu-id="017da-199">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="017da-200">Avant de créer votre propre classeur de modèles personnalisé, vérifiez la façon dont les classeurs de modèles existants sont implémentés.</span><span class="sxs-lookup"><span data-stu-id="017da-200">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="017da-201">Considérez le <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> qui peut être utilisé pour convertir des chaînes encodées en base64 en tableaux d’octets.</span><span class="sxs-lookup"><span data-stu-id="017da-201">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="017da-202">Les tableaux d’octets sont souvent stockés sous forme de fichiers ou de champs BLOB de base de données.</span><span class="sxs-lookup"><span data-stu-id="017da-202">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="017da-203">Utilisation de ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="017da-203">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="017da-204">Les chaînes encodées au format Base64 peuvent être utilisées pour représenter des données binaires.</span><span class="sxs-lookup"><span data-stu-id="017da-204">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="017da-205">Par exemple, une image peut être encodée sous forme de chaîne.</span><span class="sxs-lookup"><span data-stu-id="017da-205">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="017da-206">L’exemple comprend une image sous forme de chaîne encodée en Base64 dans [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="017da-206">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="017da-207">ASP.NET Core MVC peut accepter une chaîne encodée au format base64 et utiliser `ByteArrayModelBinder` pour la convertir en tableau d’octets.</span><span class="sxs-lookup"><span data-stu-id="017da-207">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="017da-208">Les <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> `byte[]` arguments Maps pour `ByteArrayModelBinder` :</span><span class="sxs-lookup"><span data-stu-id="017da-208">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="017da-209">Quand vous créez votre propre classeur de modèles personnalisé, vous pouvez implémenter votre propre `IModelBinderProvider` type ou utiliser le <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .</span><span class="sxs-lookup"><span data-stu-id="017da-209">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="017da-210">L’exemple suivant montre comment utiliser `ByteArrayModelBinder` pour convertir une chaîne encodée au format base64 en `byte[]`, et comment enregistrer le résultat dans un fichier :</span><span class="sxs-lookup"><span data-stu-id="017da-210">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="017da-211">Vous pouvez envoyer (POST) une chaîne encodée au format base64 à cette méthode d’API à l’aide d’un outil tel que [Postman](https://www.getpostman.com/) :</span><span class="sxs-lookup"><span data-stu-id="017da-211">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="017da-212">![Postman](custom-model-binding/images/postman.png "Postman")</span><span class="sxs-lookup"><span data-stu-id="017da-212">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="017da-213">Tant que le classeur peut lier les données de requête à des propriétés ou des arguments nommés de manière appropriée, la liaison de données s’effectue correctement.</span><span class="sxs-lookup"><span data-stu-id="017da-213">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="017da-214">L’exemple suivant montre comment utiliser `ByteArrayModelBinder` avec un modèle de vue :</span><span class="sxs-lookup"><span data-stu-id="017da-214">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="017da-215">Exemple de classeur de modèles personnalisé</span><span class="sxs-lookup"><span data-stu-id="017da-215">Custom model binder sample</span></span>

<span data-ttu-id="017da-216">Dans cette section, nous allons implémenter un classeur de modèles personnalisé qui :</span><span class="sxs-lookup"><span data-stu-id="017da-216">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="017da-217">Convertit les données de requête entrantes en arguments clés fortement typés.</span><span class="sxs-lookup"><span data-stu-id="017da-217">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="017da-218">Utilise Entity Framework Core pour récupérer (fetch) l’entité associée.</span><span class="sxs-lookup"><span data-stu-id="017da-218">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="017da-219">Passe l’entité associée en tant qu’argument à la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="017da-219">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="017da-220">L’exemple suivant utilise l’attribut `ModelBinder` pour le modèle `Author` :</span><span class="sxs-lookup"><span data-stu-id="017da-220">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="017da-221">Dans le code précédent, l’attribut `ModelBinder` spécifie le type de `IModelBinder` à utiliser pour lier les paramètres d’action de `Author`.</span><span class="sxs-lookup"><span data-stu-id="017da-221">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="017da-222">La classe `AuthorEntityBinder` suivante est utilisée pour lier un paramètre `Author` en récupérant (fetch) l’entité à partir d’une source de données via Entity Framework Core et `authorId` :</span><span class="sxs-lookup"><span data-stu-id="017da-222">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="017da-223">La classe `AuthorEntityBinder` précédente est destinée à illustrer un classeur de modèles personnalisé.</span><span class="sxs-lookup"><span data-stu-id="017da-223">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="017da-224">La classe n’est pas destinée à illustrer les bonnes pratiques pour un scénario de recherche.</span><span class="sxs-lookup"><span data-stu-id="017da-224">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="017da-225">Pour la recherche, liez `authorId` et interrogez la base de données dans une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="017da-225">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="017da-226">Cette approche sépare les échecs de liaison des modèles des cas `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="017da-226">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="017da-227">Le code suivant montre comment utiliser `AuthorEntityBinder` dans une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="017da-227">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="017da-228">L’attribut `ModelBinder` permet d’appliquer `AuthorEntityBinder` aux paramètres qui n’utilisent pas les conventions par défaut :</span><span class="sxs-lookup"><span data-stu-id="017da-228">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="017da-229">Dans cet exemple, comme le nom de l’argument n’est pas le `authorId` par défaut, il est spécifié dans le paramètre à l’aide de l’attribut `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="017da-229">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="017da-230">Le contrôleur et la méthode d’action sont simplifiés par rapport à la recherche de l’entité dans la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="017da-230">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="017da-231">La logique permettant de récupérer (fetch) l’auteur à l’aide d’Entity Framework Core est déplacée vers le classeur de modèles.</span><span class="sxs-lookup"><span data-stu-id="017da-231">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="017da-232">Cela peut représenter une simplification considérable quand vous avez plusieurs méthodes qui sont liées au modèle `Author`.</span><span class="sxs-lookup"><span data-stu-id="017da-232">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="017da-233">Vous pouvez appliquer l’attribut `ModelBinder` à des propriétés de modèle individuelles (par exemple viewmodel) ou à des paramètres de méthode d’action afin de spécifier un classeur de modèles ou un nom de modèle particulier pour ce type ou cette action uniquement.</span><span class="sxs-lookup"><span data-stu-id="017da-233">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="017da-234">Implémentation de ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="017da-234">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="017da-235">Au lieu d’appliquer un attribut, vous pouvez implémenter `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="017da-235">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="017da-236">C’est ainsi que les classeurs de framework intégrés sont implémentés.</span><span class="sxs-lookup"><span data-stu-id="017da-236">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="017da-237">Quand vous spécifiez le type sur lequel votre classeur opère, vous spécifiez le type d’argument qu’il produit, et **non** l’entrée que votre classeur accepte.</span><span class="sxs-lookup"><span data-stu-id="017da-237">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="017da-238">Le fournisseur de classeurs suivant fonctionne avec `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="017da-238">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="017da-239">Quand il est ajouté à la collection de fournisseurs de MVC, vous n’avez pas besoin d’utiliser l’attribut `ModelBinder` sur `Author` ou sur les paramètres typés de `Author`.</span><span class="sxs-lookup"><span data-stu-id="017da-239">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="017da-240">Remarque : Le code précédent retourne `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="017da-240">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="017da-241">`BinderTypeModelBinder` sert de fabrique pour les classeurs de modèles et permet l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="017da-241">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="017da-242">`AuthorEntityBinder` a besoin de l’injection de dépendances pour accéder à EF Core.</span><span class="sxs-lookup"><span data-stu-id="017da-242">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="017da-243">Utilisez `BinderTypeModelBinder`, si votre classeur de modèles nécessite des services liés à l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="017da-243">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="017da-244">Pour utiliser un fournisseur de classeurs de modèles personnalisé, ajoutez-le à `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="017da-244">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="017da-245">Durant l’évaluation des classeurs de modèles, la collection de fournisseurs est examinée dans un certain ordre.</span><span class="sxs-lookup"><span data-stu-id="017da-245">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="017da-246">Le premier fournisseur qui retourne un classeur est utilisé.</span><span class="sxs-lookup"><span data-stu-id="017da-246">The first provider that returns a binder is used.</span></span> <span data-ttu-id="017da-247">L’ajout de votre fournisseur à la fin de la collection peut entraîner l’appel d’un classeur de modèles intégré avant votre classeur personnalisé.</span><span class="sxs-lookup"><span data-stu-id="017da-247">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="017da-248">Dans cet exemple, le fournisseur personnalisé est ajouté au début de la collection afin qu’il soit utilisé pour les arguments d’action `Author`.</span><span class="sxs-lookup"><span data-stu-id="017da-248">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="017da-249">Liaison de modèle polymorphe</span><span class="sxs-lookup"><span data-stu-id="017da-249">Polymorphic model binding</span></span>

<span data-ttu-id="017da-250">La liaison à différents modèles de types dérivés porte le nom de liaison de modèle polymorphe.</span><span class="sxs-lookup"><span data-stu-id="017da-250">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="017da-251">La liaison de modèle personnalisé polymorphe est requise lorsque la valeur de la demande doit être liée au type de modèle dérivé spécifique.</span><span class="sxs-lookup"><span data-stu-id="017da-251">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="017da-252">Liaison de modèle polymorphe :</span><span class="sxs-lookup"><span data-stu-id="017da-252">Polymorphic model binding:</span></span>

* <span data-ttu-id="017da-253">N’est pas typique d’une API REST conçue pour interagir avec toutes les langues.</span><span class="sxs-lookup"><span data-stu-id="017da-253">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="017da-254">Rend difficile la raison des modèles liés.</span><span class="sxs-lookup"><span data-stu-id="017da-254">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="017da-255">Toutefois, si une application requiert une liaison de modèle polymorphe, une implémentation peut se présenter comme le code suivant :</span><span class="sxs-lookup"><span data-stu-id="017da-255">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="017da-256">Recommandations et bonnes pratiques</span><span class="sxs-lookup"><span data-stu-id="017da-256">Recommendations and best practices</span></span>

<span data-ttu-id="017da-257">Les classeurs de modèles personnalisés :</span><span class="sxs-lookup"><span data-stu-id="017da-257">Custom model binders:</span></span>

- <span data-ttu-id="017da-258">Ne doivent pas tenter de définir des codes d’état ou de retourner des résultats (par exemple, 404 Introuvable).</span><span class="sxs-lookup"><span data-stu-id="017da-258">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="017da-259">En cas d’échec de la liaison de données, un [filtre d’action](xref:mvc/controllers/filters) ou une logique située dans la méthode d’action elle-même doit prendre en charge l’erreur.</span><span class="sxs-lookup"><span data-stu-id="017da-259">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="017da-260">Sont surtout utiles pour éliminer le code répétitif et les problèmes transversaux des méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="017da-260">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="017da-261">Ne doivent pas être utilisés pour convertir une chaîne en type personnalisé. En règle générale, <xref:System.ComponentModel.TypeConverter> est une meilleure option.</span><span class="sxs-lookup"><span data-stu-id="017da-261">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
