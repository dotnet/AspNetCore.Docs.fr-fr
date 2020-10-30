---
title: Tag Helper Partial dans ASP.NET Core
author: scottaddie
description: Découvrez le Tag Helper Partial ASP.NET Core et le rôle de ses attributs dans le rendu d’une vue partielle.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 124f23caa4a757f63a80dfea627304204ba2cdca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061429"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="0d2cd-103">Tag Helper Partial dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d2cd-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="0d2cd-104">Par [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="0d2cd-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="0d2cd-105">Pour avoir une vue d’ensemble de Tag Helpers, consultez <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="0d2cd-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0d2cd-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="0d2cd-107">Vue d'ensemble</span><span class="sxs-lookup"><span data-stu-id="0d2cd-107">Overview</span></span>

<span data-ttu-id="0d2cd-108">Le tag Helper partiel est utilisé pour restituer une [vue partielle](xref:mvc/views/partial) dans :::no-loc(Razor)::: les pages et les applications MVC.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in :::no-loc(Razor)::: Pages and MVC apps.</span></span> <span data-ttu-id="0d2cd-109">Tenez compte des points suivants :</span><span class="sxs-lookup"><span data-stu-id="0d2cd-109">Consider that it:</span></span>

* <span data-ttu-id="0d2cd-110">Il nécessite ASP.NET Core 2.1 ou ultérieur.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="0d2cd-111">Il constitue une alternative à la [syntaxe HTML Helper](xref:mvc/views/partial#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="0d2cd-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="0d2cd-112">Il affiche la vue partielle de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="0d2cd-113">Parmi les options HTML Helper utilisées pour le rendu d’une vue partielle, citons les suivantes :</span><span class="sxs-lookup"><span data-stu-id="0d2cd-113">The HTML Helper options for rendering a partial view include:</span></span>

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="0d2cd-114">Le modèle *Product* est utilisé dans les exemples tout au long de ce document :</span><span class="sxs-lookup"><span data-stu-id="0d2cd-114">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="0d2cd-115">Voici l’inventaire des attributs du Tag Helper Partial.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-115">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="0d2cd-116">name</span><span class="sxs-lookup"><span data-stu-id="0d2cd-116">name</span></span>

<span data-ttu-id="0d2cd-117">L'attribut `name` est obligatoire.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-117">The `name` attribute is required.</span></span> <span data-ttu-id="0d2cd-118">Il indique le nom ou le chemin de la vue partielle à afficher.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-118">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="0d2cd-119">Quand un nom de vue partielle est fourni, le processus de [découverte de vue](xref:mvc/views/overview#view-discovery) est lancé.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-119">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="0d2cd-120">Ce processus est ignoré quand un chemin explicite est fourni.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-120">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="0d2cd-121">Pour connaître toutes les valeurs `name` acceptables, consultez [Découverte des vues partielles](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="0d2cd-121">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="0d2cd-122">Le balisage suivant utilise un chemin explicite indiquant que le fichier *_ProductPartial.cshtml* doit être chargé à partir du dossier *Shared* .</span><span class="sxs-lookup"><span data-stu-id="0d2cd-122">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="0d2cd-123">À l’aide de l’attribut [for](#for), un modèle est passé à la vue partielle pour liaison.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-123">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="0d2cd-124">pour</span><span class="sxs-lookup"><span data-stu-id="0d2cd-124">for</span></span>

<span data-ttu-id="0d2cd-125">L’attribut `for` affecte un [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) à évaluer par rapport au modèle actif.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-125">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="0d2cd-126">`ModelExpression` déduit la syntaxe `@Model.`.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-126">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="0d2cd-127">Par exemple, `for="Product"` peut être utilisé à la place de `for="@Model.Product"`.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-127">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="0d2cd-128">Pour substituer ce comportement d’inférence par défaut, utilisez le symbole `@` pour définir une expression inline.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-128">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="0d2cd-129">Le balisage suivant charge *_ProductPartial.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0d2cd-129">The following markup loads *_ProductPartial.cshtml* :</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="0d2cd-130">La vue partielle est liée à la propriété `Product` du modèle de page associé :</span><span class="sxs-lookup"><span data-stu-id="0d2cd-130">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="0d2cd-131">model</span><span class="sxs-lookup"><span data-stu-id="0d2cd-131">model</span></span>

<span data-ttu-id="0d2cd-132">L’attribut `model` affecte une instance de modèle à passer à la vue partielle.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-132">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="0d2cd-133">L’attribut `model` ne peut pas être utilisé avec l’attribut [for](#for).</span><span class="sxs-lookup"><span data-stu-id="0d2cd-133">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="0d2cd-134">Dans le balisage suivant, un nouvel objet `Product` est instancié et passé à l’attribut `model` pour liaison :</span><span class="sxs-lookup"><span data-stu-id="0d2cd-134">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="0d2cd-135">view-data</span><span class="sxs-lookup"><span data-stu-id="0d2cd-135">view-data</span></span>

<span data-ttu-id="0d2cd-136">L’attribut `view-data` affecte un [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) à passer à la vue partielle.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-136">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="0d2cd-137">Le balisage suivant rend l’ensemble de la collection ViewData accessible à la vue partielle :</span><span class="sxs-lookup"><span data-stu-id="0d2cd-137">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="0d2cd-138">Dans le code précédent, la valeur de clé `IsNumberReadOnly` est `true` et ajoutée à la collection ViewData.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-138">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="0d2cd-139">`ViewData["IsNumberReadOnly"]` est donc accessible au sein de la vue partielle suivante :</span><span class="sxs-lookup"><span data-stu-id="0d2cd-139">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="0d2cd-140">Dans cet exemple, la valeur de `ViewData["IsNumberReadOnly"]` détermine si le champ *Number* s’affiche en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-140">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="0d2cd-141">Migrer à partir d’une assistance HTML</span><span class="sxs-lookup"><span data-stu-id="0d2cd-141">Migrate from an HTML Helper</span></span>

<span data-ttu-id="0d2cd-142">Prenons l’exemple d’assistance HTML asynchrone suivante.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-142">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="0d2cd-143">Une collection de produits est parcourue et affichée.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-143">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="0d2cd-144">Conformément au premier paramètre de la méthode `PartialAsync`, la vue partielle *_ProductPartial.cshtml* est chargée.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-144">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="0d2cd-145">Une instance du modèle `Product` est passée à la vue partielle pour la liaison.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-145">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="0d2cd-146">Le Tag Helper Partial suivant permet d’obtenir le même comportement de rendu asynchrone que l’assistance HTML `PartialAsync`.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-146">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="0d2cd-147">Une instance de modèle `Product` est assignée à l’attribut `model` pour la liaison à la vue partielle.</span><span class="sxs-lookup"><span data-stu-id="0d2cd-147">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="0d2cd-148">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0d2cd-148">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
