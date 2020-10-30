---
title: 'Partie 6, ajouter une recherche aux :::no-loc(Razor)::: Pages ASP.net Core'
author: rick-anderson
description: 'Partie 6 de la série de didacticiels sur les :::no-loc(Razor)::: pages.'
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/search
ms.openlocfilehash: 960f60198f5e65ed05d0374fd0704537376d27d6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058088"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a><span data-ttu-id="f7e9e-103">Partie 6, ajouter une recherche aux :::no-loc(Razor)::: Pages ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f7e9e-103">Part 6, add search to ASP.NET Core :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="f7e9e-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f7e9e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="f7e9e-105">Dans les sections suivantes, la recherche de films par *genre* ou par *nom* est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="f7e9e-106">Ajoutez les propriétés en surbrillance suivantes à *Pages/Movies/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs* :</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="f7e9e-107">`SearchString` : contient le texte que les utilisateurs entrent dans la zone de texte de recherche.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="f7e9e-108">`SearchString` a l' [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-108">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="f7e9e-109">`[BindProperty]` lie les valeurs de formulaire et les chaînes de requête avec le même nom que la propriété.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="f7e9e-110">`(SupportsGet = true)` est obligatoire pour la liaison sur les requêtes GET.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="f7e9e-111">`Genres` : contient la liste des genres.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="f7e9e-112">`Genres` permet à l’utilisateur de sélectionner un genre dans la liste.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="f7e9e-113">`SelectList` nécessite `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="f7e9e-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="f7e9e-114">`MovieGenre` : contient le genre spécifique sélectionné par l’utilisateur (par exemple, « Western »).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="f7e9e-115">`Genres` et `MovieGenre` sont utilisés plus loin dans ce tutoriel.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="f7e9e-116">Mettez à jour la méthode `OnGetAsync` de la page d’index avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="f7e9e-117">La première ligne de la méthode `OnGetAsync` crée une requête [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pour sélectionner les films :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="f7e9e-118">La requête est *seulement* définie à ce stade, elle n’a **pas** été exécutée sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="f7e9e-119">Si la propriété `SearchString` n’est pas nulle ou vide, la requête sur les films est modifiée de façon à filtrer sur la chaîne de recherche :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="f7e9e-120">Le code `s => s.Title.Contains()` est une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="f7e9e-121">Les expressions lambda sont utilisées dans les requêtes [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) basées sur une méthode comme arguments pour les méthodes d’opérateur de requête standard, telles que la méthode [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) ou `Contains` (utilisée dans le code précédent).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="f7e9e-122">Les requêtes LINQ ne sont pas exécutées quand elles sont définies ou quand elles sont modifiées en appelant une méthode (comme `Where`, `Contains` ou `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="f7e9e-123">Au lieu de cela, l’exécution de la requête est différée.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="f7e9e-124">Cela signifie que l’évaluation d’une expression est retardée jusqu’à ce que sa valeur réalisée fasse l’objet d’une itération réelle ou que la méthode `ToListAsync` soit appelée.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="f7e9e-125">Pour plus d’informations, consultez [Exécution de requête](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f7e9e-126">La méthode [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) est exécutée sur la base de données, et non pas dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-126">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="f7e9e-127">Le respect de la casse pour la requête dépend de la base de données et du classement.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="f7e9e-128">Sur SQL Server, `Contains` est mappée à [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), qui ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="f7e9e-129">Dans SQLite, avec le classement par défaut, elle respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="f7e9e-130">Accédez à la page Movies, puis ajoutez une chaîne de requête telle que `?searchString=Ghost` à l’URL (par exemple, `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="f7e9e-131">Les films filtrés sont affichés.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-131">The filtered movies are displayed.</span></span>

![Vue Index](search/_static/ghost.png)

<span data-ttu-id="f7e9e-133">Si le modèle d’itinéraire suivant est ajouté à la page d’index, la chaîne de recherche peut être passée comme un segment d’URL (par exemple, `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="f7e9e-134">La contrainte d’itinéraire précédente permet de rechercher le titre comme données d’itinéraire (un segment d’URL) et non comme valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="f7e9e-135">Le `?` dans `"{searchString?}"` signifie qu’il s’agit d’un paramètre d’itinéraire facultatif.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Vue Index avec le mot « ghost » ajouté à l’URL et une liste de films retournée contenant deux films, Ghostbusters et Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="f7e9e-137">Le runtime ASP.NET Core utilise la [liaison de modèle](xref:mvc/models/model-binding) pour définir la valeur de la propriété `SearchString` à partir de la chaîne de requête (`?searchString=Ghost`) ou des données de la route (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="f7e9e-138">La liaison de modèle ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="f7e9e-139">Cependant, vous ne pouvez pas attendre des utilisateurs qu’ils modifient l’URL pour rechercher un film.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="f7e9e-140">Dans cette étape, une interface utilisateur est ajoutée pour filtrer les films.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="f7e9e-141">Si vous avez ajouté la contrainte d’itinéraire `"{searchString?}"`, supprimez-la.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="f7e9e-142">Ouvrez le fichier *Pages/Movies/Index.cshtml* , puis ajoutez la balise `<form>` mise en surbrillance dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="f7e9e-143">La balise HTML `<form>` utilise les [Tag Helpers](xref:mvc/views/tag-helpers/intro) suivants :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="f7e9e-144">[Tag Helper Form](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="f7e9e-145">Quand le formulaire est envoyé, la chaîne de filtrage est envoyée à la page *Pages/Movies/Index* via la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="f7e9e-146">Tag Helper Input</span><span class="sxs-lookup"><span data-stu-id="f7e9e-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="f7e9e-147">Enregistrez les modifications apportées, puis testez le filtre.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-147">Save the changes and test the filter.</span></span>

![Vue Index avec le mot « ghost » tapé dans la zone de texte de filtre des titres](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="f7e9e-149">Rechercher par genre</span><span class="sxs-lookup"><span data-stu-id="f7e9e-149">Search by genre</span></span>

<span data-ttu-id="f7e9e-150">Mettez à jour la méthode `OnGetAsync` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="f7e9e-151">Le code suivant est une requête LINQ qui récupère tous les genres dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="f7e9e-152">La liste `SelectList` de genres est créée en projetant des différents genres.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="f7e9e-153">Ajouter la recherche par genre à la :::no-loc(Razor)::: page</span><span class="sxs-lookup"><span data-stu-id="f7e9e-153">Add search by genre to the :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="f7e9e-154">Mettez à jour *Index.cshtml* comme suit :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="f7e9e-155">Testez l’application en effectuant une recherche par genre, par titre de film et selon ces deux critères.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-155">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7e9e-156">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f7e9e-156">Additional resources</span></span>

* [<span data-ttu-id="f7e9e-157">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="f7e9e-157">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="f7e9e-158">[Précédent : mise à jour des pages](xref:tutorials/razor-pages/da1) 
>  [Suivant : ajout d’un nouveau champ](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="f7e9e-158">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="f7e9e-159">Dans les sections suivantes, la recherche de films par *genre* ou par *nom* est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-159">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="f7e9e-160">Ajoutez les propriétés en surbrillance suivantes à *Pages/Movies/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-160">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs* :</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="f7e9e-161">`SearchString` : contient le texte que les utilisateurs entrent dans la zone de texte de recherche.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-161">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="f7e9e-162">`SearchString` a l' [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-162">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="f7e9e-163">`[BindProperty]` lie les valeurs de formulaire et les chaînes de requête avec le même nom que la propriété.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-163">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="f7e9e-164">`(SupportsGet = true)` est obligatoire pour la liaison sur les requêtes GET.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-164">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="f7e9e-165">`Genres` : contient la liste des genres.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-165">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="f7e9e-166">`Genres` permet à l’utilisateur de sélectionner un genre dans la liste.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-166">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="f7e9e-167">`SelectList` nécessite `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="f7e9e-167">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="f7e9e-168">`MovieGenre` : contient le genre spécifique sélectionné par l’utilisateur (par exemple, « Western »).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-168">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="f7e9e-169">`Genres` et `MovieGenre` sont utilisés plus loin dans ce tutoriel.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-169">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="f7e9e-170">Mettez à jour la méthode `OnGetAsync` de la page d’index avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-170">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="f7e9e-171">La première ligne de la méthode `OnGetAsync` crée une requête [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pour sélectionner les films :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-171">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="f7e9e-172">La requête est *seulement* définie à ce stade, elle n’a **pas** été exécutée sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-172">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="f7e9e-173">Si la propriété `SearchString` n’est pas nulle ou vide, la requête sur les films est modifiée de façon à filtrer sur la chaîne de recherche :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-173">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="f7e9e-174">Le code `s => s.Title.Contains()` est une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-174">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="f7e9e-175">Les expressions lambda sont utilisées dans les requêtes [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) basées sur une méthode comme arguments pour les méthodes d’opérateur de requête standard, telles que la méthode [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) ou `Contains` (utilisée dans le code précédent).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-175">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="f7e9e-176">Les requêtes LINQ ne sont pas exécutées quand elles sont définies ou quand elles sont modifiées en appelant une méthode (comme `Where`, `Contains` ou `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-176">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="f7e9e-177">Au lieu de cela, l’exécution de la requête est différée.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-177">Rather, query execution is deferred.</span></span> <span data-ttu-id="f7e9e-178">Cela signifie que l’évaluation d’une expression est retardée jusqu’à ce que sa valeur réalisée fasse l’objet d’une itération réelle ou que la méthode `ToListAsync` soit appelée.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-178">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="f7e9e-179">Pour plus d’informations, consultez [Exécution de requête](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-179">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="f7e9e-180">**Remarque :** La méthode [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) est exécutée sur la base de données, et non pas dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-180">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="f7e9e-181">Le respect de la casse pour la requête dépend de la base de données et du classement.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-181">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="f7e9e-182">Sur SQL Server, `Contains` est mappée à [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), qui ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-182">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="f7e9e-183">Dans SQLite, avec le classement par défaut, elle respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-183">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="f7e9e-184">Accédez à la page Movies, puis ajoutez une chaîne de requête telle que `?searchString=Ghost` à l’URL (par exemple, `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-184">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="f7e9e-185">Les films filtrés sont affichés.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-185">The filtered movies are displayed.</span></span>

![Vue Index](search/_static/ghost.png)

<span data-ttu-id="f7e9e-187">Si le modèle d’itinéraire suivant est ajouté à la page d’index, la chaîne de recherche peut être passée comme un segment d’URL (par exemple, `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-187">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="f7e9e-188">La contrainte d’itinéraire précédente permet de rechercher le titre comme données d’itinéraire (un segment d’URL) et non comme valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-188">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="f7e9e-189">Le `?` dans `"{searchString?}"` signifie qu’il s’agit d’un paramètre d’itinéraire facultatif.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-189">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Vue Index avec le mot « ghost » ajouté à l’URL et une liste de films retournée contenant deux films, Ghostbusters et Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="f7e9e-191">Le runtime ASP.NET Core utilise la [liaison de modèle](xref:mvc/models/model-binding) pour définir la valeur de la propriété `SearchString` à partir de la chaîne de requête (`?searchString=Ghost`) ou des données de la route (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-191">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="f7e9e-192">La liaison de modèle ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-192">Model binding is not case sensitive.</span></span>

<span data-ttu-id="f7e9e-193">Cependant, vous ne pouvez pas attendre des utilisateurs qu’ils modifient l’URL pour rechercher un film.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-193">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="f7e9e-194">Dans cette étape, une interface utilisateur est ajoutée pour filtrer les films.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-194">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="f7e9e-195">Si vous avez ajouté la contrainte d’itinéraire `"{searchString?}"`, supprimez-la.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-195">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="f7e9e-196">Ouvrez le fichier *Pages/Movies/Index.cshtml* , puis ajoutez la balise `<form>` mise en surbrillance dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-196">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="f7e9e-197">La balise HTML `<form>` utilise les [Tag Helpers](xref:mvc/views/tag-helpers/intro) suivants :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-197">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="f7e9e-198">[Tag Helper Form](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f7e9e-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="f7e9e-199">Quand le formulaire est envoyé, la chaîne de filtrage est envoyée à la page *Pages/Movies/Index* via la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-199">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="f7e9e-200">Tag Helper Input</span><span class="sxs-lookup"><span data-stu-id="f7e9e-200">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="f7e9e-201">Enregistrez les modifications apportées, puis testez le filtre.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-201">Save the changes and test the filter.</span></span>

![Vue Index avec le mot « ghost » tapé dans la zone de texte de filtre des titres](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="f7e9e-203">Rechercher par genre</span><span class="sxs-lookup"><span data-stu-id="f7e9e-203">Search by genre</span></span>

<span data-ttu-id="f7e9e-204">Mettez à jour la méthode `OnGetAsync` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-204">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="f7e9e-205">Le code suivant est une requête LINQ qui récupère tous les genres dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-205">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="f7e9e-206">La liste `SelectList` de genres est créée en projetant des différents genres.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-206">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="f7e9e-207">Ajouter la recherche par genre à la :::no-loc(Razor)::: page</span><span class="sxs-lookup"><span data-stu-id="f7e9e-207">Add search by genre to the :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="f7e9e-208">Mettez à jour *Index.cshtml* comme suit :</span><span class="sxs-lookup"><span data-stu-id="f7e9e-208">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="f7e9e-209">Testez l’application en effectuant une recherche par genre, par titre de film et selon ces deux critères.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-209">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="f7e9e-210">Le code précédent utilise le [tag Helper Select](xref:mvc/views/working-with-forms#the-select-tag-helper) et option tag Helper.</span><span class="sxs-lookup"><span data-stu-id="f7e9e-210">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7e9e-211">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f7e9e-211">Additional resources</span></span>

* [<span data-ttu-id="f7e9e-212">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="f7e9e-212">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="f7e9e-213">[Précédent : mise à jour des pages](xref:tutorials/razor-pages/da1) 
>  [Suivant : ajout d’un nouveau champ](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="f7e9e-213">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
