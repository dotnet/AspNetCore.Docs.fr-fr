---
title: 'Partie 5 : mettre à jour les pages générées'
author: rick-anderson
description: 'Partie 5 de la série de didacticiels sur les Razor pages.'
ms.author: riande
ms.date: 09/20/2020
no-loc:
- 'Index'
- 'Create'
- 'Delete'
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 7146c1955a578502a63578de4f1abce932cb8b32
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360603"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="f0eca-103">Partie 5 : mettre à jour les pages générées dans une application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f0eca-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="f0eca-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f0eca-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0eca-105">L’application de gestion des films générée est un bon début, mais la présentation n’est pas idéale.</span><span class="sxs-lookup"><span data-stu-id="f0eca-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="f0eca-106">La **publication** doit être de deux mots, **Date de publication**.</span><span class="sxs-lookup"><span data-stu-id="f0eca-106">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Application de gestion des films ouverte dans Chrome](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="f0eca-108">Mettre à jour le code généré</span><span class="sxs-lookup"><span data-stu-id="f0eca-108">Update the generated code</span></span>

<span data-ttu-id="f0eca-109">Ouvrez le fichier *Models/Movie.cs* , puis ajoutez les lignes affichées en surbrillance dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f0eca-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="f0eca-110">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="f0eca-110">In the previous code:</span></span>

* <span data-ttu-id="f0eca-111">L’annotation de données `[Column(TypeName = "decimal(18, 2)")]` permet à Entity Framework Core de mapper correctement `Price` en devise dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="f0eca-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="f0eca-112">Pour plus d’informations, consultez [Types de données](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="f0eca-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>
* <span data-ttu-id="f0eca-113">L’attribut [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) spécifie le nom complet d’un champ.</span><span class="sxs-lookup"><span data-stu-id="f0eca-113">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies the display name of a field.</span></span> <span data-ttu-id="f0eca-114">Dans le code précédent, « date de publication » au lieu de « Released ».</span><span class="sxs-lookup"><span data-stu-id="f0eca-114">In the preceding code, "Release Date" instead of "ReleaseDate".</span></span>
* <span data-ttu-id="f0eca-115">L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="f0eca-115">The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f0eca-116">Les informations d’heure stockées dans le champ ne sont pas affichées.</span><span class="sxs-lookup"><span data-stu-id="f0eca-116">The time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="f0eca-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) est abordé dans le tutoriel suivant.</span><span class="sxs-lookup"><span data-stu-id="f0eca-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span>

<span data-ttu-id="f0eca-118">Accédez à *pages/films* et pointez sur un lien **modifier** pour afficher l’URL cible.</span><span class="sxs-lookup"><span data-stu-id="f0eca-118">Browse to *Pages/Movies* and hover over an **Edit** link to see the target URL.</span></span>

![Fenêtre de navigateur avec la souris sur le lien Edit et l’URL de lien https://localhost:1234/Movies/Edit/5 affichée](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="f0eca-120">Les liens **Edit** , **Details** et **Delete** Links sont générés par le [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dans le fichier *pages/movies/ Index . cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f0eca-120">The **Edit** , **Details** , and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="f0eca-121">Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers.</span><span class="sxs-lookup"><span data-stu-id="f0eca-121">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="f0eca-122">Dans le code précédent, le [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) génère dynamiquement la `href` valeur de l’attribut HTML à partir de la Razor page (l’itinéraire est relatif), et l’identificateur de l' `asp-page` itinéraire ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="f0eca-122">In the preceding code, the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route identifier (`asp-route-id`).</span></span> <span data-ttu-id="f0eca-123">Pour plus d’informations, consultez [génération d’URL pour les pages](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="f0eca-123">For more information, see [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages).</span></span>

<span data-ttu-id="f0eca-124">Utilisez **afficher la source** à partir d’un navigateur pour examiner le balisage généré.</span><span class="sxs-lookup"><span data-stu-id="f0eca-124">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="f0eca-125">Une partie du code HTML généré est affichée ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="f0eca-125">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   <span data-ttu-id="f0eca-126">Les liens générés dynamiquement passent l’ID de film avec une chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="f0eca-126">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="f0eca-127">Par exemple, `?id=1` dans `https://localhost:5001/Movies/Details?id=1` .</span><span class="sxs-lookup"><span data-stu-id="f0eca-127">For example, the `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span></span>

### <a name="add-route-template"></a><span data-ttu-id="f0eca-128">Ajouter un modèle de route</span><span class="sxs-lookup"><span data-stu-id="f0eca-128">Add route template</span></span>

<span data-ttu-id="f0eca-129">Mettez à jour la modification, les détails et les Delete Razor pages pour utiliser le `{id:int}` modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="f0eca-129">Update the Edit, Details, and Delete Razor Pages to use the `{id:int}` route template.</span></span> <span data-ttu-id="f0eca-130">Remplacez la directive de chacune de ces pages (`@page "{id:int}"`) par `@page`.</span><span class="sxs-lookup"><span data-stu-id="f0eca-130">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="f0eca-131">Exécuter l’application, puis affichez le code source.</span><span class="sxs-lookup"><span data-stu-id="f0eca-131">Run the app and then view source.</span></span>

<span data-ttu-id="f0eca-132">Le code HTML généré ajoute l’ID à la partie de chemin de l’URL :</span><span class="sxs-lookup"><span data-stu-id="f0eca-132">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="f0eca-133">Une requête à la page avec le `{id:int}` modèle de routage qui n’inclut **pas** l’entier retourne une erreur HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="f0eca-133">A request to the page with the `{id:int}` route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="f0eca-134">Par exemple, `https://localhost:5001/Movies/Details` retourne une erreur 404.</span><span class="sxs-lookup"><span data-stu-id="f0eca-134">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="f0eca-135">Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :</span><span class="sxs-lookup"><span data-stu-id="f0eca-135">To make the ID optional, append `?` to the route constraint:</span></span>

```cshtml
@page "{id:int?}"
```

<span data-ttu-id="f0eca-136">Tester le comportement de `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="f0eca-136">Test the behavior of `@page "{id:int?}"`:</span></span>

1. <span data-ttu-id="f0eca-137">Définissez la directive de page dans *Pages/Movies/Details.cshtml* sur `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="f0eca-137">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
1. <span data-ttu-id="f0eca-138">Définissez un point d’arrêt dans `public async Task<IActionResult> OnGetAsync(int? id)` , dans *pages/movies/details. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="f0eca-138">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
1. <span data-ttu-id="f0eca-139">Accédez à `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="f0eca-139">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="f0eca-140">Avec la directive `@page "{id:int}"`, le point d’arrêt n’est jamais atteint.</span><span class="sxs-lookup"><span data-stu-id="f0eca-140">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="f0eca-141">Le moteur de routage retourne l’erreur HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="f0eca-141">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="f0eca-142">À l’aide de `@page "{id:int?}"` , la `OnGetAsync` méthode retourne `NotFound` (http 404) :</span><span class="sxs-lookup"><span data-stu-id="f0eca-142">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="f0eca-143">Passer en revue la gestion des exceptions d’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="f0eca-143">Review concurrency exception handling</span></span>

<span data-ttu-id="f0eca-144">Passez en revue la méthode `OnPostAsync` dans le fichier *Pages/Movies/Edit.cshtml.cs*  :</span><span class="sxs-lookup"><span data-stu-id="f0eca-144">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="f0eca-145">Le code précédent détecte les exceptions d’accès concurrentiel lorsqu’un client supprime le film et que l’autre client publie les modifications apportées au film.</span><span class="sxs-lookup"><span data-stu-id="f0eca-145">The previous code detects concurrency exceptions when one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="f0eca-146">Pour tester le bloc `catch` :</span><span class="sxs-lookup"><span data-stu-id="f0eca-146">To test the `catch` block:</span></span>

1. <span data-ttu-id="f0eca-147">Définissez un point d’arrêt sur `catch (DbUpdateConcurrencyException)` .</span><span class="sxs-lookup"><span data-stu-id="f0eca-147">Set a breakpoint on `catch (DbUpdateConcurrencyException)`.</span></span>
1. <span data-ttu-id="f0eca-148">Sélectionnez **Edit** (Modifier) pour un film, apportez des modifications, mais ne cliquez pas sur **Save** (Enregistrer).</span><span class="sxs-lookup"><span data-stu-id="f0eca-148">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
1. <span data-ttu-id="f0eca-149">Dans une autre fenêtre de navigateur, sélectionnez le **Delete** lien du même film, puis supprimez le film.</span><span class="sxs-lookup"><span data-stu-id="f0eca-149">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
1. <span data-ttu-id="f0eca-150">Dans la fenêtre de navigateur précédente, postez les modifications apportées au film.</span><span class="sxs-lookup"><span data-stu-id="f0eca-150">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="f0eca-151">Dans le code destiné à la production, il est nécessaire de détecter les conflits d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="f0eca-151">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="f0eca-152">Pour plus d’informations, consultez [Gérer les conflits d’accès concurrentiel](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="f0eca-152">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="f0eca-153">Validation de la publication et de la liaison</span><span class="sxs-lookup"><span data-stu-id="f0eca-153">Posting and binding review</span></span>

<span data-ttu-id="f0eca-154">Examinez le fichier *Pages/Movies/Edit.cshtml.cs* : </span><span class="sxs-lookup"><span data-stu-id="f0eca-154">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="f0eca-155">Quand une requête HTTP est effectuée sur la page movies/Edit, par exemple `https://localhost:5001/Movies/Edit/3` :</span><span class="sxs-lookup"><span data-stu-id="f0eca-155">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="f0eca-156">La méthode `OnGetAsync` extrait le film de la base de données et retourne la méthode `Page`.</span><span class="sxs-lookup"><span data-stu-id="f0eca-156">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="f0eca-157">La `Page` méthode restitue la page *pages/movies/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="f0eca-157">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="f0eca-158">Le fichier *pages/movies/Edit. cshtml* contient la directive Model `@model RazorPagesMovie.Pages.Movies.EditModel` , qui rend le modèle Movie disponible sur la page.</span><span class="sxs-lookup"><span data-stu-id="f0eca-158">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="f0eca-159">Le formulaire d’édition s’affiche avec les valeurs relatives au film.</span><span class="sxs-lookup"><span data-stu-id="f0eca-159">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="f0eca-160">Quand la page Movies/Edit est postée :</span><span class="sxs-lookup"><span data-stu-id="f0eca-160">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="f0eca-161">Les valeurs de formulaire affichées dans la page sont liées à la propriété `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f0eca-161">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="f0eca-162">L’attribut `[BindProperty]` active la [liaison de données](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="f0eca-162">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="f0eca-163">S’il existe des erreurs dans l’état du modèle, par exemple, `ReleaseDate` ne peut pas être convertie en date, le formulaire est à nouveau affiché avec les valeurs soumises.</span><span class="sxs-lookup"><span data-stu-id="f0eca-163">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="f0eca-164">S’il n’y a aucune erreur de modèle, le film est enregistré.</span><span class="sxs-lookup"><span data-stu-id="f0eca-164">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="f0eca-165">Les méthodes HTTP d’extraction des Index Create pages, et Delete Razor suivent un modèle similaire.</span><span class="sxs-lookup"><span data-stu-id="f0eca-165">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="f0eca-166">La méthode HTTP après `OnPostAsync` dans la Create Razor page suit un modèle similaire à la `OnPostAsync` méthode dans la page de modification Razor .</span><span class="sxs-lookup"><span data-stu-id="f0eca-166">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f0eca-167">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f0eca-167">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f0eca-168">[Précédent : utiliser une base de données](xref:tutorials/razor-pages/sql) 
>  [Suivant : ajouter une recherche](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="f0eca-168">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f0eca-169">L’application de gestion des films générée est un bon début, mais la présentation n’est pas idéale.</span><span class="sxs-lookup"><span data-stu-id="f0eca-169">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="f0eca-170">La **publication** doit être de deux mots, **Date de publication**.</span><span class="sxs-lookup"><span data-stu-id="f0eca-170">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Application de gestion des films ouverte dans Chrome](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="f0eca-172">Mettre à jour le code généré</span><span class="sxs-lookup"><span data-stu-id="f0eca-172">Update the generated code</span></span>

<span data-ttu-id="f0eca-173">Ouvrez le fichier *Models/Movie.cs* , puis ajoutez les lignes affichées en surbrillance dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f0eca-173">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="f0eca-174">L’annotation de données `[Column(TypeName = "decimal(18, 2)")]` permet à Entity Framework Core de mapper correctement `Price` en devise dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="f0eca-174">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="f0eca-175">Pour plus d’informations, consultez [Types de données](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="f0eca-175">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="f0eca-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) est abordé dans le tutoriel suivant.</span><span class="sxs-lookup"><span data-stu-id="f0eca-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="f0eca-177">L’attribut [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) spécifie les éléments à afficher pour le nom d’un champ, dans le cas présent « date de publication » au lieu de « Released ».</span><span class="sxs-lookup"><span data-stu-id="f0eca-177">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies what to display for the name of a field, in this case "Release Date" instead of "ReleaseDate".</span></span> <span data-ttu-id="f0eca-178">L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ), de sorte que les informations d’heure stockées dans le champ ne sont pas affichées.</span><span class="sxs-lookup"><span data-stu-id="f0eca-178">The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="f0eca-179">Accédez à Pages/Movies, puis placez le curseur sur un lien **Modifier** pour afficher l’URL cible.</span><span class="sxs-lookup"><span data-stu-id="f0eca-179">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Fenêtre de navigateur avec la souris sur le lien Edit et l’URL de lien http://localhost:1234/Movies/Edit/5 affichée](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="f0eca-181">Les liens **Edit** , **Details** et **Delete** Links sont générés par le [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dans le fichier *pages/movies/ Index . cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f0eca-181">The **Edit** , **Details** , and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="f0eca-182">Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers.</span><span class="sxs-lookup"><span data-stu-id="f0eca-182">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="f0eca-183">Dans le code précédent, le `AnchorTagHelper` génère dynamiquement la `href` valeur de l’attribut HTML à partir de la Razor page (l’itinéraire est relatif), le `asp-page` et l’ID de l’itinéraire ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="f0eca-183">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route id (`asp-route-id`).</span></span> <span data-ttu-id="f0eca-184">Pour plus d’informations, consultez [Génération d’URL pour les pages](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="f0eca-184">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="f0eca-185">Utilisez **afficher la source** à partir d’un navigateur pour examiner le balisage généré.</span><span class="sxs-lookup"><span data-stu-id="f0eca-185">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="f0eca-186">Une partie du code HTML généré est affichée ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="f0eca-186">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="f0eca-187">Les liens générés dynamiquement passent l’ID de film avec une chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="f0eca-187">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="f0eca-188">Par exemple, `?id=1` dans  `https://localhost:5001/Movies/Details?id=1` .</span><span class="sxs-lookup"><span data-stu-id="f0eca-188">For example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`.</span></span>

<span data-ttu-id="f0eca-189">Mettez à jour les pages Edition, détails et Delete Razor pages pour utiliser le modèle de routage « {ID : int} ».</span><span class="sxs-lookup"><span data-stu-id="f0eca-189">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="f0eca-190">Remplacez la directive de chacune de ces pages (`@page "{id:int}"`) par `@page`.</span><span class="sxs-lookup"><span data-stu-id="f0eca-190">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="f0eca-191">Exécuter l’application, puis affichez le code source.</span><span class="sxs-lookup"><span data-stu-id="f0eca-191">Run the app and then view source.</span></span> <span data-ttu-id="f0eca-192">Le code HTML généré ajoute l’ID à la partie de chemin de l’URL :</span><span class="sxs-lookup"><span data-stu-id="f0eca-192">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="f0eca-193">Une requête à la page avec le modèle d’itinéraire « {id:int} » qui n’inclut **pas** l’entier retourne une erreur HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="f0eca-193">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="f0eca-194">Par exemple, `https://localhost:5001/Movies/Details` retourne une erreur 404.</span><span class="sxs-lookup"><span data-stu-id="f0eca-194">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="f0eca-195">Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :</span><span class="sxs-lookup"><span data-stu-id="f0eca-195">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="f0eca-196">Pour tester le comportement de `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="f0eca-196">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="f0eca-197">Définissez la directive de page dans *Pages/Movies/Details.cshtml* sur `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="f0eca-197">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="f0eca-198">Définissez un point d’arrêt dans `public async Task<IActionResult> OnGetAsync(int? id)` , dans *pages/movies/details. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="f0eca-198">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
* <span data-ttu-id="f0eca-199">Accédez à `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="f0eca-199">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="f0eca-200">Avec la directive `@page "{id:int}"`, le point d’arrêt n’est jamais atteint.</span><span class="sxs-lookup"><span data-stu-id="f0eca-200">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="f0eca-201">Le moteur de routage retourne l’erreur HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="f0eca-201">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="f0eca-202">À l’aide de `@page "{id:int?}"` , la `OnGetAsync` méthode retourne `NotFound` (http 404) :</span><span class="sxs-lookup"><span data-stu-id="f0eca-202">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="f0eca-203">Passer en revue la gestion des exceptions d’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="f0eca-203">Review concurrency exception handling</span></span>

<span data-ttu-id="f0eca-204">Passez en revue la méthode `OnPostAsync` dans le fichier *Pages/Movies/Edit.cshtml.cs*  :</span><span class="sxs-lookup"><span data-stu-id="f0eca-204">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="f0eca-205">Le code précédent détecte les exceptions d’accès concurrentiel quand un client supprime le film et que l’autre client envoie les modifications apportées au film.</span><span class="sxs-lookup"><span data-stu-id="f0eca-205">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="f0eca-206">Pour tester le bloc `catch` :</span><span class="sxs-lookup"><span data-stu-id="f0eca-206">To test the `catch` block:</span></span>

* <span data-ttu-id="f0eca-207">Définissez un point d'arrêt sur `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="f0eca-207">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="f0eca-208">Sélectionnez **Edit** (Modifier) pour un film, apportez des modifications, mais ne cliquez pas sur **Save** (Enregistrer).</span><span class="sxs-lookup"><span data-stu-id="f0eca-208">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="f0eca-209">Dans une autre fenêtre de navigateur, sélectionnez le **Delete** lien du même film, puis supprimez le film.</span><span class="sxs-lookup"><span data-stu-id="f0eca-209">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="f0eca-210">Dans la fenêtre de navigateur précédente, postez les modifications apportées au film.</span><span class="sxs-lookup"><span data-stu-id="f0eca-210">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="f0eca-211">Dans le code destiné à la production, il est nécessaire de détecter les conflits d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="f0eca-211">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="f0eca-212">Pour plus d’informations, consultez [Gérer les conflits d’accès concurrentiel](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="f0eca-212">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="f0eca-213">Validation de la publication et de la liaison</span><span class="sxs-lookup"><span data-stu-id="f0eca-213">Posting and binding review</span></span>

<span data-ttu-id="f0eca-214">Examinez le fichier *Pages/Movies/Edit.cshtml.cs* : </span><span class="sxs-lookup"><span data-stu-id="f0eca-214">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="f0eca-215">Quand une requête HTTP est effectuée sur la page movies/Edit, par exemple `https://localhost:5001/Movies/Edit/3` :</span><span class="sxs-lookup"><span data-stu-id="f0eca-215">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="f0eca-216">La méthode `OnGetAsync` extrait le film de la base de données et retourne la méthode `Page`.</span><span class="sxs-lookup"><span data-stu-id="f0eca-216">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="f0eca-217">La `Page` méthode restitue la page *pages/movies/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="f0eca-217">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="f0eca-218">Le fichier *pages/movies/Edit. cshtml* contient la directive Model `@model RazorPagesMovie.Pages.Movies.EditModel` , qui rend le modèle Movie disponible sur la page.</span><span class="sxs-lookup"><span data-stu-id="f0eca-218">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="f0eca-219">Le formulaire d’édition s’affiche avec les valeurs relatives au film.</span><span class="sxs-lookup"><span data-stu-id="f0eca-219">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="f0eca-220">Quand la page Movies/Edit est postée :</span><span class="sxs-lookup"><span data-stu-id="f0eca-220">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="f0eca-221">Les valeurs de formulaire affichées dans la page sont liées à la propriété `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f0eca-221">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="f0eca-222">L’attribut `[BindProperty]` active la [liaison de données](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="f0eca-222">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="f0eca-223">S’il existe des erreurs dans l’état du modèle, par exemple, `ReleaseDate` ne peut pas être convertie en date, le formulaire s’affiche avec les valeurs soumises.</span><span class="sxs-lookup"><span data-stu-id="f0eca-223">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="f0eca-224">S’il n’y a aucune erreur de modèle, le film est enregistré.</span><span class="sxs-lookup"><span data-stu-id="f0eca-224">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="f0eca-225">Les méthodes HTTP d’extraction des Index Create pages, et Delete Razor suivent un modèle similaire.</span><span class="sxs-lookup"><span data-stu-id="f0eca-225">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="f0eca-226">La méthode HTTP après `OnPostAsync` dans la Create Razor page suit un modèle similaire à la `OnPostAsync` méthode dans la page de modification Razor .</span><span class="sxs-lookup"><span data-stu-id="f0eca-226">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="f0eca-227">La fonction de recherche est ajoutée dans le prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="f0eca-227">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f0eca-228">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f0eca-228">Additional resources</span></span>

* [<span data-ttu-id="f0eca-229">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="f0eca-229">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="f0eca-230">[Précédent : utiliser une base de données](xref:tutorials/razor-pages/sql) 
>  [Suivant : ajouter une recherche](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="f0eca-230">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
