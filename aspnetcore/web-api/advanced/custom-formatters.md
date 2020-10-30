---
title: Formateurs personnalisés dans l’API web ASP.NET Core
author: rick-anderson
description: Découvrez comment créer et utiliser des formateurs personnalisés pour les API web dans ASP.NET Core.
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: e4d73fdc0db3faeace5d68b3d71718315e68cae3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058920"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="c55dd-103">Formateurs personnalisés dans l’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c55dd-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="c55dd-104">Par [Kirk Larkin](https://twitter.com/serpent5) et [Tom Dykstra](https://github.com/tdykstra).</span><span class="sxs-lookup"><span data-stu-id="c55dd-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Tom Dykstra](https://github.com/tdykstra).</span></span>

<span data-ttu-id="c55dd-105">ASP.NET Core MVC prend en charge l’échange de données dans les API Web à l’aide de formateurs d’entrée et de sortie.</span><span class="sxs-lookup"><span data-stu-id="c55dd-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="c55dd-106">Les formateurs d’entrée sont utilisés par la [liaison de modèle](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="c55dd-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="c55dd-107">Les formateurs de sortie sont utilisés pour [mettre en forme les réponses](xref:web-api/advanced/formatting).</span><span class="sxs-lookup"><span data-stu-id="c55dd-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="c55dd-108">L’infrastructure fournit des formateurs d’entrée et de sortie intégrés pour JSON et XML.</span><span class="sxs-lookup"><span data-stu-id="c55dd-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="c55dd-109">Il fournit un formateur de sortie intégré pour le texte brut, mais ne fournit pas de formateur d’entrée pour le texte brut.</span><span class="sxs-lookup"><span data-stu-id="c55dd-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="c55dd-110">Cet article montre comment ajouter la prise en charge de formats supplémentaires en créant des formateurs personnalisés.</span><span class="sxs-lookup"><span data-stu-id="c55dd-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="c55dd-111">Pour obtenir un exemple de formateur d’entrée de texte brut personnalisé, consultez [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="c55dd-111">For an example of a custom plain text input formatter, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="c55dd-112">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c55dd-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="c55dd-113">Quand utiliser les formateurs personnalisés</span><span class="sxs-lookup"><span data-stu-id="c55dd-113">When to use custom formatters</span></span>

<span data-ttu-id="c55dd-114">Utilisez un formateur personnalisé pour ajouter la prise en charge d’un type de contenu qui n’est pas géré par les formateurs intégrés.</span><span class="sxs-lookup"><span data-stu-id="c55dd-114">Use a custom formatter to add support for a content type that isn't handled by the built-in formatters.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="c55dd-115">Présentation de l’utilisation d’un formateur personnalisé</span><span class="sxs-lookup"><span data-stu-id="c55dd-115">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="c55dd-116">Pour créer un formateur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="c55dd-116">To create a custom formatter:</span></span>

* <span data-ttu-id="c55dd-117">Pour sérialiser des données envoyées au client, créez une classe de formateur de sortie.</span><span class="sxs-lookup"><span data-stu-id="c55dd-117">For serializing data sent to the client, create an output formatter class.</span></span>
* <span data-ttu-id="c55dd-118">Pour désérialiser les données reçues du client, créez une classe de formateur d’entrée.</span><span class="sxs-lookup"><span data-stu-id="c55dd-118">For deserializing data received from the client, create an input formatter class.</span></span>
* <span data-ttu-id="c55dd-119">Ajoutez des instances de classes de formateur `InputFormatters` aux `OutputFormatters` collections et dans <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="c55dd-119">Add instances of formatter classes to the `InputFormatters` and `OutputFormatters` collections in <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="c55dd-120">Guide pratique pour créer une classe de formateur personnalisé</span><span class="sxs-lookup"><span data-stu-id="c55dd-120">How to create a custom formatter class</span></span>

<span data-ttu-id="c55dd-121">Pour créer un formateur :</span><span class="sxs-lookup"><span data-stu-id="c55dd-121">To create a formatter:</span></span>

* <span data-ttu-id="c55dd-122">Faites dériver la classe de la classe de base appropriée.</span><span class="sxs-lookup"><span data-stu-id="c55dd-122">Derive the class from the appropriate base class.</span></span> <span data-ttu-id="c55dd-123">L’exemple d’application dérive de <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> et de <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="c55dd-123">The sample app derives from <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> and <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter>.</span></span>
* <span data-ttu-id="c55dd-124">Spécifiez les encodages et types de média valides dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="c55dd-124">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="c55dd-125">Remplacez les méthodes <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> et <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>.</span><span class="sxs-lookup"><span data-stu-id="c55dd-125">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> and <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> methods.</span></span>
* <span data-ttu-id="c55dd-126">Remplacez les méthodes <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> et `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="c55dd-126">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> and `WriteResponseBodyAsync` methods.</span></span>

<span data-ttu-id="c55dd-127">Le code suivant illustre la `VcardOutputFormatter` classe de l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span><span class="sxs-lookup"><span data-stu-id="c55dd-127">The following code shows the `VcardOutputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="c55dd-128">Effectuer une dérivation à partir de la classe de base appropriée</span><span class="sxs-lookup"><span data-stu-id="c55dd-128">Derive from the appropriate base class</span></span>

<span data-ttu-id="c55dd-129">Pour les types de supports de texte (par exemple, vCard), dérivez de la <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> classe de base ou.</span><span class="sxs-lookup"><span data-stu-id="c55dd-129">For text media types (for example, vCard), derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> base class.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

<span data-ttu-id="c55dd-130">Pour les types binaires, dérivez de la <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> classe de base ou.</span><span class="sxs-lookup"><span data-stu-id="c55dd-130">For binary types, derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="c55dd-131">Spécifier les encodages et types de média valides</span><span class="sxs-lookup"><span data-stu-id="c55dd-131">Specify valid media types and encodings</span></span>

<span data-ttu-id="c55dd-132">Dans le constructeur, spécifiez les encodages et types de média valides en effectuant les ajouts nécessaires aux collections `SupportedMediaTypes` et `SupportedEncodings`.</span><span class="sxs-lookup"><span data-stu-id="c55dd-132">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

<span data-ttu-id="c55dd-133">Une classe de formateur **ne peut pas** utiliser l’injection de constructeur pour ses dépendances.</span><span class="sxs-lookup"><span data-stu-id="c55dd-133">A formatter class can **not** use constructor injection for its dependencies.</span></span> <span data-ttu-id="c55dd-134">Par exemple, `ILogger<VcardOutputFormatter>` ne peut pas être ajouté en tant que paramètre au constructeur.</span><span class="sxs-lookup"><span data-stu-id="c55dd-134">For example, `ILogger<VcardOutputFormatter>` cannot be added as a parameter to the constructor.</span></span> <span data-ttu-id="c55dd-135">Pour accéder aux services, utilisez l’objet de contexte qui est passé aux méthodes.</span><span class="sxs-lookup"><span data-stu-id="c55dd-135">To access services, use the context object that gets passed in to the methods.</span></span> <span data-ttu-id="c55dd-136">Un exemple de code dans cet article et l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) montrent comment procéder.</span><span class="sxs-lookup"><span data-stu-id="c55dd-136">A code example in this article and the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) show how to do this.</span></span>

### <a name="override-canreadtype-and-canwritetype"></a><span data-ttu-id="c55dd-137">Remplacer CanReadType et CanWriteType</span><span class="sxs-lookup"><span data-stu-id="c55dd-137">Override CanReadType and CanWriteType</span></span>

<span data-ttu-id="c55dd-138">Spécifiez le type à désérialiser ou à partir duquel effectuer la sérialisation en remplaçant les `CanReadType` `CanWriteType` méthodes ou.</span><span class="sxs-lookup"><span data-stu-id="c55dd-138">Specify the type to deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="c55dd-139">Par exemple, la création d’un texte vCard à partir d’un `Contact` type et vice versa.</span><span class="sxs-lookup"><span data-stu-id="c55dd-139">For example, creating vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="c55dd-140">Méthode CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="c55dd-140">The CanWriteResult method</span></span>

<span data-ttu-id="c55dd-141">Dans certains scénarios, `CanWriteResult` doit être substitué plutôt que `CanWriteType` .</span><span class="sxs-lookup"><span data-stu-id="c55dd-141">In some scenarios, `CanWriteResult` must be overridden rather than `CanWriteType`.</span></span> <span data-ttu-id="c55dd-142">Utilisez `CanWriteResult` si les conditions suivantes sont vraies :</span><span class="sxs-lookup"><span data-stu-id="c55dd-142">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="c55dd-143">La méthode d’action retourne une classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="c55dd-143">The action method returns a model class.</span></span>
* <span data-ttu-id="c55dd-144">Il existe des classes dérivées qui peuvent être retournées au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="c55dd-144">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="c55dd-145">La classe dérivée retournée par l’action doit être connue au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="c55dd-145">The derived class returned by the action must be known at runtime.</span></span>

<span data-ttu-id="c55dd-146">Par exemple, supposons que la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="c55dd-146">For example, suppose the action method:</span></span>

* <span data-ttu-id="c55dd-147">La signature retourne un `Person` type.</span><span class="sxs-lookup"><span data-stu-id="c55dd-147">Signature returns a `Person` type.</span></span>
* <span data-ttu-id="c55dd-148">Peut retourner un `Student` `Instructor` type ou qui dérive de `Person` .</span><span class="sxs-lookup"><span data-stu-id="c55dd-148">Can return a `Student` or `Instructor` type that derives from `Person`.</span></span> 

<span data-ttu-id="c55dd-149">Pour que le formateur gère uniquement les `Student` objets, vérifiez le type de <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> dans l’objet de contexte fourni à la `CanWriteResult` méthode.</span><span class="sxs-lookup"><span data-stu-id="c55dd-149">For the formatter to handle only `Student` objects, check the type of <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="c55dd-150">Lorsque la méthode d’action retourne `IActionResult` :</span><span class="sxs-lookup"><span data-stu-id="c55dd-150">When the action method returns `IActionResult`:</span></span>

* <span data-ttu-id="c55dd-151">Il n’est pas nécessaire d’utiliser `CanWriteResult` .</span><span class="sxs-lookup"><span data-stu-id="c55dd-151">It's not necessary to use `CanWriteResult`.</span></span>
* <span data-ttu-id="c55dd-152">La `CanWriteType` méthode reçoit le type au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="c55dd-152">The `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a><span data-ttu-id="c55dd-153">Remplacer ReadRequestBodyAsync et WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="c55dd-153">Override ReadRequestBodyAsync and WriteResponseBodyAsync</span></span>

<span data-ttu-id="c55dd-154">La désérialisation ou la sérialisation est effectuée dans `ReadRequestBodyAsync` ou `WriteResponseBodyAsync` .</span><span class="sxs-lookup"><span data-stu-id="c55dd-154">Deserialization or serialization is performed in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="c55dd-155">L’exemple suivant montre comment récupérer des services à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="c55dd-155">The following example shows how to get services from the dependency injection container.</span></span> <span data-ttu-id="c55dd-156">Les services ne peuvent pas être obtenus à partir des paramètres du constructeur.</span><span class="sxs-lookup"><span data-stu-id="c55dd-156">Services can't be obtained from constructor parameters.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="c55dd-157">Guide pratique pour configurer MVC et utiliser un formateur personnalisé</span><span class="sxs-lookup"><span data-stu-id="c55dd-157">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="c55dd-158">Pour utiliser un formateur personnalisé, ajoutez une instance de la classe du formateur à la collection `InputFormatters` ou `OutputFormatters`.</span><span class="sxs-lookup"><span data-stu-id="c55dd-158">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

<span data-ttu-id="c55dd-159">Les formateurs sont évalués dans l’ordre dans lequel vous les insérez.</span><span class="sxs-lookup"><span data-stu-id="c55dd-159">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="c55dd-160">Le premier est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="c55dd-160">The first one takes precedence.</span></span>

## <a name="the-complete-vcardinputformatter-class"></a><span data-ttu-id="c55dd-161">La `VcardInputFormatter` classe complète</span><span class="sxs-lookup"><span data-stu-id="c55dd-161">The complete `VcardInputFormatter` class</span></span>

<span data-ttu-id="c55dd-162">Le code suivant illustre la `VcardInputFormatter` classe de l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span><span class="sxs-lookup"><span data-stu-id="c55dd-162">The following code shows the `VcardInputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a><span data-ttu-id="c55dd-163">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="c55dd-163">Test the app</span></span>

<span data-ttu-id="c55dd-164">[Exécutez l’exemple d’application pour cet article](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), qui implémente des formateurs de sortie et d’entrée vCard de base.</span><span class="sxs-lookup"><span data-stu-id="c55dd-164">[Run the sample app for this article](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), which implements basic vCard input and output formatters.</span></span> <span data-ttu-id="c55dd-165">L’application lit et écrit les vCards semblables à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="c55dd-165">The app reads and writes vCards similar to the following:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

<span data-ttu-id="c55dd-166">Pour afficher la sortie vCard, exécutez l’application et envoyez une demande obtenir avec l’en-tête Accept `text/vcard` à `https://localhost:5001/api/contacts` .</span><span class="sxs-lookup"><span data-stu-id="c55dd-166">To see vCard output, run the app and send a Get request with Accept header `text/vcard` to `https://localhost:5001/api/contacts`.</span></span>

<span data-ttu-id="c55dd-167">Pour ajouter une vCard à la collection de contacts en mémoire :</span><span class="sxs-lookup"><span data-stu-id="c55dd-167">To add a vCard to the in-memory collection of contacts:</span></span>

* <span data-ttu-id="c55dd-168">Envoyez une `Post` demande à à l' `/api/contacts` aide d’un outil tel que poster.</span><span class="sxs-lookup"><span data-stu-id="c55dd-168">Send a `Post` request to `/api/contacts` with a tool like Postman.</span></span>
* <span data-ttu-id="c55dd-169">Attribuez à l’en-tête `Content-Type` la valeur `text/vcard`.</span><span class="sxs-lookup"><span data-stu-id="c55dd-169">Set the `Content-Type` header to `text/vcard`.</span></span>
* <span data-ttu-id="c55dd-170">Définissez `vCard` le texte dans le corps, mis en forme comme dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="c55dd-170">Set `vCard` text in the body, formatted like the preceding example.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c55dd-171">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c55dd-171">Additional resources</span></span>

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
