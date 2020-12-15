---
title: Partie 3, pages de génération de modèles automatique Razor
author: rick-anderson
description: Partie 3 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.date: 09/25/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: a6efbb22f8b6280bd636cd1575d8a4a2bca0bb06
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486172"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="ea12b-103">Partie 3, Razor pages de génération de modèles automatique dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ea12b-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="ea12b-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ea12b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ea12b-105">Ce didacticiel examine les Razor pages créées par génération de modèles automatique dans le [didacticiel précédent](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="ea12b-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ea12b-106">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ea12b-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="ea12b-107">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ea12b-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="ea12b-108">Pages Create, Delete, Details et Edit</span><span class="sxs-lookup"><span data-stu-id="ea12b-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="ea12b-109">Examinez le modèle de page *pages/movies/ Index . cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="ea12b-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="ea12b-110">Razor Les pages sont dérivées de `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="ea12b-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="ea12b-111">Par Convention, la `PageModel` classe dérivée de est nommée `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="ea12b-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="ea12b-112">Le constructeur utilise l' [injection de dépendances](xref:fundamentals/dependency-injection) pour ajouter `RazorPagesMovieContext` à la page :</span><span class="sxs-lookup"><span data-stu-id="ea12b-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="ea12b-113">Consultez [Code asynchrone](xref:data/ef-rp/intro#asynchronous-code) pour plus d’informations sur la programmation asynchrone avec Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="ea12b-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="ea12b-114">Quand une demande est effectuée pour la page, la `OnGetAsync` méthode retourne une liste de films à la Razor page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="ea12b-115">Sur une Razor page, `OnGetAsync` ou `OnGet` est appelé pour initialiser l’état de la page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="ea12b-116">Dans ce cas, `OnGetAsync` obtient une liste de films et les affiche.</span><span class="sxs-lookup"><span data-stu-id="ea12b-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="ea12b-117">Lorsque `OnGet` retourne `void` ou `OnGetAsync` retourne une valeur `Task` , aucune instruction return n’est utilisée.</span><span class="sxs-lookup"><span data-stu-id="ea12b-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="ea12b-118">Par exemple, la page confidentialité :</span><span class="sxs-lookup"><span data-stu-id="ea12b-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="ea12b-119">Lorsque le type de retour est `IActionResult` ou `Task<IActionResult>`, une instruction de retour doit être spécifiée.</span><span class="sxs-lookup"><span data-stu-id="ea12b-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="ea12b-120">Par exemple, la méthode *Pages/Movies/Create.cshtml.cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="ea12b-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="ea12b-121">Examinez la page *pages/movies/ Index . cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="ea12b-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="ea12b-122">Razor peut passer du code HTML au langage C# ou à Razor un balisage spécifique.</span><span class="sxs-lookup"><span data-stu-id="ea12b-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="ea12b-123">Quand un `@` symbole est suivi d’un [ Razor mot clé réservé](xref:mvc/views/razor#razor-reserved-keywords), il passe dans le Razor balisage spécifique, sinon il passe en C#.</span><span class="sxs-lookup"><span data-stu-id="ea12b-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="ea12b-124">Directive @page</span><span class="sxs-lookup"><span data-stu-id="ea12b-124">The @page directive</span></span>

<span data-ttu-id="ea12b-125">La `@page` Razor directive fait du fichier une action MVC, ce qui signifie qu’elle peut gérer les demandes.</span><span class="sxs-lookup"><span data-stu-id="ea12b-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="ea12b-126">`@page` doit être la première Razor directive sur une page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="ea12b-127">`@page` et `@model` sont des exemples de transition vers Razor un balisage spécifique.</span><span class="sxs-lookup"><span data-stu-id="ea12b-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="ea12b-128">Pour plus d’informations, consultez [ Razor syntaxe](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="ea12b-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="ea12b-129">Directive @model</span><span class="sxs-lookup"><span data-stu-id="ea12b-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="ea12b-130">La `@model` directive spécifie le type du modèle passé à la Razor page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="ea12b-131">Dans l’exemple précédent, la `@model` ligne rend la `PageModel` classe dérivée de disponible sur la Razor page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="ea12b-132">Le modèle est utilisé dans les  [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` et `@Html.DisplayFor` de la page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="ea12b-133">Examinez l’expression lambda utilisée dans le HTML Helper suivant :</span><span class="sxs-lookup"><span data-stu-id="ea12b-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="ea12b-134">Le HTML Helper <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> inspecte la propriété `Title` référencée dans l’expression lambda pour déterminer le nom d’affichage.</span><span class="sxs-lookup"><span data-stu-id="ea12b-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="ea12b-135">L’expression lambda est inspectée plutôt qu’évaluée.</span><span class="sxs-lookup"><span data-stu-id="ea12b-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="ea12b-136">Cela signifie qu’il n’y a aucune violation d’accès quand `model` , `model.Movie` ou `model.Movie[0]` est `null` ou est vide.</span><span class="sxs-lookup"><span data-stu-id="ea12b-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="ea12b-137">Quand l’expression lambda est évaluée, par exemple avec `@Html.DisplayFor(modelItem => item.Title)` , les valeurs de propriété du modèle sont évaluées.</span><span class="sxs-lookup"><span data-stu-id="ea12b-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="ea12b-138">La page de disposition</span><span class="sxs-lookup"><span data-stu-id="ea12b-138">The layout page</span></span>

<span data-ttu-id="ea12b-139">Sélectionnez le menu liens **Razor PagesMovie**, **orig** et **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="ea12b-139">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="ea12b-140">Chaque page affiche la même disposition de menu.</span><span class="sxs-lookup"><span data-stu-id="ea12b-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="ea12b-141">La disposition du menu est implémentée dans le fichier *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea12b-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="ea12b-142">Ouvrez et examinez le fichier *pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ea12b-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="ea12b-143">Les modèles de [Disposition](xref:mvc/views/layout) permettent que la disposition du conteneur HTML soit :</span><span class="sxs-lookup"><span data-stu-id="ea12b-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="ea12b-144">spécifiée à un seul endroit ;</span><span class="sxs-lookup"><span data-stu-id="ea12b-144">Specified in one place.</span></span>
* <span data-ttu-id="ea12b-145">appliquée à plusieurs pages du site.</span><span class="sxs-lookup"><span data-stu-id="ea12b-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="ea12b-146">Recherchez la ligne `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="ea12b-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="ea12b-147">`RenderBody` est un espace réservé dans lequel toutes les vues propres à la page s’affichent, *encapsulées* dans la page de disposition.</span><span class="sxs-lookup"><span data-stu-id="ea12b-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="ea12b-148">Par exemple, sélectionnez le lien **Confidentialité** et la vue *Pages/Privacy.cshtml* est restituée dans la méthode `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="ea12b-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="ea12b-149">ViewData et disposition</span><span class="sxs-lookup"><span data-stu-id="ea12b-149">ViewData and layout</span></span>

<span data-ttu-id="ea12b-150">Examinez le balisage suivant à partir du fichier *pages/movies/ Index . cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ea12b-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="ea12b-151">Le balisage en surbrillance précédent est un exemple de Razor transition en C#.</span><span class="sxs-lookup"><span data-stu-id="ea12b-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="ea12b-152">Les caractères `{` et `}` délimitent un bloc de code C#.</span><span class="sxs-lookup"><span data-stu-id="ea12b-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="ea12b-153">La `PageModel` classe de base contient une `ViewData` propriété de dictionnaire qui peut être utilisée pour passer des données à une vue.</span><span class="sxs-lookup"><span data-stu-id="ea12b-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="ea12b-154">Les objets sont ajoutés au `ViewData` dictionnaire à l’aide d’un modèle de \*\*valeur de clé\*\*\*.</span><span class="sxs-lookup"><span data-stu-id="ea12b-154">Objects are added to the `ViewData` dictionary using a \***key value** _ pattern.</span></span> <span data-ttu-id="ea12b-155">Dans l’exemple précédent, la propriété `Title` est ajoutée au dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="ea12b-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="ea12b-156">La `Title` propriété est utilisée dans le fichier _Pages/shared/_Layout. cshtml \*.</span><span class="sxs-lookup"><span data-stu-id="ea12b-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="ea12b-157">La balise suivante montre les premières lignes du fichier *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea12b-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="ea12b-158">La ligne `@*Markup removed for brevity.*@` est un Razor commentaire.</span><span class="sxs-lookup"><span data-stu-id="ea12b-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="ea12b-159">Contrairement aux commentaires HTML `<!-- -->` , Razor les commentaires ne sont pas envoyés au client.</span><span class="sxs-lookup"><span data-stu-id="ea12b-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="ea12b-160">Pour plus d’informations, consultez la page [documentation Web MDN : prise en main du code html](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) .</span><span class="sxs-lookup"><span data-stu-id="ea12b-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="ea12b-161">Mettre à jour la disposition</span><span class="sxs-lookup"><span data-stu-id="ea12b-161">Update the layout</span></span>

1. <span data-ttu-id="ea12b-162">Modifiez l' `<title>` élément dans le fichier *pages/Shared/_Layout. cshtml* pour afficher **Movie** plutôt que **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="ea12b-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="ea12b-163">Recherchez l’élément anchor suivant dans le fichier *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea12b-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="ea12b-164">Remplacez l’élément précédent par la balise suivante :</span><span class="sxs-lookup"><span data-stu-id="ea12b-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="ea12b-165">L’élément anchor précédent est un [Tag Helper](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ea12b-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="ea12b-166">Dans le cas présent, il s’agit du [Tag Helper d’ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ea12b-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="ea12b-167">L' `asp-page="/Movies/Index"` attribut et la valeur tag Helper crée un lien vers la `/Movies/Index` Razor page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="ea12b-168">La valeur de l’attribut `asp-area` est vide : la zone n’est donc pas utilisée dans le lien.</span><span class="sxs-lookup"><span data-stu-id="ea12b-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="ea12b-169">Pour plus d’informations, consultez [Zones](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="ea12b-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="ea12b-170">Enregistrez les modifications et testez l’application en sélectionnant le lien **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="ea12b-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="ea12b-171">Consultez le fichier [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) dans GitHub si vous rencontrez des problèmes.</span><span class="sxs-lookup"><span data-stu-id="ea12b-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="ea12b-172">Testez les liens **démarrage**, **RpMovie**, **créer**, **modifier** et **supprimer** .</span><span class="sxs-lookup"><span data-stu-id="ea12b-172">Test the **Home**, **RpMovie**, **Create**, **Edit**, and **Delete** links.</span></span> <span data-ttu-id="ea12b-173">Chaque page définit le titre, que vous pouvez voir dans l’onglet navigateur. Lorsque vous ajoutez une page à un signet, le titre est utilisé pour le signet.</span><span class="sxs-lookup"><span data-stu-id="ea12b-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="ea12b-174">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="ea12b-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="ea12b-175">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que l’anglais qui utilisent une virgule (",") pour une virgule décimale, et les formats de date non US-English, vous devez prendre des mesures pour globaliser l’application.</span><span class="sxs-lookup"><span data-stu-id="ea12b-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="ea12b-176">Consultez cette page [GitHub problème 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) pour savoir comment ajouter une virgule décimale.</span><span class="sxs-lookup"><span data-stu-id="ea12b-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="ea12b-177">La propriété `Layout` est définie dans le fichier *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ea12b-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="ea12b-178">Le balisage précédent définit le fichier de disposition sur *pages/Shared/_Layout. cshtml* pour tous les Razor fichiers sous le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="ea12b-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="ea12b-179">Pour plus d’informations, consultez [Disposition](xref:razor-pages/index#layout).</span><span class="sxs-lookup"><span data-stu-id="ea12b-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="ea12b-180">Le modèle de page Create</span><span class="sxs-lookup"><span data-stu-id="ea12b-180">The Create page model</span></span>

<span data-ttu-id="ea12b-181">Examinez le modèle de page *Pages/Movies/Create.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="ea12b-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="ea12b-182">La méthode `OnGet` initialise l’état nécessaire pour la page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="ea12b-183">La page Create n’ayant aucun état à initialiser, `Page` est retourné.</span><span class="sxs-lookup"><span data-stu-id="ea12b-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="ea12b-184">Plus loin dans le tutoriel, un exemple d’état d’initialisation `OnGet` est affiché.</span><span class="sxs-lookup"><span data-stu-id="ea12b-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="ea12b-185">La méthode `Page` crée un objet `PageResult` qui affiche la page *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea12b-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="ea12b-186">La `Movie` propriété utilise l’attribut [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) pour s’abonner à la [liaison de modèle](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="ea12b-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="ea12b-187">Quand le formulaire Create publie les valeurs de formulaire, le runtime ASP.NET Core lie les valeurs publiées au modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="ea12b-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="ea12b-188">La méthode `OnPostAsync` est exécutée quand la page publie les données de formulaire :</span><span class="sxs-lookup"><span data-stu-id="ea12b-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="ea12b-189">S’il existe des erreurs liées au modèle, le formulaire est réaffiché, ainsi que toutes les données de formulaire publiées.</span><span class="sxs-lookup"><span data-stu-id="ea12b-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="ea12b-190">La plupart des erreurs de modèle peuvent être interceptées côté client avant la publication du formulaire.</span><span class="sxs-lookup"><span data-stu-id="ea12b-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="ea12b-191">Voici un exemple d’erreur de modèle : la publication pour le champ de date d’une valeur qui ne peut pas être convertie en date.</span><span class="sxs-lookup"><span data-stu-id="ea12b-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="ea12b-192">La validation côté client et la validation du modèle sont présentées plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="ea12b-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="ea12b-193">S’il n’existe aucune erreur de modèle :</span><span class="sxs-lookup"><span data-stu-id="ea12b-193">If there are no model errors:</span></span>

* <span data-ttu-id="ea12b-194">Les données sont enregistrées.</span><span class="sxs-lookup"><span data-stu-id="ea12b-194">The data is saved.</span></span>
* <span data-ttu-id="ea12b-195">Le navigateur est redirigé vers la Index page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-195">The browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="ea12b-196">La Razor page créer</span><span class="sxs-lookup"><span data-stu-id="ea12b-196">The Create Razor Page</span></span>

<span data-ttu-id="ea12b-197">Examinez le fichier de page *pages/movies/Create. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="ea12b-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="ea12b-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea12b-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ea12b-199">Visual Studio affiche la balise suivante dans une police différenciée en gras utilisée pour les Tag Helpers :</span><span class="sxs-lookup"><span data-stu-id="ea12b-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Vue VS17 de la page Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ea12b-201">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="ea12b-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ea12b-202">Les Tag Helpers suivants sont affichées dans la balise précédente :</span><span class="sxs-lookup"><span data-stu-id="ea12b-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="ea12b-203">L’élément `<form method="post">` est un [Tag Helper de formulaire](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ea12b-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="ea12b-204">Le Tag Helper de formulaire inclut automatiquement un [jeton de protection contre les falsifications](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="ea12b-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="ea12b-205">Le moteur de génération de modèles automatique crée un Razor balisage pour chaque champ du modèle, à l’exception de l’ID, semblable à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="ea12b-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="ea12b-206">Les [tag helpers de validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` et `<span asp-validation-for` ) affichent des erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="ea12b-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="ea12b-207">La validation est traitée de manière plus détaillée plus loin dans cette série.</span><span class="sxs-lookup"><span data-stu-id="ea12b-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="ea12b-208">Le [tag Helper étiquette](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) génère la légende et l' `[for]` attribut de l’étiquette pour la `Title` propriété.</span><span class="sxs-lookup"><span data-stu-id="ea12b-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="ea12b-209">Le [tag Helper d’entrée](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) utilise les attributs [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) et produit les attributs HTML nécessaires à la validation jQuery côté client.</span><span class="sxs-lookup"><span data-stu-id="ea12b-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="ea12b-210">Pour plus d’informations sur les Tag Helpers, comme `<form method="post">`, consultez [Tag Helpers dans ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ea12b-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ea12b-211">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ea12b-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ea12b-212">[Précédent : ajouter un modèle](xref:tutorials/razor-pages/model) 
>  [Suivant : utiliser une base de données](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="ea12b-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="ea12b-213">Pages Create, Delete, Details et Edit</span><span class="sxs-lookup"><span data-stu-id="ea12b-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="ea12b-214">Examinez le modèle de page *pages/movies/ Index . cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="ea12b-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="ea12b-215">Razor Les pages sont dérivées de `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="ea12b-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="ea12b-216">Par Convention, la `PageModel` classe dérivée de est nommée `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="ea12b-216">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="ea12b-217">Le constructeur utilise l’[injection de dépendances](xref:fundamentals/dependency-injection) pour ajouter `RazorPagesMovieContext` à la page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="ea12b-218">Les pages de génération de modèles automatique suivent ce modèle.</span><span class="sxs-lookup"><span data-stu-id="ea12b-218">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="ea12b-219">Consultez [Code asynchrone](xref:data/ef-rp/intro#asynchronous-code) pour plus d’informations sur la programmation asynchrone avec Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="ea12b-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="ea12b-220">Quand une demande est effectuée pour la page, la `OnGetAsync` méthode retourne une liste de films à la Razor page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="ea12b-221">`OnGetAsync` ou `OnGet` est appelé sur une Razor page pour initialiser l’état de la page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="ea12b-222">Dans ce cas, `OnGetAsync` obtient une liste de films et les affiche.</span><span class="sxs-lookup"><span data-stu-id="ea12b-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="ea12b-223">Lorsque `OnGet` retourne `void` ou `OnGetAsync` retourne une valeur `Task` , aucune méthode de retour n’est utilisée.</span><span class="sxs-lookup"><span data-stu-id="ea12b-223">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="ea12b-224">Lorsque le type de retour est `IActionResult` ou `Task<IActionResult>`, une instruction de retour doit être spécifiée.</span><span class="sxs-lookup"><span data-stu-id="ea12b-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="ea12b-225">Par exemple, la méthode *Pages/Movies/Create.cshtml.cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="ea12b-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="ea12b-226">Examinez la page *pages/movies/ Index . cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="ea12b-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="ea12b-227">Razor peut passer du code HTML au langage C# ou à Razor un balisage spécifique.</span><span class="sxs-lookup"><span data-stu-id="ea12b-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="ea12b-228">Quand un `@` symbole est suivi d’un [ Razor mot clé réservé](xref:mvc/views/razor#razor-reserved-keywords), il passe dans le Razor balisage spécifique, sinon il passe en C#.</span><span class="sxs-lookup"><span data-stu-id="ea12b-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="ea12b-229">La `@page` Razor directive convertit le fichier en action MVC, ce qui signifie qu’il peut gérer les demandes.</span><span class="sxs-lookup"><span data-stu-id="ea12b-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="ea12b-230">`@page` doit être la première Razor directive sur une page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="ea12b-231">`@page` est un exemple de transition vers un Razor balisage spécifique.</span><span class="sxs-lookup"><span data-stu-id="ea12b-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="ea12b-232">Pour plus d’informations, consultez [ Razor syntaxe](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="ea12b-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="ea12b-233">Directive @model</span><span class="sxs-lookup"><span data-stu-id="ea12b-233">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="ea12b-234">La `@model` directive spécifie le type du modèle passé à la Razor page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-234">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="ea12b-235">Dans l’exemple précédent, la `@model` ligne rend la `PageModel` classe dérivée de disponible sur la Razor page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-235">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="ea12b-236">Le modèle est utilisé dans les  [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` et `@Html.DisplayFor` de la page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-236">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="ea12b-237">Applications auxiliaires HTML</span><span class="sxs-lookup"><span data-stu-id="ea12b-237">HTML Helpers</span></span>

<span data-ttu-id="ea12b-238">Examinez l’expression lambda utilisée dans le HTML Helper suivant :</span><span class="sxs-lookup"><span data-stu-id="ea12b-238">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="ea12b-239">Le HTML Helper <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> inspecte la propriété `Title` référencée dans l’expression lambda pour déterminer le nom d’affichage.</span><span class="sxs-lookup"><span data-stu-id="ea12b-239">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="ea12b-240">L’expression lambda est inspectée plutôt qu’évaluée.</span><span class="sxs-lookup"><span data-stu-id="ea12b-240">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="ea12b-241">Cela signifie qu’il n’existe pas de violation d’accès quand `model`, `model.Movie` ou `model.Movie[0]` ont une valeur `null` ou vide.</span><span class="sxs-lookup"><span data-stu-id="ea12b-241">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="ea12b-242">Quand l’expression lambda est évaluée, par exemple avec `@Html.DisplayFor(modelItem => item.Title)` , les valeurs de propriété du modèle sont évaluées.</span><span class="sxs-lookup"><span data-stu-id="ea12b-242">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="ea12b-243">La page de disposition</span><span class="sxs-lookup"><span data-stu-id="ea12b-243">The layout page</span></span>

<span data-ttu-id="ea12b-244">Sélectionnez le menu liens **Razor PagesMovie**, **orig** et **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="ea12b-244">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="ea12b-245">Chaque page affiche la même disposition de menu.</span><span class="sxs-lookup"><span data-stu-id="ea12b-245">Each page shows the same menu layout.</span></span> <span data-ttu-id="ea12b-246">La disposition du menu est implémentée dans le fichier *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea12b-246">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="ea12b-247">Ouvrez et examinez le fichier *pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ea12b-247">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="ea12b-248">Les modèles de [disposition](xref:mvc/views/layout) vous permettent de spécifier la disposition de conteneur HTML d’un site à un emplacement, puis de l’appliquer sur plusieurs pages du site.</span><span class="sxs-lookup"><span data-stu-id="ea12b-248">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="ea12b-249">Recherchez la ligne `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="ea12b-249">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="ea12b-250">`RenderBody` est un espace réservé dans lequel tous les affichages spécifiques à la page que vous créez s’affichent, *inclus* dans la page de disposition.</span><span class="sxs-lookup"><span data-stu-id="ea12b-250">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="ea12b-251">Par exemple, si vous sélectionnez le lien **Confidentialité**, la vue **Pages/Privacy.cshtml** est restituée dans la méthode `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="ea12b-251">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="ea12b-252">ViewData et disposition</span><span class="sxs-lookup"><span data-stu-id="ea12b-252">ViewData and layout</span></span>

<span data-ttu-id="ea12b-253">Considérez le code suivant dans le fichier *pages/movies/ Index . cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ea12b-253">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="ea12b-254">Le code en surbrillance précédent est un exemple de Razor transition en C#.</span><span class="sxs-lookup"><span data-stu-id="ea12b-254">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="ea12b-255">Les caractères `{` et `}` délimitent un bloc de code C#.</span><span class="sxs-lookup"><span data-stu-id="ea12b-255">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="ea12b-256">La classe de base `PageModel` a une propriété de dictionnaire `ViewData` qui permet d’ajouter des données à passer à une vue.</span><span class="sxs-lookup"><span data-stu-id="ea12b-256">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="ea12b-257">Vous pouvez ajouter des objets au dictionnaire `ViewData` à l’aide d’un modèle clé/valeur.</span><span class="sxs-lookup"><span data-stu-id="ea12b-257">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="ea12b-258">Dans l’exemple précédent, la propriété `Title` est ajoutée au dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="ea12b-258">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="ea12b-259">La propriété `Title` est utilisée dans le fichier *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea12b-259">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="ea12b-260">La balise suivante montre les premières lignes du fichier *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea12b-260">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="ea12b-261">La ligne `@*Markup removed for brevity.*@` est un Razor commentaire.</span><span class="sxs-lookup"><span data-stu-id="ea12b-261">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="ea12b-262">Contrairement aux commentaires HTML `<!-- -->` , Razor les commentaires ne sont pas envoyés au client.</span><span class="sxs-lookup"><span data-stu-id="ea12b-262">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="ea12b-263">Pour plus d’informations, consultez la page [documentation Web MDN : prise en main du code html](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) .</span><span class="sxs-lookup"><span data-stu-id="ea12b-263">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="ea12b-264">Mettre à jour la disposition</span><span class="sxs-lookup"><span data-stu-id="ea12b-264">Update the layout</span></span>

<span data-ttu-id="ea12b-265">Modifiez l' `<title>` élément dans le fichier *pages/Shared/_Layout. cshtml* pour afficher **Movie** plutôt que **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="ea12b-265">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="ea12b-266">Recherchez l’élément anchor suivant dans le fichier *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea12b-266">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="ea12b-267">Remplacez l’élément précédent par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="ea12b-267">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="ea12b-268">L’élément anchor précédent est un [Tag Helper](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ea12b-268">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="ea12b-269">Dans le cas présent, il s’agit du [Tag Helper d’ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ea12b-269">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="ea12b-270">L' `asp-page="/Movies/Index"` attribut et la valeur tag Helper crée un lien vers la `/Movies/Index` Razor page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-270">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="ea12b-271">La valeur de l’attribut `asp-area` est vide : la zone n’est donc pas utilisée dans le lien.</span><span class="sxs-lookup"><span data-stu-id="ea12b-271">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="ea12b-272">Pour plus d’informations, consultez [Zones](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="ea12b-272">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="ea12b-273">Enregistrez vos changements, puis testez l’application en cliquant sur le lien **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="ea12b-273">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="ea12b-274">Consultez le fichier [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) dans GitHub si vous rencontrez des problèmes.</span><span class="sxs-lookup"><span data-stu-id="ea12b-274">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="ea12b-275">Testez les autres liens (**Home**, **RpMovie**, **Create**, **Edit** et **Delete**).</span><span class="sxs-lookup"><span data-stu-id="ea12b-275">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="ea12b-276">Chaque page définit le titre, que vous pouvez voir dans l’onglet navigateur. Lorsque vous ajoutez une page à un signet, le titre est utilisé pour le signet.</span><span class="sxs-lookup"><span data-stu-id="ea12b-276">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="ea12b-277">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="ea12b-277">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="ea12b-278">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que l’anglais qui utilisent une virgule (",") pour une virgule décimale, et les formats de date non US-English, vous devez prendre des mesures pour globaliser l’application.</span><span class="sxs-lookup"><span data-stu-id="ea12b-278">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="ea12b-279">Consultez la page [GitHub problème 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) pour savoir comment ajouter une virgule décimale.</span><span class="sxs-lookup"><span data-stu-id="ea12b-279">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="ea12b-280">La propriété `Layout` est définie dans le fichier *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ea12b-280">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="ea12b-281">Le balisage précédent définit le fichier de disposition sur *pages/Shared/_Layout. cshtml* pour tous les Razor fichiers sous le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="ea12b-281">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="ea12b-282">Pour plus d’informations, consultez [Disposition](xref:razor-pages/index#layout).</span><span class="sxs-lookup"><span data-stu-id="ea12b-282">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="ea12b-283">Le modèle de page Create</span><span class="sxs-lookup"><span data-stu-id="ea12b-283">The Create page model</span></span>

<span data-ttu-id="ea12b-284">Examinez le modèle de page *Pages/Movies/Create.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="ea12b-284">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="ea12b-285">La méthode `OnGet` initialise l’état nécessaire pour la page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-285">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="ea12b-286">La page Create n’ayant aucun état à initialiser, `Page` est retourné.</span><span class="sxs-lookup"><span data-stu-id="ea12b-286">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="ea12b-287">Ce tutoriel illustre plus loin l’initialisation de l’état par la méthode `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="ea12b-287">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="ea12b-288">La méthode `Page` crée un objet `PageResult` qui affiche la page *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea12b-288">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="ea12b-289">La `Movie` propriété utilise l’attribut [[BindProperty]] <xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> pour s’abonner à la [liaison de modèle](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="ea12b-289">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="ea12b-290">Quand le formulaire Create publie les valeurs de formulaire, le runtime ASP.NET Core lie les valeurs publiées au modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="ea12b-290">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="ea12b-291">La méthode `OnPostAsync` est exécutée quand la page publie les données de formulaire :</span><span class="sxs-lookup"><span data-stu-id="ea12b-291">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="ea12b-292">S’il existe des erreurs liées au modèle, le formulaire est réaffiché, ainsi que toutes les données de formulaire publiées.</span><span class="sxs-lookup"><span data-stu-id="ea12b-292">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="ea12b-293">La plupart des erreurs de modèle peuvent être interceptées côté client avant la publication du formulaire.</span><span class="sxs-lookup"><span data-stu-id="ea12b-293">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="ea12b-294">Voici un exemple d’erreur de modèle : la publication pour le champ de date d’une valeur qui ne peut pas être convertie en date.</span><span class="sxs-lookup"><span data-stu-id="ea12b-294">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="ea12b-295">La validation côté client et la validation du modèle sont présentées plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="ea12b-295">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="ea12b-296">S’il n’y a pas d’erreurs de modèle, les données sont enregistrées et le navigateur est redirigé vers la Index page.</span><span class="sxs-lookup"><span data-stu-id="ea12b-296">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="ea12b-297">La Razor page créer</span><span class="sxs-lookup"><span data-stu-id="ea12b-297">The Create Razor Page</span></span>

<span data-ttu-id="ea12b-298">Examinez le fichier de page *pages/movies/Create. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="ea12b-298">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="ea12b-299">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea12b-299">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ea12b-300">Visual Studio affiche la balise `<form method="post">` dans une police différenciée en gras utilisée pour les Tag Helpers :</span><span class="sxs-lookup"><span data-stu-id="ea12b-300">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Vue VS17 de la page Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ea12b-302">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea12b-302">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ea12b-303">Pour plus d’informations sur les Tag Helpers, comme `<form method="post">`, consultez [Tag Helpers dans ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ea12b-303">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea12b-304">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="ea12b-304">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ea12b-305">Visual Studio pour Mac affiche la balise `<form method="post">` dans une police différenciée en gras utilisée pour les Tag Helpers.</span><span class="sxs-lookup"><span data-stu-id="ea12b-305">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="ea12b-306">L’élément `<form method="post">` est un [Tag Helper de formulaire](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ea12b-306">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="ea12b-307">Le Tag Helper de formulaire inclut automatiquement un [jeton de protection contre les falsifications](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="ea12b-307">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="ea12b-308">Le moteur de génération de modèles automatique crée un Razor balisage pour chaque champ du modèle, à l’exception de l’ID, semblable à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="ea12b-308">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="ea12b-309">Les [tag helpers de validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` et `<span asp-validation-for` ) affichent des erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="ea12b-309">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="ea12b-310">La validation est traitée de manière plus détaillée plus loin dans cette série.</span><span class="sxs-lookup"><span data-stu-id="ea12b-310">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="ea12b-311">Le [tag Helper étiquette](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) génère la légende et l' `[for]` attribut de l’étiquette pour la `Title` propriété.</span><span class="sxs-lookup"><span data-stu-id="ea12b-311">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="ea12b-312">Le [tag Helper d’entrée](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) utilise les attributs [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) et produit les attributs HTML nécessaires à la validation jQuery côté client.</span><span class="sxs-lookup"><span data-stu-id="ea12b-312">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ea12b-313">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ea12b-313">Additional resources</span></span>

* [<span data-ttu-id="ea12b-314">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="ea12b-314">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="ea12b-315">[Précédent : ajouter un modèle](xref:tutorials/razor-pages/model) 
>  [Suivant : utiliser une base de données](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="ea12b-315">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
