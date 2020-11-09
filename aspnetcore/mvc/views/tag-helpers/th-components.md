---
title: Tag Helper Components dans ASP.NET Core
author: scottaddie
description: Découvrez ce que sont les Tag Helper Components et apprenez à les utiliser dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
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
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 15bddd8ce18546bef7ee7e6ec2e32e369d0858a3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060558"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="9f4ca-103">Tag Helper Components dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9f4ca-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="9f4ca-104">Par [Scott Addie](https://twitter.com/Scott_Addie) et [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span><span class="sxs-lookup"><span data-stu-id="9f4ca-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="9f4ca-105">Un Tag Helper Component est un Tag Helper qui vous permet de modifier ou d’ajouter des éléments HTML de manière conditionnelle à partir du code côté serveur.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="9f4ca-106">Cette fonctionnalité est disponible dans ASP.NET Core 2.0 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="9f4ca-107">ASP.NET Core inclut deux Tag Helper Components intégrés : `head` et `body`.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="9f4ca-108">Ils sont situés dans l' <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> espace de noms et peuvent être utilisés à la fois dans MVC et dans les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="9f4ca-109">Les Tag Helper Components ne nécessitent pas d’être inscrits avec l’application dans *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9f4ca-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml* .</span></span>

<span data-ttu-id="9f4ca-110">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f4ca-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="9f4ca-111">Cas d'utilisation</span><span class="sxs-lookup"><span data-stu-id="9f4ca-111">Use cases</span></span>

<span data-ttu-id="9f4ca-112">Voici deux cas d’usage courants des Tag Helper Components :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="9f4ca-113">Injection d’un `<link>` dans le `<head>` .</span><span class="sxs-lookup"><span data-stu-id="9f4ca-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="9f4ca-114">Injection d’un `<script>` dans le `<body>` .</span><span class="sxs-lookup"><span data-stu-id="9f4ca-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="9f4ca-115">Les sections suivantes décrivent ces cas d’usage.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="9f4ca-116">Injection dans l’élément head HTML</span><span class="sxs-lookup"><span data-stu-id="9f4ca-116">Inject into HTML head element</span></span>

<span data-ttu-id="9f4ca-117">Dans l’élément `<head>` HTML, les fichiers CSS sont généralement importés avec l’élément `<link>` HTML.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="9f4ca-118">Le code suivant injecte un élément `<link>` dans l’élément `<head>` à l’aide du Tag Helper Component `head` :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="9f4ca-119">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-119">In the preceding code:</span></span>

* <span data-ttu-id="9f4ca-120">L'objet `AddressStyleTagHelperComponent` implémente l'objet <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="9f4ca-121">L’abstraction :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-121">The abstraction:</span></span>
  * <span data-ttu-id="9f4ca-122">Permet l’initialisation de la classe avec un <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="9f4ca-123">Permet l’utilisation des Tag Helper Components pour ajouter ou modifier des éléments HTML.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="9f4ca-124">La propriété <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> définit l’ordre dans lequel les Components sont rendus.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="9f4ca-125">`Order` est requis quand il y a plusieurs utilisations des Tag Helper Components dans une application.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="9f4ca-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compare la valeur de la propriété <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> du contexte d’exécution à `head`.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="9f4ca-127">Si le résultat de la comparaison est true, le contenu du champ `_style` est injecté dans l’élément `<head>` HTML.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="9f4ca-128">Injection dans l’élément body HTML</span><span class="sxs-lookup"><span data-stu-id="9f4ca-128">Inject into HTML body element</span></span>

<span data-ttu-id="9f4ca-129">Le Tag Helper Component `body` peut injecter un élément `<script>` dans l’élément `<body>`.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="9f4ca-130">Le code suivant illustre cette technique :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="9f4ca-131">Un fichier HTML distinct est utilisé pour stocker l’élément `<script>`.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="9f4ca-132">Le fichier HTML rend le code plus lisible et plus facile à tenir à jour.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="9f4ca-133">Le code précédent lit le contenu de *TagHelpers/Templates/AddressToolTipScript.html* et l’ajoute à la sortie du Tag Helper.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="9f4ca-134">Le fichier *AddressToolTipScript.html* inclut le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="9f4ca-135">Le code précédent lie un [widget tooltip amorçable](https://getbootstrap.com/docs/3.3/javascript/#tooltips) à tout élément `<address>` contenant un attribut `printable`.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="9f4ca-136">L’effet est visible quand l’utilisateur pointe sur l’élément avec la souris.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="9f4ca-137">Inscrire un Component</span><span class="sxs-lookup"><span data-stu-id="9f4ca-137">Register a Component</span></span>

<span data-ttu-id="9f4ca-138">Un Tag Helper Component doit être ajouté à la collection des Tag Helper Components de l’application.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="9f4ca-139">Cet ajout à la collection peut s’effectuer de trois façons :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-139">There are three ways to add to the collection:</span></span>

* [<span data-ttu-id="9f4ca-140">Inscription par le biais d’un conteneur de services</span><span class="sxs-lookup"><span data-stu-id="9f4ca-140">Registration via services container</span></span>](#registration-via-services-container)
* [<span data-ttu-id="9f4ca-141">Inscription par le biais du Razor fichier</span><span class="sxs-lookup"><span data-stu-id="9f4ca-141">Registration via Razor file</span></span>](#registration-via-razor-file)
* [<span data-ttu-id="9f4ca-142">Inscription à l’aide d’un modèle de page ou d’un contrôleur</span><span class="sxs-lookup"><span data-stu-id="9f4ca-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="9f4ca-143">Inscription par le biais d’un conteneur de services</span><span class="sxs-lookup"><span data-stu-id="9f4ca-143">Registration via services container</span></span>

<span data-ttu-id="9f4ca-144">Si la classe Tag Helper Component n’est pas managée avec <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, elle doit être inscrite avec le système [d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9f4ca-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="9f4ca-145">Le code `Startup.ConfigureServices` ci-dessous inscrit les classes `AddressStyleTagHelperComponent` et `AddressScriptTagHelperComponent` avec une [durée de vie transitoire (transient)](xref:fundamentals/dependency-injection#lifetime-and-registration-options) :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-no-locrazor-file"></a><span data-ttu-id="9f4ca-146">Inscription par le biais du Razor fichier</span><span class="sxs-lookup"><span data-stu-id="9f4ca-146">Registration via Razor file</span></span>

<span data-ttu-id="9f4ca-147">Si le composant tag Helper n’est pas inscrit auprès de DI, il peut être enregistré à partir d’une Razor page pages ou d’une vue Mvc.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-147">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="9f4ca-148">Cette technique est utilisée pour contrôler le balisage injecté et l’ordre d’exécution du composant à partir d’un Razor fichier.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-148">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="9f4ca-149">`ITagHelperComponentManager` est utilisé pour ajouter ou supprimer des Tag Helper Components dans l’application.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="9f4ca-150">Le code suivant illustre cette technique avec `AddressTagHelperComponent` :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="9f4ca-151">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-151">In the preceding code:</span></span>

* <span data-ttu-id="9f4ca-152">La directive `@inject` fournit une instance de `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="9f4ca-153">L’instance est assignée à une variable nommée `manager` pour l’accès en aval dans le Razor fichier.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-153">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="9f4ca-154">Une instance de `AddressTagHelperComponent` est ajoutée à la collection des Tag Helper Components de l’application.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="9f4ca-155">`AddressTagHelperComponent` est modifié pour contenir un constructeur qui accepte les paramètres `markup` et `order` :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="9f4ca-156">Voici comment le paramètre `markup` s’utilise dans `ProcessAsync` :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="9f4ca-157">Inscription à l’aide d’un modèle de page ou d’un contrôleur</span><span class="sxs-lookup"><span data-stu-id="9f4ca-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="9f4ca-158">Si le composant tag Helper n’est pas inscrit auprès de DI, il peut être inscrit à partir d’un Razor modèle de page pages ou d’un contrôleur Mvc.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-158">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="9f4ca-159">Cette technique est utile pour séparer la logique C# des Razor fichiers.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-159">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="9f4ca-160">L’injection de constructeur est utilisée pour accéder à une instance de `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="9f4ca-161">Le Tag Helper Component est ajouté à la collection des Tag Helper Components de l’instance.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="9f4ca-162">Le Razor modèle de page pages suivant illustre cette technique avec `AddressTagHelperComponent` :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-162">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="9f4ca-163">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-163">In the preceding code:</span></span>

* <span data-ttu-id="9f4ca-164">L’injection de constructeur est utilisée pour accéder à une instance de `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="9f4ca-165">Une instance de `AddressTagHelperComponent` est ajoutée à la collection des Tag Helper Components de l’application.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="9f4ca-166">Créer un Component</span><span class="sxs-lookup"><span data-stu-id="9f4ca-166">Create a Component</span></span>

<span data-ttu-id="9f4ca-167">Pour créer un Tag Helper Component personnalisé :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="9f4ca-168">Créez une classe publique dérivée de <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="9f4ca-169">Appliquez un [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribut à la classe.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-169">Apply an [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="9f4ca-170">Spécifiez le nom de l’élément HTML cible.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="9f4ca-171">*Facultatif* : appliquez un [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) attribut à la classe pour supprimer l’affichage du type dans IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-171">*Optional* : Apply an [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="9f4ca-172">Le code suivant crée un Tag Helper Component personnalisé qui cible l’élément HTML `<address>` :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="9f4ca-173">Utilisez le Tag Helper Component `address` personnalisé pour injecter le balisage HTML comme ceci :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

<span data-ttu-id="9f4ca-174">La méthode `ProcessAsync` précédente injecte le code HTML fourni à <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> dans l’élément `<address>` correspondant.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="9f4ca-175">L’injection se produit quand :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-175">The injection occurs when:</span></span>

* <span data-ttu-id="9f4ca-176">La propriété `TagName` du contexte d’exécution a la valeur `address`.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="9f4ca-177">L’élément `<address>` correspondant a un attribut `printable`.</span><span class="sxs-lookup"><span data-stu-id="9f4ca-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="9f4ca-178">Par exemple, l’instruction `if` a la valeur true quand l’élément `<address>` suivant est exécuté :</span><span class="sxs-lookup"><span data-stu-id="9f4ca-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="9f4ca-179">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9f4ca-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
