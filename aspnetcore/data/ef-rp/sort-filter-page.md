---
title: 'Partie 3 : :::no-loc(Razor)::: pages avec EF Core dans ASP.net Core-Trier, filtrer, pagination'
author: rick-anderson
description: 'Partie 3 des :::no-loc(Razor)::: pages et Entity Framework de la série de didacticiels.'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 51a1e2a90259898262ac655b7a0e8a55d766f0c7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061039"
---
# <a name="part-3-no-locrazor-pages-with-ef-core-in-aspnet-core---sort-filter-paging"></a><span data-ttu-id="61d18-103">Partie 3 : :::no-loc(Razor)::: pages avec EF Core dans ASP.net Core-Trier, filtrer, pagination</span><span class="sxs-lookup"><span data-stu-id="61d18-103">Part 3, :::no-loc(Razor)::: Pages with EF Core in ASP.NET Core - Sort, Filter, Paging</span></span>

<span data-ttu-id="61d18-104">Par [Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT) et [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="61d18-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="61d18-105">Ce tutoriel ajoute des fonctionnalités de tri, de filtrage et de pagination aux pages des étudiants.</span><span class="sxs-lookup"><span data-stu-id="61d18-105">This tutorial adds sorting, filtering, and paging functionality to the Students pages.</span></span>

<span data-ttu-id="61d18-106">L’illustration suivante présente une page complète.</span><span class="sxs-lookup"><span data-stu-id="61d18-106">The following illustration shows a completed page.</span></span> <span data-ttu-id="61d18-107">Les en-têtes de colonne sont des liens hypertexte permettant de trier la colonne.</span><span class="sxs-lookup"><span data-stu-id="61d18-107">The column headings are clickable links to sort the column.</span></span> <span data-ttu-id="61d18-108">Cliquez de façon répétée sur un en-tête de colonne pour changer l’ordre de tri (croissant ou décroissant).</span><span class="sxs-lookup"><span data-stu-id="61d18-108">Click a column heading repeatedly to switch between ascending and descending sort order.</span></span>

![Page d’index des étudiants](sort-filter-page/_static/paging30.png)

## <a name="add-sorting"></a><span data-ttu-id="61d18-110">Ajouter la fonctionnalité de tri</span><span class="sxs-lookup"><span data-stu-id="61d18-110">Add sorting</span></span>

<span data-ttu-id="61d18-111">Remplacez le code de *Pages/Students/Index.cshtml.cs* par le code suivant pour ajouter la fonctionnalité de tri.</span><span class="sxs-lookup"><span data-stu-id="61d18-111">Replace the code in *Pages/Students/Index.cshtml.cs* with the following code to add sorting.</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_All)]

<span data-ttu-id="61d18-112">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="61d18-112">The preceding code:</span></span>

* <span data-ttu-id="61d18-113">Nécessite l’ajout de `using System;` .</span><span class="sxs-lookup"><span data-stu-id="61d18-113">Requires adding `using System;`.</span></span>
* <span data-ttu-id="61d18-114">Ajoute des propriétés devant contenir les paramètres de tri.</span><span class="sxs-lookup"><span data-stu-id="61d18-114">Adds properties to contain the sorting parameters.</span></span>
* <span data-ttu-id="61d18-115">Remplace le nom de la propriété `Student` par `Students`.</span><span class="sxs-lookup"><span data-stu-id="61d18-115">Changes the name of the `Student` property to `Students`.</span></span>
* <span data-ttu-id="61d18-116">Remplace le code de la méthode `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="61d18-116">Replaces the code in the `OnGetAsync` method.</span></span>

<span data-ttu-id="61d18-117">La méthode `OnGetAsync` reçoit un paramètre `sortOrder` à partir de la chaîne de requête dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="61d18-117">The `OnGetAsync` method receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="61d18-118">L’URL et la chaîne de requête sont générées par le [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="61d18-118">The URL and query string is generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span>

<span data-ttu-id="61d18-119">Le `sortOrder` paramètre a la valeur `Name` ou `Date` .</span><span class="sxs-lookup"><span data-stu-id="61d18-119">The `sortOrder` parameter is either `Name` or `Date`.</span></span> <span data-ttu-id="61d18-120">Le `sortOrder` paramètre est éventuellement suivi `_desc` de pour spécifier l’ordre décroissant.</span><span class="sxs-lookup"><span data-stu-id="61d18-120">The `sortOrder` parameter is optionally followed by `_desc` to specify descending order.</span></span> <span data-ttu-id="61d18-121">L’ordre de tri par défaut est croissant.</span><span class="sxs-lookup"><span data-stu-id="61d18-121">The default sort order is ascending.</span></span>

<span data-ttu-id="61d18-122">Quand la page Index est demandée à partir du lien **Students** , il n’existe aucune chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="61d18-122">When the Index page is requested from the **Students** link, there's no query string.</span></span> <span data-ttu-id="61d18-123">Les étudiants sont affichés par nom de famille dans l’ordre croissant.</span><span class="sxs-lookup"><span data-stu-id="61d18-123">The students are displayed in ascending order by last name.</span></span> <span data-ttu-id="61d18-124">L’ordre croissant par nom de famille est le `default` dans l' `switch` instruction.</span><span class="sxs-lookup"><span data-stu-id="61d18-124">Ascending order by last name is the `default` in the `switch` statement.</span></span> <span data-ttu-id="61d18-125">Quand l’utilisateur clique sur un lien d’en-tête de colonne, la valeur `sortOrder` appropriée est fournie dans la valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="61d18-125">When the user clicks a column heading link, the appropriate `sortOrder` value is provided in the query string value.</span></span>

<span data-ttu-id="61d18-126">`NameSort` et `DateSort` sont utilisés par la :::no-loc(Razor)::: page pour configurer les liens hypertexte d’en-tête de colonne avec les valeurs de chaîne de requête appropriées :</span><span class="sxs-lookup"><span data-stu-id="61d18-126">`NameSort` and `DateSort` are used by the :::no-loc(Razor)::: Page to configure the column heading hyperlinks with the appropriate query string values:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_Ternary)]

<span data-ttu-id="61d18-127">Le code utilise l' [opérateur conditionnel C# ?:](/dotnet/csharp/language-reference/operators/conditional-operator).</span><span class="sxs-lookup"><span data-stu-id="61d18-127">The code uses the C# [conditional operator ?:](/dotnet/csharp/language-reference/operators/conditional-operator).</span></span> <span data-ttu-id="61d18-128">L' `?:` opérateur est un opérateur ternaire qui prend trois opérandes.</span><span class="sxs-lookup"><span data-stu-id="61d18-128">The `?:` operator is a ternary operator, it takes three operands.</span></span> <span data-ttu-id="61d18-129">La première ligne spécifie que lorsque `sortOrder` a la valeur null ou est vide, `NameSort` a la valeur `name_desc` .</span><span class="sxs-lookup"><span data-stu-id="61d18-129">The first line specifies that when `sortOrder` is null or empty, `NameSort` is set to `name_desc`.</span></span> <span data-ttu-id="61d18-130">Si `sortOrder` est \* *_not_* _ null ou vide, `NameSort` a pour valeur une chaîne vide.</span><span class="sxs-lookup"><span data-stu-id="61d18-130">If `sortOrder` is \* *_not_* _ null or empty, `NameSort` is set to an empty string.</span></span>

<span data-ttu-id="61d18-131">Ces deux instructions permettent à la page de définir les liens hypertexte d’en-tête de colonne comme suit :</span><span class="sxs-lookup"><span data-stu-id="61d18-131">These two statements enable the page to set the column heading hyperlinks as follows:</span></span>

| <span data-ttu-id="61d18-132">Ordre de tri actuel</span><span class="sxs-lookup"><span data-stu-id="61d18-132">Current sort order</span></span>   | <span data-ttu-id="61d18-133">Lien hypertexte Nom de famille</span><span class="sxs-lookup"><span data-stu-id="61d18-133">Last Name Hyperlink</span></span> | <span data-ttu-id="61d18-134">Lien hypertexte Date</span><span class="sxs-lookup"><span data-stu-id="61d18-134">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="61d18-135">Nom de famille croissant</span><span class="sxs-lookup"><span data-stu-id="61d18-135">Last Name ascending</span></span>  | <span data-ttu-id="61d18-136">descending</span><span class="sxs-lookup"><span data-stu-id="61d18-136">descending</span></span>          | <span data-ttu-id="61d18-137">ascending</span><span class="sxs-lookup"><span data-stu-id="61d18-137">ascending</span></span>      |
| <span data-ttu-id="61d18-138">Nom de famille décroissant</span><span class="sxs-lookup"><span data-stu-id="61d18-138">Last Name descending</span></span> | <span data-ttu-id="61d18-139">ascending</span><span class="sxs-lookup"><span data-stu-id="61d18-139">ascending</span></span>           | <span data-ttu-id="61d18-140">ascending</span><span class="sxs-lookup"><span data-stu-id="61d18-140">ascending</span></span>      |
| <span data-ttu-id="61d18-141">Date croissante</span><span class="sxs-lookup"><span data-stu-id="61d18-141">Date ascending</span></span>       | <span data-ttu-id="61d18-142">ascending</span><span class="sxs-lookup"><span data-stu-id="61d18-142">ascending</span></span>           | <span data-ttu-id="61d18-143">descending</span><span class="sxs-lookup"><span data-stu-id="61d18-143">descending</span></span>     |
| <span data-ttu-id="61d18-144">Date décroissante</span><span class="sxs-lookup"><span data-stu-id="61d18-144">Date descending</span></span>      | <span data-ttu-id="61d18-145">ascending</span><span class="sxs-lookup"><span data-stu-id="61d18-145">ascending</span></span>           | <span data-ttu-id="61d18-146">ascending</span><span class="sxs-lookup"><span data-stu-id="61d18-146">ascending</span></span>      |

<span data-ttu-id="61d18-147">La méthode utilise LINQ to Entities pour spécifier la colonne d’après laquelle effectuer le tri.</span><span class="sxs-lookup"><span data-stu-id="61d18-147">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="61d18-148">Le code initialise un `IQueryable<Student>` avant l’instruction switch, et le modifie dans l’instruction switch :</span><span class="sxs-lookup"><span data-stu-id="61d18-148">The code initializes an `IQueryable<Student>` before the switch statement, and modifies it in the switch statement:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_IQueryable)]

<span data-ttu-id="61d18-149">Lorsqu’une `IQueryable` est créée ou modifiée, aucune requête n’est envoyée à la base de données.</span><span class="sxs-lookup"><span data-stu-id="61d18-149">When an `IQueryable` is created or modified, no query is sent to the database.</span></span> <span data-ttu-id="61d18-150">La requête n’est pas exécutée tant que l’objet `IQueryable` n’a pas été converti en collection.</span><span class="sxs-lookup"><span data-stu-id="61d18-150">The query isn't executed until the `IQueryable` object is converted into a collection.</span></span> <span data-ttu-id="61d18-151">Les `IQueryable` sont convertis en collection en appelant une méthode telle que `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="61d18-151">`IQueryable` are converted to a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="61d18-152">Ainsi, le code `IQueryable` génère une requête unique qui n’est pas exécutée avant l’instruction suivante :</span><span class="sxs-lookup"><span data-stu-id="61d18-152">Therefore, the `IQueryable` code results in a single query that's not executed until the following statement:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_SortOnlyRtn)]

<span data-ttu-id="61d18-153">`OnGetAsync` peut contenir un grand nombre de colonnes triables.</span><span class="sxs-lookup"><span data-stu-id="61d18-153">`OnGetAsync` could get verbose with a large number of sortable columns.</span></span> <span data-ttu-id="61d18-154">Pour connaître les autres méthodes permettant de coder cette fonctionnalité, consultez [Utiliser du code dynamique LINQ pour simplifier le code](xref:data/ef-mvc/advanced#dynamic-linq) dans la version MVC de cette série de tutoriels.</span><span class="sxs-lookup"><span data-stu-id="61d18-154">For information about an alternative way to code this functionality, see [Use dynamic LINQ to simplify code](xref:data/ef-mvc/advanced#dynamic-linq) in the MVC version of this tutorial series.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a><span data-ttu-id="61d18-155">Ajouter des liens hypertexte d’en-tête de colonne à la page d’index des étudiants</span><span class="sxs-lookup"><span data-stu-id="61d18-155">Add column heading hyperlinks to the Student Index page</span></span>

<span data-ttu-id="61d18-156">Remplacez le code dans _Students/index.cshtml \*, par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="61d18-156">Replace the code in _Students/Index.cshtml\*, with the following code.</span></span> <span data-ttu-id="61d18-157">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="61d18-157">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml?highlight=5,8,17-19,22,25-27,33)]

<span data-ttu-id="61d18-158">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="61d18-158">The preceding code:</span></span>

* <span data-ttu-id="61d18-159">Ajoute des liens hypertexte aux en-têtes de colonne `LastName` et `EnrollmentDate`.</span><span class="sxs-lookup"><span data-stu-id="61d18-159">Adds hyperlinks to the `LastName` and `EnrollmentDate` column headings.</span></span>
* <span data-ttu-id="61d18-160">Utilise les informations contenues dans `NameSort` et `DateSort` pour définir des liens hypertexte avec les valeurs d’ordre de tri actuelles.</span><span class="sxs-lookup"><span data-stu-id="61d18-160">Uses the information in `NameSort` and `DateSort` to set up hyperlinks with the current sort order values.</span></span>
* <span data-ttu-id="61d18-161">Remplace l’en-tête Index de la page par l’en-tête Students.</span><span class="sxs-lookup"><span data-stu-id="61d18-161">Changes the page heading from Index to Students.</span></span>
* <span data-ttu-id="61d18-162">Remplace `Model.Student` par `Model.Students`.</span><span class="sxs-lookup"><span data-stu-id="61d18-162">Changes `Model.Student` to `Model.Students`.</span></span>

<span data-ttu-id="61d18-163">Pour vérifier que le tri fonctionne</span><span class="sxs-lookup"><span data-stu-id="61d18-163">To verify that sorting works:</span></span>

* <span data-ttu-id="61d18-164">Exécutez l’application et sélectionnez l’onglet **Students** .</span><span class="sxs-lookup"><span data-stu-id="61d18-164">Run the app and select the **Students** tab.</span></span>
* <span data-ttu-id="61d18-165">Cliquez sur les en-têtes de colonne.</span><span class="sxs-lookup"><span data-stu-id="61d18-165">Click the column headings.</span></span>

## <a name="add-filtering"></a><span data-ttu-id="61d18-166">Ajouter la fonctionnalité de filtrage</span><span class="sxs-lookup"><span data-stu-id="61d18-166">Add filtering</span></span>

<span data-ttu-id="61d18-167">Pour ajouter le filtrage à la page d’index des étudiants :</span><span class="sxs-lookup"><span data-stu-id="61d18-167">To add filtering to the Students Index page:</span></span>

* <span data-ttu-id="61d18-168">Une zone de texte et un bouton Envoyer sont ajoutés à la :::no-loc(Razor)::: page.</span><span class="sxs-lookup"><span data-stu-id="61d18-168">A text box and a submit button is added to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="61d18-169">La zone de texte fournit une chaîne de recherche sur le prénom ou le nom de famille.</span><span class="sxs-lookup"><span data-stu-id="61d18-169">The text box supplies a search string on the first or last name.</span></span>
* <span data-ttu-id="61d18-170">Le modèle de page est mis à jour pour utiliser la valeur de zone de texte.</span><span class="sxs-lookup"><span data-stu-id="61d18-170">The page model is updated to use the text box value.</span></span>

### <a name="update-the-ongetasync-method"></a><span data-ttu-id="61d18-171">Mettre à jour la méthode OnGetAsync</span><span class="sxs-lookup"><span data-stu-id="61d18-171">Update the OnGetAsync method</span></span>

<span data-ttu-id="61d18-172">Remplacez le code de *Students/Index.cshtml.cs* par le code suivant pour ajouter la fonctionnalité de filtrage :</span><span class="sxs-lookup"><span data-stu-id="61d18-172">Replace the code in *Students/Index.cshtml.cs* with the following code to add filtering:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml.cs?name=snippet_All&highlight=17,22,26-30)]

<span data-ttu-id="61d18-173">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="61d18-173">The preceding code:</span></span>

* <span data-ttu-id="61d18-174">Ajoute le paramètre `searchString` à la méthode `OnGetAsync`, et enregistre la valeur de paramètre dans la propriété `CurrentFilter`.</span><span class="sxs-lookup"><span data-stu-id="61d18-174">Adds the `searchString` parameter to the `OnGetAsync` method, and saves the parameter value in the `CurrentFilter` property.</span></span> <span data-ttu-id="61d18-175">La valeur de chaîne de recherche est reçue à partir d’une zone de texte qui est ajoutée dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="61d18-175">The search string value is received from a text box that's added in the next section.</span></span>
* <span data-ttu-id="61d18-176">Ajoute une clause `Where` à l’instruction LINQ.</span><span class="sxs-lookup"><span data-stu-id="61d18-176">Adds to the LINQ statement a `Where` clause.</span></span> <span data-ttu-id="61d18-177">La clause `Where` sélectionne uniquement les étudiants dont le prénom ou le nom de famille contient la chaîne de recherche.</span><span class="sxs-lookup"><span data-stu-id="61d18-177">The `Where` clause selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="61d18-178">L’instruction LINQ est exécutée uniquement s’il y a une valeur à rechercher.</span><span class="sxs-lookup"><span data-stu-id="61d18-178">The LINQ statement is executed only if there's a value to search for.</span></span>

### <a name="iqueryable-vs-ienumerable"></a><span data-ttu-id="61d18-179">IQueryable et IEnumerable</span><span class="sxs-lookup"><span data-stu-id="61d18-179">IQueryable vs. IEnumerable</span></span>

<span data-ttu-id="61d18-180">Le code appelle la méthode <xref:System.Linq.Queryable.Where%2A> de l’objet `IQueryable`, et le filtre est traité sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="61d18-180">The code calls the <xref:System.Linq.Queryable.Where%2A> method on an `IQueryable` object, and the filter is processed on the server.</span></span> <span data-ttu-id="61d18-181">Dans certains scénarios, l’application peut appeler la méthode `Where` en tant que méthode d’extension sur une collection en mémoire.</span><span class="sxs-lookup"><span data-stu-id="61d18-181">In some scenarios, the app might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="61d18-182">Par exemple, supposez que `_context.Students` passe de `DbSet` EF Core à une méthode de référentiel qui retourne une collection `IEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="61d18-182">For example, suppose `_context.Students` changes from EF Core `DbSet` to a repository method that returns an `IEnumerable` collection.</span></span> <span data-ttu-id="61d18-183">Le résultat serait normalement le même, mais dans certains cas il peut être différent.</span><span class="sxs-lookup"><span data-stu-id="61d18-183">The result would normally be the same but in some cases may be different.</span></span>

<span data-ttu-id="61d18-184">Par exemple, l’implémentation .NET Framework de `Contains` effectue par défaut une comparaison respectant la casse.</span><span class="sxs-lookup"><span data-stu-id="61d18-184">For example, the .NET Framework implementation of `Contains` performs a case-sensitive comparison by default.</span></span> <span data-ttu-id="61d18-185">Dans SQL Server, le respect de la casse de `Contains` est déterminé par le paramètre de classement de l’instance de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="61d18-185">In SQL Server, `Contains` case-sensitivity is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="61d18-186">Par défaut, SQL Server ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="61d18-186">SQL Server defaults to case-insensitive.</span></span> <span data-ttu-id="61d18-187">Par défaut, SQLite est sensible à la casse.</span><span class="sxs-lookup"><span data-stu-id="61d18-187">SQLite defaults to case-sensitive.</span></span> <span data-ttu-id="61d18-188">`ToUpper` peut être appelée pour que le test ne respecte pas la casse de manière explicite :</span><span class="sxs-lookup"><span data-stu-id="61d18-188">`ToUpper` could be called to make the test explicitly case-insensitive:</span></span>

```csharp
Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`
```

<span data-ttu-id="61d18-189">Le code précédent garantit que le filtre n’est pas sensible à la casse, même si la méthode `Where` est appelée sur un `IEnumerable` ou s’exécute sur SQLite.</span><span class="sxs-lookup"><span data-stu-id="61d18-189">The preceding code would ensure that the filter is case-insensitive even if the `Where` method is called on an `IEnumerable` or runs on SQLite.</span></span>

<span data-ttu-id="61d18-190">Quand `Contains` est appelée sur une collection `IEnumerable`, l’implémentation .NET Core est utilisée.</span><span class="sxs-lookup"><span data-stu-id="61d18-190">When `Contains` is called on an `IEnumerable` collection, the .NET Core implementation is used.</span></span> <span data-ttu-id="61d18-191">Quand `Contains` est appelée sur un objet `IQueryable`, l’implémentation de base de données est utilisée.</span><span class="sxs-lookup"><span data-stu-id="61d18-191">When `Contains` is called on an `IQueryable` object, the database implementation is used.</span></span>

<span data-ttu-id="61d18-192">Pour des raisons de performances, il est généralement préférable d’appeler `Contains` sur un `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="61d18-192">Calling `Contains` on an `IQueryable` is usually preferable for performance reasons.</span></span> <span data-ttu-id="61d18-193">Avec `IQueryable`, le filtrage est effectué par le serveur de base de données.</span><span class="sxs-lookup"><span data-stu-id="61d18-193">With `IQueryable`, the filtering is done by the database server.</span></span> <span data-ttu-id="61d18-194">Si un `IEnumerable` est créé en premier, toutes les lignes doivent être retournées à partir du serveur de base de données.</span><span class="sxs-lookup"><span data-stu-id="61d18-194">If an `IEnumerable` is created first, all the rows have to be returned from the database server.</span></span>

<span data-ttu-id="61d18-195">Il existe un coût en matière de performances en cas d’appel à `ToUpper`.</span><span class="sxs-lookup"><span data-stu-id="61d18-195">There's a performance penalty for calling `ToUpper`.</span></span> <span data-ttu-id="61d18-196">Le code `ToUpper` ajoute une fonction dans la clause WHERE de l’instruction TSQL SELECT.</span><span class="sxs-lookup"><span data-stu-id="61d18-196">The `ToUpper` code adds a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="61d18-197">La fonction ajoutée empêche l’optimiseur d’utiliser un index.</span><span class="sxs-lookup"><span data-stu-id="61d18-197">The added function prevents the optimizer from using an index.</span></span> <span data-ttu-id="61d18-198">Étant donné que SQL est installé sans respect de la casse, il est préférable d’éviter l’appel à `ToUpper` quand il n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="61d18-198">Given that SQL is installed as case-insensitive, it's best to avoid the `ToUpper` call when it's not needed.</span></span>

<span data-ttu-id="61d18-199">Pour plus d’informations, consultez [How to use case-insensitive query with Sqlite provider](https://github.com/aspnet/EntityFrameworkCore/issues/11414).</span><span class="sxs-lookup"><span data-stu-id="61d18-199">For more information, see [How to use case-insensitive query with Sqlite provider](https://github.com/aspnet/EntityFrameworkCore/issues/11414).</span></span>

### <a name="update-the-no-locrazor-page"></a><span data-ttu-id="61d18-200">Mettre à jour la :::no-loc(Razor)::: page</span><span class="sxs-lookup"><span data-stu-id="61d18-200">Update the :::no-loc(Razor)::: page</span></span>

<span data-ttu-id="61d18-201">Remplacez le code dans *pages/Students/index. cshtml* pour ajouter un bouton de **recherche** .</span><span class="sxs-lookup"><span data-stu-id="61d18-201">Replace the code in *Pages/Students/Index.cshtml* to add a **Search** button.</span></span>

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml?highlight=14-23)]

<span data-ttu-id="61d18-202">Le code précédent utilise le  [Tag Helper](xref:mvc/views/tag-helpers/intro)`<form>` pour ajouter le bouton et la zone de texte de recherche.</span><span class="sxs-lookup"><span data-stu-id="61d18-202">The preceding code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="61d18-203">Par défaut, le Tag Helper `<form>` envoie les données de formulaire avec un POST.</span><span class="sxs-lookup"><span data-stu-id="61d18-203">By default, the `<form>` tag helper submits form data with a POST.</span></span> <span data-ttu-id="61d18-204">Avec POST, les paramètres sont passés dans le corps du message HTTP et non dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="61d18-204">With POST, the parameters are passed in the HTTP message body and not in the URL.</span></span> <span data-ttu-id="61d18-205">Quand HTTP GET est utilisé, les données du formulaire sont transmises dans l’URL sous forme de chaînes de requête.</span><span class="sxs-lookup"><span data-stu-id="61d18-205">When HTTP GET is used, the form data is passed in the URL as query strings.</span></span> <span data-ttu-id="61d18-206">La transmission des données avec des chaînes de requête permet aux utilisateurs d’ajouter l’URL aux favoris.</span><span class="sxs-lookup"><span data-stu-id="61d18-206">Passing the data with query strings enables users to bookmark the URL.</span></span> <span data-ttu-id="61d18-207">Les [recommandations du W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) stipulent que GET doit être utilisé quand l’action ne produit pas de mise à jour.</span><span class="sxs-lookup"><span data-stu-id="61d18-207">The [W3C guidelines](https://www.w3.org/2001/tag/doc/whenToUseGet.html) recommend that GET should be used when the action doesn't result in an update.</span></span>

<span data-ttu-id="61d18-208">Testez l’application :</span><span class="sxs-lookup"><span data-stu-id="61d18-208">Test the app:</span></span>

* <span data-ttu-id="61d18-209">Sélectionnez l’onglet **Students** et entrez une chaîne de recherche.</span><span class="sxs-lookup"><span data-stu-id="61d18-209">Select the **Students** tab and enter a search string.</span></span> <span data-ttu-id="61d18-210">Si vous utilisez SQLite, le filtre n’est pas sensible à la casse seulement si vous avez implémenté le code `ToUpper` facultatif indiqué plus haut.</span><span class="sxs-lookup"><span data-stu-id="61d18-210">If you're using SQLite, the filter is case-insensitive only if you implemented the optional `ToUpper` code shown earlier.</span></span>

* <span data-ttu-id="61d18-211">Sélectionnez **Recherche** .</span><span class="sxs-lookup"><span data-stu-id="61d18-211">Select **Search** .</span></span>

<span data-ttu-id="61d18-212">Notez que l’URL contient la chaîne de recherche.</span><span class="sxs-lookup"><span data-stu-id="61d18-212">Notice that the URL contains the search string.</span></span> <span data-ttu-id="61d18-213">Exemple :</span><span class="sxs-lookup"><span data-stu-id="61d18-213">For example:</span></span>

```browser-address-bar
https://localhost:5001/Students?SearchString=an
```

<span data-ttu-id="61d18-214">Si la page est dans les favoris, le favori contient l’URL de la page et la chaîne de requête `SearchString`.</span><span class="sxs-lookup"><span data-stu-id="61d18-214">If the page is bookmarked, the bookmark contains the URL to the page and the `SearchString` query string.</span></span> <span data-ttu-id="61d18-215">`method="get"` dans la balise `form` est ce qui a provoqué la génération de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="61d18-215">The `method="get"` in the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="61d18-216">Actuellement, quand un lien de tri d’en-tête de colonne est sélectionné, la valeur du filtre de la zone **Search** est perdue.</span><span class="sxs-lookup"><span data-stu-id="61d18-216">Currently, when a column heading sort link is selected, the filter value from the **Search** box is lost.</span></span> <span data-ttu-id="61d18-217">La valeur de filtre perdue est corrigée dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="61d18-217">The lost filter value is fixed in the next section.</span></span>

## <a name="add-paging"></a><span data-ttu-id="61d18-218">Ajouter la fonctionnalité de pagination</span><span class="sxs-lookup"><span data-stu-id="61d18-218">Add paging</span></span>

<span data-ttu-id="61d18-219">Dans cette section, nous allons créer une classe `PaginatedList` pour prendre en charge la pagination.</span><span class="sxs-lookup"><span data-stu-id="61d18-219">In this section, a `PaginatedList` class is created to support paging.</span></span> <span data-ttu-id="61d18-220">La classe `PaginatedList` utilise des instructions `Skip` et `Take` pour filtrer les données sur le serveur au lieu de récupérer toutes les lignes de la table.</span><span class="sxs-lookup"><span data-stu-id="61d18-220">The `PaginatedList` class uses `Skip` and `Take` statements to filter data on the server instead of retrieving all rows of the table.</span></span> <span data-ttu-id="61d18-221">L’illustration suivante montre les boutons de pagination.</span><span class="sxs-lookup"><span data-stu-id="61d18-221">The following illustration shows the paging buttons.</span></span>

![Page d’index des étudiants avec liens de changement de page](sort-filter-page/_static/paging30.png)

### <a name="create-the-paginatedlist-class"></a><span data-ttu-id="61d18-223">Créer la classe PaginatedList</span><span class="sxs-lookup"><span data-stu-id="61d18-223">Create the PaginatedList class</span></span>

<span data-ttu-id="61d18-224">Dans le dossier du projet, créez `PaginatedList.cs` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="61d18-224">In the project folder, create `PaginatedList.cs` with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30/PaginatedList.cs)]

<span data-ttu-id="61d18-225">La méthode `CreateAsync` dans le code précédent prend la taille de page et le numéro de page, et applique les instructions `Skip` et `Take` appropriées au `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="61d18-225">The `CreateAsync` method in the preceding code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="61d18-226">Quand `ToListAsync` est appelée sur le `IQueryable`, elle retourne une liste contenant uniquement la page demandée.</span><span class="sxs-lookup"><span data-stu-id="61d18-226">When `ToListAsync` is called on the `IQueryable`, it returns a List containing only the requested page.</span></span> <span data-ttu-id="61d18-227">Les propriétés `HasPreviousPage` et `HasNextPage` sont utilisées pour activer ou désactiver les boutons de pagination **Previous** et **Next** .</span><span class="sxs-lookup"><span data-stu-id="61d18-227">The properties `HasPreviousPage` and `HasNextPage` are used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="61d18-228">La méthode `CreateAsync` est utilisée pour créer le `PaginatedList<T>`.</span><span class="sxs-lookup"><span data-stu-id="61d18-228">The `CreateAsync` method is used to create the `PaginatedList<T>`.</span></span> <span data-ttu-id="61d18-229">Un constructeur ne peut pas créer l’objet `PaginatedList<T>`, car les constructeurs ne peuvent pas exécuter du code asynchrone.</span><span class="sxs-lookup"><span data-stu-id="61d18-229">A constructor can't create the `PaginatedList<T>` object; constructors can't run asynchronous code.</span></span>

### <a name="add-paging-to-the-pagemodel-class"></a><span data-ttu-id="61d18-230">Ajouter la fonctionnalité de pagination à la classe PageModel</span><span class="sxs-lookup"><span data-stu-id="61d18-230">Add paging to the PageModel class</span></span>

<span data-ttu-id="61d18-231">Remplacez le code de *Students/Index.cshtml.cs* pour ajouter la fonctionnalité de pagination.</span><span class="sxs-lookup"><span data-stu-id="61d18-231">Replace the code in *Students/Index.cshtml.cs* to add paging.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Index.cshtml.cs?name=snippet_All&highlight=15-20,23-30,57-59)]

<span data-ttu-id="61d18-232">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="61d18-232">The preceding code:</span></span>

* <span data-ttu-id="61d18-233">Remplace le type `IList<Student>` de la propriété `Students` par le type `PaginatedList<Student>`.</span><span class="sxs-lookup"><span data-stu-id="61d18-233">Changes the type of the `Students` property from `IList<Student>` to `PaginatedList<Student>`.</span></span>
* <span data-ttu-id="61d18-234">Ajoute l’index de page, le `sortOrder` actuel et le `currentFilter` à la signature de méthode `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="61d18-234">Adds the page index, the current `sortOrder`, and the `currentFilter` to the `OnGetAsync` method signature.</span></span>
* <span data-ttu-id="61d18-235">Enregistre l’ordre de tri dans la `CurrentSort` propriété.</span><span class="sxs-lookup"><span data-stu-id="61d18-235">Saves the sort order in the `CurrentSort` property.</span></span>
* <span data-ttu-id="61d18-236">Rétablit la valeur 1 pour l’index de la page lorsqu’il existe une nouvelle chaîne de recherche.</span><span class="sxs-lookup"><span data-stu-id="61d18-236">Resets page index to 1 when there's a new search string.</span></span>
* <span data-ttu-id="61d18-237">Utilise la classe `PaginatedList` pour accéder aux entités d’étudiants.</span><span class="sxs-lookup"><span data-stu-id="61d18-237">Uses the `PaginatedList` class to get Student entities.</span></span>
* <span data-ttu-id="61d18-238">Définit `pageSize` sur 3.</span><span class="sxs-lookup"><span data-stu-id="61d18-238">Sets `pageSize` to 3.</span></span> <span data-ttu-id="61d18-239">Une application réelle utilise la [configuration](xref:fundamentals/configuration/index) pour définir la valeur de taille de page.</span><span class="sxs-lookup"><span data-stu-id="61d18-239">A real app would use [Configuration](xref:fundamentals/configuration/index) to set the page size value.</span></span>

<span data-ttu-id="61d18-240">Tous les paramètres reçus par `OnGetAsync` sont Null si :</span><span class="sxs-lookup"><span data-stu-id="61d18-240">All the parameters that `OnGetAsync` receives are null when:</span></span>

* <span data-ttu-id="61d18-241">La page est appelée à partir du lien **Students** .</span><span class="sxs-lookup"><span data-stu-id="61d18-241">The page is called from the **Students** link.</span></span>
* <span data-ttu-id="61d18-242">L’utilisateur n’a pas cliqué sur un lien de pagination ou de tri.</span><span class="sxs-lookup"><span data-stu-id="61d18-242">The user hasn't clicked a paging or sorting link.</span></span>

<span data-ttu-id="61d18-243">Quand l’utilisateur clique sur un lien de pagination, la variable d’index de page contient le numéro de page à afficher.</span><span class="sxs-lookup"><span data-stu-id="61d18-243">When a paging link is clicked, the page index variable contains the page number to display.</span></span>

<span data-ttu-id="61d18-244">La `CurrentSort` propriété fournit la :::no-loc(Razor)::: page avec l’ordre de tri actuel.</span><span class="sxs-lookup"><span data-stu-id="61d18-244">The `CurrentSort` property provides the :::no-loc(Razor)::: Page with the current sort order.</span></span> <span data-ttu-id="61d18-245">L’ordre de tri actuel doit être inclus dans les liens de pagination afin de conserver l’ordre de tri lors de la pagination.</span><span class="sxs-lookup"><span data-stu-id="61d18-245">The current sort order must be included in the paging links to keep the sort order while paging.</span></span>

<span data-ttu-id="61d18-246">La `CurrentFilter` propriété fournit la :::no-loc(Razor)::: page avec la chaîne de filtre actuelle.</span><span class="sxs-lookup"><span data-stu-id="61d18-246">The `CurrentFilter` property provides the :::no-loc(Razor)::: Page with the current filter string.</span></span> <span data-ttu-id="61d18-247">La valeur `CurrentFilter` :</span><span class="sxs-lookup"><span data-stu-id="61d18-247">The `CurrentFilter` value:</span></span>

* <span data-ttu-id="61d18-248">Doit être incluse dans les liens de pagination afin de conserver les paramètres de filtre lors de la pagination.</span><span class="sxs-lookup"><span data-stu-id="61d18-248">Must be included in the paging links in order to maintain the filter settings during paging.</span></span>
* <span data-ttu-id="61d18-249">Doit être restaurée à la zone de texte quand la page est réaffichée.</span><span class="sxs-lookup"><span data-stu-id="61d18-249">Must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="61d18-250">Si la chaîne de recherche est modifiée pendant la pagination, la page est réinitialisée à 1.</span><span class="sxs-lookup"><span data-stu-id="61d18-250">If the search string is changed while paging, the page is reset to 1.</span></span> <span data-ttu-id="61d18-251">La page doit être réinitialisée à 1, car le nouveau filtre peut entraîner l’affichage de données différentes.</span><span class="sxs-lookup"><span data-stu-id="61d18-251">The page has to be reset to 1 because the new filter can result in different data to display.</span></span> <span data-ttu-id="61d18-252">Quand une valeur de recherche est entrée et que le bouton **Submit** est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="61d18-252">When a search value is entered and **Submit** is selected:</span></span>

  * <span data-ttu-id="61d18-253">La chaîne de recherche est changée.</span><span class="sxs-lookup"><span data-stu-id="61d18-253">The search string is changed.</span></span>
  * <span data-ttu-id="61d18-254">Le paramètre `searchString` n’est pas null.</span><span class="sxs-lookup"><span data-stu-id="61d18-254">The `searchString` parameter isn't null.</span></span>

  <span data-ttu-id="61d18-255">La méthode `PaginatedList.CreateAsync` convertit la requête d’étudiant en une seule page d’étudiants dans un type de collection qui prend en charge la pagination.</span><span class="sxs-lookup"><span data-stu-id="61d18-255">The `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="61d18-256">Cette seule page d’élèves est transmise à la :::no-loc(Razor)::: page.</span><span class="sxs-lookup"><span data-stu-id="61d18-256">That single page of students is passed to the :::no-loc(Razor)::: Page.</span></span>

  <span data-ttu-id="61d18-257">Les deux points d’interrogation situés après `pageIndex` dans l’appel `PaginatedList.CreateAsync` représentent l’[opérateur de fusion avec valeur Null](/dotnet/csharp/language-reference/operators/null-conditional-operator).</span><span class="sxs-lookup"><span data-stu-id="61d18-257">The two question marks after `pageIndex` in the `PaginatedList.CreateAsync` call represent the [null-coalescing operator](/dotnet/csharp/language-reference/operators/null-conditional-operator).</span></span> <span data-ttu-id="61d18-258">L’opérateur de fusion de Null définit une valeur par défaut pour un type nullable.</span><span class="sxs-lookup"><span data-stu-id="61d18-258">The null-coalescing operator defines a default value for a nullable type.</span></span> <span data-ttu-id="61d18-259">L’expression `pageIndex ?? 1` retourne la valeur de `pageIndex` si elle a une valeur ; sinon, elle retourne la valeur 1.</span><span class="sxs-lookup"><span data-stu-id="61d18-259">The expression `pageIndex ?? 1` returns the value of `pageIndex` if it has a value, otherwise, it returns 1.</span></span>

### <a name="add-paging-links-to-the-no-locrazor-page"></a><span data-ttu-id="61d18-260">Ajouter des liens de pagination à la :::no-loc(Razor)::: page</span><span class="sxs-lookup"><span data-stu-id="61d18-260">Add paging links to the :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="61d18-261">Remplacez le code dans *students/index. cshtml* par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="61d18-261">Replace the code in *Students/Index.cshtml* with the following code.</span></span> <span data-ttu-id="61d18-262">Les modifications apparaissent en surbrillance :</span><span class="sxs-lookup"><span data-stu-id="61d18-262">The changes are highlighted:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?highlight=29-32,38-41,69-87)]

<span data-ttu-id="61d18-263">Les liens d’en-tête de colonne utilisent la chaîne de requête pour passer la chaîne de recherche actuelle à la méthode `OnGetAsync` :</span><span class="sxs-lookup"><span data-stu-id="61d18-263">The column header links use the query string to pass the current search string to the `OnGetAsync` method:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=29-32)]

<span data-ttu-id="61d18-264">Les boutons de changement de page sont affichés par des Tag Helpers :</span><span class="sxs-lookup"><span data-stu-id="61d18-264">The paging buttons are displayed by tag helpers:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=73-87)]

<span data-ttu-id="61d18-265">Exécutez l’application et accédez à la page des étudiants.</span><span class="sxs-lookup"><span data-stu-id="61d18-265">Run the app and navigate to the students page.</span></span>

* <span data-ttu-id="61d18-266">Pour vérifier que la pagination fonctionne, cliquez sur les liens de pagination dans différents ordres de tri.</span><span class="sxs-lookup"><span data-stu-id="61d18-266">To make sure paging works, click the paging links in different sort orders.</span></span>
* <span data-ttu-id="61d18-267">Pour vérifier que la pagination fonctionne correctement avec le tri et le filtrage, entrez une chaîne de recherche et essayez de changer de page.</span><span class="sxs-lookup"><span data-stu-id="61d18-267">To verify that paging works correctly with sorting and filtering, enter a search string and try paging.</span></span>

![Page d’index des étudiants avec liens de pagination](sort-filter-page/_static/paging30.png)

## <a name="add-grouping"></a><span data-ttu-id="61d18-269">Ajouter la fonctionnalité de regroupement</span><span class="sxs-lookup"><span data-stu-id="61d18-269">Add grouping</span></span>

<span data-ttu-id="61d18-270">Cette section crée la page About (À propos) qui indique le nombre d’étudiants inscrits pour chaque date d’inscription.</span><span class="sxs-lookup"><span data-stu-id="61d18-270">This section creates an About page that displays how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="61d18-271">La mise à jour utilise le regroupement et comprend les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="61d18-271">The update uses grouping and includes the following steps:</span></span>

* <span data-ttu-id="61d18-272">Créez un modèle de vue pour les données utilisées par la page **about** .</span><span class="sxs-lookup"><span data-stu-id="61d18-272">Create a view model for the data used by the **About** page.</span></span>
* <span data-ttu-id="61d18-273">Mettre à jour la page About pour utiliser le modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="61d18-273">Update the About page to use the view model.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="61d18-274">Créer le modèle d’affichage</span><span class="sxs-lookup"><span data-stu-id="61d18-274">Create the view model</span></span>

<span data-ttu-id="61d18-275">Créez un dossier *Models/SchoolViewModels* .</span><span class="sxs-lookup"><span data-stu-id="61d18-275">Create a *Models/SchoolViewModels* folder.</span></span>

<span data-ttu-id="61d18-276">Créez *SchoolViewModels/EnrollmentDateGroup.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="61d18-276">Create *SchoolViewModels/EnrollmentDateGroup.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="create-the-no-locrazor-page"></a><span data-ttu-id="61d18-277">Créer la :::no-loc(Razor)::: page</span><span class="sxs-lookup"><span data-stu-id="61d18-277">Create the :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="61d18-278">Créez un fichier *Pages/About.cshtml* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="61d18-278">Create a *Pages/About.cshtml* file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/About.cshtml)]

### <a name="create-the-page-model"></a><span data-ttu-id="61d18-279">Créer le modèle de page</span><span class="sxs-lookup"><span data-stu-id="61d18-279">Create the page model</span></span>

<span data-ttu-id="61d18-280">Mettez à jour le fichier *Pages/About.cshtml.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="61d18-280">Update the *Pages/About.cshtml.cs* file with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/About.cshtml.cs)]

<span data-ttu-id="61d18-281">L’instruction LINQ regroupe les entités Student par date d’inscription, calcule le nombre d’entités dans chaque groupe et stocke les résultats dans une collection d’objets de modèle de vue `EnrollmentDateGroup`.</span><span class="sxs-lookup"><span data-stu-id="61d18-281">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

<span data-ttu-id="61d18-282">Exécutez l’application et accédez à la page About.</span><span class="sxs-lookup"><span data-stu-id="61d18-282">Run the app and navigate to the About page.</span></span> <span data-ttu-id="61d18-283">Le nombre d’étudiants pour chaque date d’inscription s’affiche dans une table.</span><span class="sxs-lookup"><span data-stu-id="61d18-283">The count of students for each enrollment date is displayed in a table.</span></span>

![Page About](sort-filter-page/_static/about30.png)

## <a name="next-steps"></a><span data-ttu-id="61d18-285">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="61d18-285">Next steps</span></span>

<span data-ttu-id="61d18-286">Dans le didacticiel suivant, l’application utilise des migrations pour mettre à jour le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="61d18-286">In the next tutorial, the app uses migrations to update the data model.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="61d18-287">[Didacticiel précédent](xref:data/ef-rp/crud) 
>  [Didacticiel suivant](xref:data/ef-rp/migrations)</span><span class="sxs-lookup"><span data-stu-id="61d18-287">[Previous tutorial](xref:data/ef-rp/crud)
[Next tutorial](xref:data/ef-rp/migrations)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="61d18-288">Dans ce didacticiel, nous allons ajouter des fonctionnalités de tri, de filtrage, de regroupement et de pagination.</span><span class="sxs-lookup"><span data-stu-id="61d18-288">In this tutorial, sorting, filtering, grouping, and paging, functionality is added.</span></span>

<span data-ttu-id="61d18-289">L’illustration suivante présente une page complète.</span><span class="sxs-lookup"><span data-stu-id="61d18-289">The following illustration shows a completed page.</span></span> <span data-ttu-id="61d18-290">Les en-têtes de colonne sont des liens hypertexte permettant de trier la colonne.</span><span class="sxs-lookup"><span data-stu-id="61d18-290">The column headings are clickable links to sort the column.</span></span> <span data-ttu-id="61d18-291">Un clic sur un en-tête de colonne permet de changer l’ordre de tri (croissant ou décroissant).</span><span class="sxs-lookup"><span data-stu-id="61d18-291">Clicking a column heading repeatedly switches between ascending and descending sort order.</span></span>

![Page d’index des étudiants](sort-filter-page/_static/paging.png)

<span data-ttu-id="61d18-293">Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez [l’application terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="61d18-293">If you run into problems you can't solve, download the [completed app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="add-sorting-to-the-index-page"></a><span data-ttu-id="61d18-294">Ajouter le tri à la page Index</span><span class="sxs-lookup"><span data-stu-id="61d18-294">Add sorting to the Index page</span></span>

<span data-ttu-id="61d18-295">Ajoutez des chaînes au *students/index. cshtml. cs* `PageModel` pour contenir les paramètres de tri :</span><span class="sxs-lookup"><span data-stu-id="61d18-295">Add strings to the *Students/Index.cshtml.cs* `PageModel` to contain the sorting parameters:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

<span data-ttu-id="61d18-296">Mettez à jour le Student */index. cshtml. cs* `OnGetAsync` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="61d18-296">Update the *Students/Index.cshtml.cs* `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

<span data-ttu-id="61d18-297">Le code précédent reçoit un paramètre `sortOrder` à partir de la chaîne de requête dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="61d18-297">The preceding code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="61d18-298">L’URL (y compris la chaîne de requête) est générée par le [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)</span><span class="sxs-lookup"><span data-stu-id="61d18-298">The URL (including the query string) is generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)</span></span>

<span data-ttu-id="61d18-299">Le paramètre `sortOrder` est « Name » ou « Date ».</span><span class="sxs-lookup"><span data-stu-id="61d18-299">The `sortOrder` parameter is either "Name" or "Date."</span></span> <span data-ttu-id="61d18-300">Le paramètre `sortOrder` peut être suivi de « _desc » pour spécifier l’ordre décroissant.</span><span class="sxs-lookup"><span data-stu-id="61d18-300">The `sortOrder` parameter is optionally followed by "_desc" to specify descending order.</span></span> <span data-ttu-id="61d18-301">L’ordre de tri par défaut est croissant.</span><span class="sxs-lookup"><span data-stu-id="61d18-301">The default sort order is ascending.</span></span>

<span data-ttu-id="61d18-302">Quand la page Index est demandée à partir du lien **Students** , il n’existe aucune chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="61d18-302">When the Index page is requested from the **Students** link, there's no query string.</span></span> <span data-ttu-id="61d18-303">Les étudiants sont affichés par nom de famille dans l’ordre croissant.</span><span class="sxs-lookup"><span data-stu-id="61d18-303">The students are displayed in ascending order by last name.</span></span> <span data-ttu-id="61d18-304">Le tri croissant par nom de famille est la valeur par défaut dans l’instruction `switch`.</span><span class="sxs-lookup"><span data-stu-id="61d18-304">Ascending order by last name is the default (fall-through case) in the `switch` statement.</span></span> <span data-ttu-id="61d18-305">Quand l’utilisateur clique sur un lien d’en-tête de colonne, la valeur `sortOrder` appropriée est fournie dans la valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="61d18-305">When the user clicks a column heading link, the appropriate `sortOrder` value is provided in the query string value.</span></span>

<span data-ttu-id="61d18-306">`NameSort` et `DateSort` sont utilisés par la :::no-loc(Razor)::: page pour configurer les liens hypertexte d’en-tête de colonne avec les valeurs de chaîne de requête appropriées :</span><span class="sxs-lookup"><span data-stu-id="61d18-306">`NameSort` and `DateSort` are used by the :::no-loc(Razor)::: Page to configure the column heading hyperlinks with the appropriate query string values:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="61d18-307">Le code suivant contient [l’opérateur ?:](/dotnet/csharp/language-reference/operators/conditional-operator) conditionnel C# :</span><span class="sxs-lookup"><span data-stu-id="61d18-307">The following code contains the C# conditional [?: operator](/dotnet/csharp/language-reference/operators/conditional-operator):</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

<span data-ttu-id="61d18-308">La première ligne indique que quand `sortOrder` est null ou vide, `NameSort` prend la valeur « name_desc ».</span><span class="sxs-lookup"><span data-stu-id="61d18-308">The first line specifies that when `sortOrder` is null or empty, `NameSort` is set to "name_desc."</span></span> <span data-ttu-id="61d18-309">Si `sortOrder` n’est **pas** null ou vide, `NameSort` prend pour valeur une chaîne vide.</span><span class="sxs-lookup"><span data-stu-id="61d18-309">If `sortOrder` is **not** null or empty, `NameSort` is set to an empty string.</span></span>

<span data-ttu-id="61d18-310">`?: operator` est également appelé opérateur ternaire.</span><span class="sxs-lookup"><span data-stu-id="61d18-310">The `?: operator` is also known as the ternary operator.</span></span>

<span data-ttu-id="61d18-311">Ces deux instructions permettent à la page de définir les liens hypertexte d’en-tête de colonne comme suit :</span><span class="sxs-lookup"><span data-stu-id="61d18-311">These two statements enable the page to set the column heading hyperlinks as follows:</span></span>

| <span data-ttu-id="61d18-312">Ordre de tri actuel</span><span class="sxs-lookup"><span data-stu-id="61d18-312">Current sort order</span></span> | <span data-ttu-id="61d18-313">Lien hypertexte Nom de famille</span><span class="sxs-lookup"><span data-stu-id="61d18-313">Last Name Hyperlink</span></span> | <span data-ttu-id="61d18-314">Lien hypertexte Date</span><span class="sxs-lookup"><span data-stu-id="61d18-314">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="61d18-315">Nom de famille croissant</span><span class="sxs-lookup"><span data-stu-id="61d18-315">Last Name ascending</span></span> | <span data-ttu-id="61d18-316">descending</span><span class="sxs-lookup"><span data-stu-id="61d18-316">descending</span></span>        | <span data-ttu-id="61d18-317">ascending</span><span class="sxs-lookup"><span data-stu-id="61d18-317">ascending</span></span>      |
| <span data-ttu-id="61d18-318">Nom de famille décroissant</span><span class="sxs-lookup"><span data-stu-id="61d18-318">Last Name descending</span></span> | <span data-ttu-id="61d18-319">ascending</span><span class="sxs-lookup"><span data-stu-id="61d18-319">ascending</span></span>           | <span data-ttu-id="61d18-320">ascending</span><span class="sxs-lookup"><span data-stu-id="61d18-320">ascending</span></span>      |
| <span data-ttu-id="61d18-321">Date croissante</span><span class="sxs-lookup"><span data-stu-id="61d18-321">Date ascending</span></span>       | <span data-ttu-id="61d18-322">ascending</span><span class="sxs-lookup"><span data-stu-id="61d18-322">ascending</span></span>           | <span data-ttu-id="61d18-323">descending</span><span class="sxs-lookup"><span data-stu-id="61d18-323">descending</span></span>     |
| <span data-ttu-id="61d18-324">Date décroissante</span><span class="sxs-lookup"><span data-stu-id="61d18-324">Date descending</span></span>      | <span data-ttu-id="61d18-325">ascending</span><span class="sxs-lookup"><span data-stu-id="61d18-325">ascending</span></span>           | <span data-ttu-id="61d18-326">ascending</span><span class="sxs-lookup"><span data-stu-id="61d18-326">ascending</span></span>      |

<span data-ttu-id="61d18-327">La méthode utilise LINQ to Entities pour spécifier la colonne d’après laquelle effectuer le tri.</span><span class="sxs-lookup"><span data-stu-id="61d18-327">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="61d18-328">Le code initialise un `IQueryable<Student>` avant l’instruction switch, et le modifie dans l’instruction switch :</span><span class="sxs-lookup"><span data-stu-id="61d18-328">The code initializes an `IQueryable<Student>` before the switch statement, and modifies it in the switch statement:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 <span data-ttu-id="61d18-329">Quand un `IQueryable` est créé ou modifié, aucune requête n’est envoyée à la base de données.</span><span class="sxs-lookup"><span data-stu-id="61d18-329">When an`IQueryable` is created or modified, no query is sent to the database.</span></span> <span data-ttu-id="61d18-330">La requête n’est pas exécutée tant que l’objet `IQueryable` n’a pas été converti en collection.</span><span class="sxs-lookup"><span data-stu-id="61d18-330">The query isn't executed until the `IQueryable` object is converted into a collection.</span></span> <span data-ttu-id="61d18-331">Les `IQueryable` sont convertis en collection en appelant une méthode telle que `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="61d18-331">`IQueryable` are converted to a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="61d18-332">Ainsi, le code `IQueryable` génère une requête unique qui n’est pas exécutée avant l’instruction suivante :</span><span class="sxs-lookup"><span data-stu-id="61d18-332">Therefore, the `IQueryable` code results in a single query that's not executed until the following statement:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

<span data-ttu-id="61d18-333">`OnGetAsync` peut contenir un grand nombre de colonnes triables.</span><span class="sxs-lookup"><span data-stu-id="61d18-333">`OnGetAsync` could get verbose with a large number of sortable columns.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a><span data-ttu-id="61d18-334">Ajouter des liens hypertexte d’en-tête de colonne à la page d’index des étudiants</span><span class="sxs-lookup"><span data-stu-id="61d18-334">Add column heading hyperlinks to the Student Index page</span></span>

<span data-ttu-id="61d18-335">Remplacez le code dans *Students/Index.cshtml* par le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="61d18-335">Replace the code in *Students/Index.cshtml* , with the following highlighted code:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

<span data-ttu-id="61d18-336">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="61d18-336">The preceding code:</span></span>

* <span data-ttu-id="61d18-337">Ajoute des liens hypertexte aux en-têtes de colonne `LastName` et `EnrollmentDate`.</span><span class="sxs-lookup"><span data-stu-id="61d18-337">Adds hyperlinks to the `LastName` and `EnrollmentDate` column headings.</span></span>
* <span data-ttu-id="61d18-338">Utilise les informations contenues dans `NameSort` et `DateSort` pour définir des liens hypertexte avec les valeurs d’ordre de tri actuelles.</span><span class="sxs-lookup"><span data-stu-id="61d18-338">Uses the information in `NameSort` and `DateSort` to set up hyperlinks with the current sort order values.</span></span>

<span data-ttu-id="61d18-339">Pour vérifier que le tri fonctionne</span><span class="sxs-lookup"><span data-stu-id="61d18-339">To verify that sorting works:</span></span>

* <span data-ttu-id="61d18-340">Exécutez l’application et sélectionnez l’onglet **Students** .</span><span class="sxs-lookup"><span data-stu-id="61d18-340">Run the app and select the **Students** tab.</span></span>
* <span data-ttu-id="61d18-341">Cliquez sur **Last Name** .</span><span class="sxs-lookup"><span data-stu-id="61d18-341">Click **Last Name** .</span></span>
* <span data-ttu-id="61d18-342">Cliquez sur **Enrollment Date** .</span><span class="sxs-lookup"><span data-stu-id="61d18-342">Click **Enrollment Date** .</span></span>

<span data-ttu-id="61d18-343">Pour mieux comprendre le fonctionnement du code</span><span class="sxs-lookup"><span data-stu-id="61d18-343">To get a better understanding of the code:</span></span>

* <span data-ttu-id="61d18-344">Dans *Students/Index.cshtml.cs* , définissez un point d’arrêt sur `switch (sortOrder)`.</span><span class="sxs-lookup"><span data-stu-id="61d18-344">In *Students/Index.cshtml.cs* , set a breakpoint on `switch (sortOrder)`.</span></span>
* <span data-ttu-id="61d18-345">Ajoutez un espion pour `NameSort` et `DateSort`.</span><span class="sxs-lookup"><span data-stu-id="61d18-345">Add a watch for `NameSort` and `DateSort`.</span></span>
* <span data-ttu-id="61d18-346">Dans *Students/Index.cshtml* , définissez un point d’arrêt sur `@Html.DisplayNameFor(model => model.Student[0].LastName)`.</span><span class="sxs-lookup"><span data-stu-id="61d18-346">In *Students/Index.cshtml* , set a breakpoint on `@Html.DisplayNameFor(model => model.Student[0].LastName)`.</span></span>

<span data-ttu-id="61d18-347">Effectuez un pas à pas détaillé dans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="61d18-347">Step through the debugger.</span></span>

## <a name="add-a-search-box-to-the-students-index-page"></a><span data-ttu-id="61d18-348">Ajouter une zone de recherche à la page d’index des étudiants</span><span class="sxs-lookup"><span data-stu-id="61d18-348">Add a Search Box to the Students Index page</span></span>

<span data-ttu-id="61d18-349">Pour ajouter le filtrage à la page d’index des étudiants :</span><span class="sxs-lookup"><span data-stu-id="61d18-349">To add filtering to the Students Index page:</span></span>

* <span data-ttu-id="61d18-350">Une zone de texte et un bouton Envoyer sont ajoutés à la :::no-loc(Razor)::: page.</span><span class="sxs-lookup"><span data-stu-id="61d18-350">A text box and a submit button is added to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="61d18-351">La zone de texte fournit une chaîne de recherche sur le prénom ou le nom de famille.</span><span class="sxs-lookup"><span data-stu-id="61d18-351">The text box supplies a search string on the first or last name.</span></span>
* <span data-ttu-id="61d18-352">Le modèle de page est mis à jour pour utiliser la valeur de zone de texte.</span><span class="sxs-lookup"><span data-stu-id="61d18-352">The page model is updated to use the text box value.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="61d18-353">Ajouter la fonctionnalité de filtrage à la méthode Index</span><span class="sxs-lookup"><span data-stu-id="61d18-353">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="61d18-354">Mettez à jour le Student */index. cshtml. cs* `OnGetAsync` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="61d18-354">Update the *Students/Index.cshtml.cs* `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="61d18-355">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="61d18-355">The preceding code:</span></span>

* <span data-ttu-id="61d18-356">Ajoute le paramètre `searchString` à la méthode `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="61d18-356">Adds the `searchString` parameter to the `OnGetAsync` method.</span></span> <span data-ttu-id="61d18-357">La valeur de chaîne de recherche est reçue à partir d’une zone de texte qui est ajoutée dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="61d18-357">The search string value is received from a text box that's added in the next section.</span></span>
* <span data-ttu-id="61d18-358">A ajouté une clause `Where` à l’instruction LINQ.</span><span class="sxs-lookup"><span data-stu-id="61d18-358">Added to the LINQ statement a `Where` clause.</span></span> <span data-ttu-id="61d18-359">La clause `Where` sélectionne uniquement les étudiants dont le prénom ou le nom de famille contient la chaîne de recherche.</span><span class="sxs-lookup"><span data-stu-id="61d18-359">The `Where` clause selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="61d18-360">L’instruction LINQ est exécutée uniquement s’il y a une valeur à rechercher.</span><span class="sxs-lookup"><span data-stu-id="61d18-360">The LINQ statement is executed only if there's a value to search for.</span></span>

<span data-ttu-id="61d18-361">Remarque : Le code précédent appelle la méthode `Where` sur un objet `IQueryable`, et le filtre est traité sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="61d18-361">Note: The preceding code calls the `Where` method on an `IQueryable` object, and the filter is processed on the server.</span></span> <span data-ttu-id="61d18-362">Dans certains scénarios, l’application peut appeler la méthode `Where` en tant que méthode d’extension sur une collection en mémoire.</span><span class="sxs-lookup"><span data-stu-id="61d18-362">In some scenarios, the app might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="61d18-363">Par exemple, supposez que `_context.Students` passe de `DbSet` EF Core à une méthode de référentiel qui retourne une collection `IEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="61d18-363">For example, suppose `_context.Students` changes from EF Core `DbSet` to a repository method that returns an `IEnumerable` collection.</span></span> <span data-ttu-id="61d18-364">Le résultat serait normalement le même, mais dans certains cas il peut être différent.</span><span class="sxs-lookup"><span data-stu-id="61d18-364">The result would normally be the same but in some cases may be different.</span></span>

<span data-ttu-id="61d18-365">Par exemple, l’implémentation .NET Framework de `Contains` effectue par défaut une comparaison respectant la casse.</span><span class="sxs-lookup"><span data-stu-id="61d18-365">For example, the .NET Framework implementation of `Contains` performs a case-sensitive comparison by default.</span></span> <span data-ttu-id="61d18-366">Dans SQL Server, le respect de la casse de `Contains` est déterminé par le paramètre de classement de l’instance de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="61d18-366">In SQL Server, `Contains` case-sensitivity is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="61d18-367">Par défaut, SQL Server ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="61d18-367">SQL Server defaults to case-insensitive.</span></span> <span data-ttu-id="61d18-368">`ToUpper` peut être appelée pour que le test ne respecte pas la casse de manière explicite :</span><span class="sxs-lookup"><span data-stu-id="61d18-368">`ToUpper` could be called to make the test explicitly case-insensitive:</span></span>

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

<span data-ttu-id="61d18-369">Le code précédent garantit que les résultats ne respectent pas la casse si le code change et utilise `IEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="61d18-369">The preceding code would ensure that results are case-insensitive if the code changes to use `IEnumerable`.</span></span> <span data-ttu-id="61d18-370">Quand `Contains` est appelée sur une collection `IEnumerable`, l’implémentation .NET Core est utilisée.</span><span class="sxs-lookup"><span data-stu-id="61d18-370">When `Contains` is called on an `IEnumerable` collection, the .NET Core implementation is used.</span></span> <span data-ttu-id="61d18-371">Quand `Contains` est appelée sur un objet `IQueryable`, l’implémentation de base de données est utilisée.</span><span class="sxs-lookup"><span data-stu-id="61d18-371">When `Contains` is called on an `IQueryable` object, the database implementation is used.</span></span> <span data-ttu-id="61d18-372">Retourner un `IEnumerable` à partir d’un référentiel peut entraîner une dégradation significative des performances :</span><span class="sxs-lookup"><span data-stu-id="61d18-372">Returning an `IEnumerable` from a repository can have a significant performance penalty:</span></span>

1. <span data-ttu-id="61d18-373">Toutes les lignes sont retournées à partir du serveur de base de données.</span><span class="sxs-lookup"><span data-stu-id="61d18-373">All the rows are returned from the DB server.</span></span>
1. <span data-ttu-id="61d18-374">Le filtre est appliqué à toutes les lignes retournées dans l’application.</span><span class="sxs-lookup"><span data-stu-id="61d18-374">The filter is applied to all the returned rows in the application.</span></span>

<span data-ttu-id="61d18-375">Il existe un coût en matière de performances en cas d’appel à `ToUpper`.</span><span class="sxs-lookup"><span data-stu-id="61d18-375">There's a performance penalty for calling `ToUpper`.</span></span> <span data-ttu-id="61d18-376">Le code `ToUpper` ajoute une fonction dans la clause WHERE de l’instruction TSQL SELECT.</span><span class="sxs-lookup"><span data-stu-id="61d18-376">The `ToUpper` code adds a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="61d18-377">La fonction ajoutée empêche l’optimiseur d’utiliser un index.</span><span class="sxs-lookup"><span data-stu-id="61d18-377">The added function prevents the optimizer from using an index.</span></span> <span data-ttu-id="61d18-378">Étant donné que SQL est installé sans respect de la casse, il est préférable d’éviter l’appel à `ToUpper` quand il n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="61d18-378">Given that SQL is installed as case-insensitive, it's best to avoid the `ToUpper` call when it's not needed.</span></span>

### <a name="add-a-search-box-to-the-student-index-page"></a><span data-ttu-id="61d18-379">Ajouter une zone de recherche à la page d’index des étudiants</span><span class="sxs-lookup"><span data-stu-id="61d18-379">Add a Search Box to the Student Index page</span></span>

<span data-ttu-id="61d18-380">Dans *Pages/Students/Index.cshtml* , ajoutez le code en surbrillance suivant pour créer un bouton **Search** et le chrome assorti.</span><span class="sxs-lookup"><span data-stu-id="61d18-380">In *Pages/Students/Index.cshtml* , add the following highlighted code to create a **Search** button and assorted chrome.</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

<span data-ttu-id="61d18-381">Le code précédent utilise le  [Tag Helper](xref:mvc/views/tag-helpers/intro)`<form>` pour ajouter le bouton et la zone de texte de recherche.</span><span class="sxs-lookup"><span data-stu-id="61d18-381">The preceding code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="61d18-382">Par défaut, le Tag Helper `<form>` envoie les données de formulaire avec un POST.</span><span class="sxs-lookup"><span data-stu-id="61d18-382">By default, the `<form>` tag helper submits form data with a POST.</span></span> <span data-ttu-id="61d18-383">Avec POST, les paramètres sont passés dans le corps du message HTTP et non dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="61d18-383">With POST, the parameters are passed in the HTTP message body and not in the URL.</span></span> <span data-ttu-id="61d18-384">Quand HTTP GET est utilisé, les données du formulaire sont transmises dans l’URL sous forme de chaînes de requête.</span><span class="sxs-lookup"><span data-stu-id="61d18-384">When HTTP GET is used, the form data is passed in the URL as query strings.</span></span> <span data-ttu-id="61d18-385">La transmission des données avec des chaînes de requête permet aux utilisateurs d’ajouter l’URL aux favoris.</span><span class="sxs-lookup"><span data-stu-id="61d18-385">Passing the data with query strings enables users to bookmark the URL.</span></span> <span data-ttu-id="61d18-386">Les [recommandations du W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) stipulent que GET doit être utilisé quand l’action ne produit pas de mise à jour.</span><span class="sxs-lookup"><span data-stu-id="61d18-386">The [W3C guidelines](https://www.w3.org/2001/tag/doc/whenToUseGet.html) recommend that GET should be used when the action doesn't result in an update.</span></span>

<span data-ttu-id="61d18-387">Testez l’application :</span><span class="sxs-lookup"><span data-stu-id="61d18-387">Test the app:</span></span>

* <span data-ttu-id="61d18-388">Sélectionnez l’onglet **Students** et entrez une chaîne de recherche.</span><span class="sxs-lookup"><span data-stu-id="61d18-388">Select the **Students** tab and enter a search string.</span></span>
* <span data-ttu-id="61d18-389">Sélectionnez **Recherche** .</span><span class="sxs-lookup"><span data-stu-id="61d18-389">Select **Search** .</span></span>

<span data-ttu-id="61d18-390">Notez que l’URL contient la chaîne de recherche.</span><span class="sxs-lookup"><span data-stu-id="61d18-390">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5000/Students?SearchString=an
```

<span data-ttu-id="61d18-391">Si la page est dans les favoris, le favori contient l’URL de la page et la chaîne de requête `SearchString`.</span><span class="sxs-lookup"><span data-stu-id="61d18-391">If the page is bookmarked, the bookmark contains the URL to the page and the `SearchString` query string.</span></span> <span data-ttu-id="61d18-392">`method="get"` dans la balise `form` est ce qui a provoqué la génération de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="61d18-392">The `method="get"` in the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="61d18-393">Actuellement, quand un lien de tri d’en-tête de colonne est sélectionné, la valeur du filtre de la zone **Search** est perdue.</span><span class="sxs-lookup"><span data-stu-id="61d18-393">Currently, when a column heading sort link is selected, the filter value from the **Search** box is lost.</span></span> <span data-ttu-id="61d18-394">La valeur de filtre perdue est corrigée dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="61d18-394">The lost filter value is fixed in the next section.</span></span>

## <a name="add-paging-functionality-to-the-students-index-page"></a><span data-ttu-id="61d18-395">Ajouter la fonctionnalité de changement de page à la page d’index des étudiants</span><span class="sxs-lookup"><span data-stu-id="61d18-395">Add paging functionality to the Students Index page</span></span>

<span data-ttu-id="61d18-396">Dans cette section, nous allons créer une classe `PaginatedList` pour prendre en charge la pagination.</span><span class="sxs-lookup"><span data-stu-id="61d18-396">In this section, a `PaginatedList` class is created to support paging.</span></span> <span data-ttu-id="61d18-397">La classe `PaginatedList` utilise des instructions `Skip` et `Take` pour filtrer les données sur le serveur au lieu de récupérer toutes les lignes de la table.</span><span class="sxs-lookup"><span data-stu-id="61d18-397">The `PaginatedList` class uses `Skip` and `Take` statements to filter data on the server instead of retrieving all rows of the table.</span></span> <span data-ttu-id="61d18-398">L’illustration suivante montre les boutons de pagination.</span><span class="sxs-lookup"><span data-stu-id="61d18-398">The following illustration shows the paging buttons.</span></span>

![Page d’index des étudiants avec liens de changement de page](sort-filter-page/_static/paging.png)

<span data-ttu-id="61d18-400">Dans le dossier du projet, créez `PaginatedList.cs` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="61d18-400">In the project folder, create `PaginatedList.cs` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

<span data-ttu-id="61d18-401">La méthode `CreateAsync` dans le code précédent prend la taille de page et le numéro de page, et applique les instructions `Skip` et `Take` appropriées au `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="61d18-401">The `CreateAsync` method in the preceding code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="61d18-402">Quand `ToListAsync` est appelée sur le `IQueryable`, elle retourne une liste contenant uniquement la page demandée.</span><span class="sxs-lookup"><span data-stu-id="61d18-402">When `ToListAsync` is called on the `IQueryable`, it returns a List containing only the requested page.</span></span> <span data-ttu-id="61d18-403">Les propriétés `HasPreviousPage` et `HasNextPage` sont utilisées pour activer ou désactiver les boutons de pagination **Previous** et **Next** .</span><span class="sxs-lookup"><span data-stu-id="61d18-403">The properties `HasPreviousPage` and `HasNextPage` are used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="61d18-404">La méthode `CreateAsync` est utilisée pour créer le `PaginatedList<T>`.</span><span class="sxs-lookup"><span data-stu-id="61d18-404">The `CreateAsync` method is used to create the `PaginatedList<T>`.</span></span> <span data-ttu-id="61d18-405">Un constructeur ne peut pas créer l’objet `PaginatedList<T>` ; les constructeurs ne peuvent pas exécuter du code asynchrone.</span><span class="sxs-lookup"><span data-stu-id="61d18-405">A constructor can't create the `PaginatedList<T>` object, constructors can't run asynchronous code.</span></span>

## <a name="add-paging-functionality-to-the-index-method"></a><span data-ttu-id="61d18-406">Ajouter la fonctionnalité de pagination à la méthode Index</span><span class="sxs-lookup"><span data-stu-id="61d18-406">Add paging functionality to the Index method</span></span>

<span data-ttu-id="61d18-407">Dans *Students/Index.cshtml.cs* , mettez à jour le type de `Student` en remplaçant `IList<Student>` par `PaginatedList<Student>` :</span><span class="sxs-lookup"><span data-stu-id="61d18-407">In *Students/Index.cshtml.cs* , update the type of `Student` from `IList<Student>` to `PaginatedList<Student>`:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

<span data-ttu-id="61d18-408">Mettez à jour le Student */index. cshtml. cs* `OnGetAsync` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="61d18-408">Update the *Students/Index.cshtml.cs* `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

<span data-ttu-id="61d18-409">Le code précédent ajoute l’index de page, le `sortOrder` actuel et le `currentFilter` à la signature de méthode.</span><span class="sxs-lookup"><span data-stu-id="61d18-409">The preceding code adds the page index, the current `sortOrder`, and the `currentFilter` to the method signature.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

<span data-ttu-id="61d18-410">Tous les paramètres sont null quand :</span><span class="sxs-lookup"><span data-stu-id="61d18-410">All the parameters are null when:</span></span>

* <span data-ttu-id="61d18-411">La page est appelée à partir du lien **Students** .</span><span class="sxs-lookup"><span data-stu-id="61d18-411">The page is called from the **Students** link.</span></span>
* <span data-ttu-id="61d18-412">L’utilisateur n’a pas cliqué sur un lien de pagination ou de tri.</span><span class="sxs-lookup"><span data-stu-id="61d18-412">The user hasn't clicked a paging or sorting link.</span></span>

<span data-ttu-id="61d18-413">Quand l’utilisateur clique sur un lien de pagination, la variable d’index de page contient le numéro de page à afficher.</span><span class="sxs-lookup"><span data-stu-id="61d18-413">When a paging link is clicked, the page index variable contains the page number to display.</span></span>

<span data-ttu-id="61d18-414">`CurrentSort` fournit la :::no-loc(Razor)::: page avec l’ordre de tri actuel.</span><span class="sxs-lookup"><span data-stu-id="61d18-414">`CurrentSort` provides the :::no-loc(Razor)::: Page with the current sort order.</span></span> <span data-ttu-id="61d18-415">L’ordre de tri actuel doit être inclus dans les liens de pagination afin de conserver l’ordre de tri lors de la pagination.</span><span class="sxs-lookup"><span data-stu-id="61d18-415">The current sort order must be included in the paging links to keep the sort order while paging.</span></span>

<span data-ttu-id="61d18-416">`CurrentFilter` fournit la :::no-loc(Razor)::: page avec la chaîne de filtre actuelle.</span><span class="sxs-lookup"><span data-stu-id="61d18-416">`CurrentFilter` provides the :::no-loc(Razor)::: Page with the current filter string.</span></span> <span data-ttu-id="61d18-417">La valeur `CurrentFilter` :</span><span class="sxs-lookup"><span data-stu-id="61d18-417">The `CurrentFilter` value:</span></span>

* <span data-ttu-id="61d18-418">Doit être incluse dans les liens de pagination afin de conserver les paramètres de filtre lors de la pagination.</span><span class="sxs-lookup"><span data-stu-id="61d18-418">Must be included in the paging links in order to maintain the filter settings during paging.</span></span>
* <span data-ttu-id="61d18-419">Doit être restaurée à la zone de texte quand la page est réaffichée.</span><span class="sxs-lookup"><span data-stu-id="61d18-419">Must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="61d18-420">Si la chaîne de recherche est modifiée pendant la pagination, la page est réinitialisée à 1.</span><span class="sxs-lookup"><span data-stu-id="61d18-420">If the search string is changed while paging, the page is reset to 1.</span></span> <span data-ttu-id="61d18-421">La page doit être réinitialisée à 1, car le nouveau filtre peut entraîner l’affichage de données différentes.</span><span class="sxs-lookup"><span data-stu-id="61d18-421">The page has to be reset to 1 because the new filter can result in different data to display.</span></span> <span data-ttu-id="61d18-422">Quand une valeur de recherche est entrée et que le bouton **Submit** est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="61d18-422">When a search value is entered and **Submit** is selected:</span></span>

* <span data-ttu-id="61d18-423">La chaîne de recherche est changée.</span><span class="sxs-lookup"><span data-stu-id="61d18-423">The search string is changed.</span></span>
* <span data-ttu-id="61d18-424">Le paramètre `searchString` n’est pas null.</span><span class="sxs-lookup"><span data-stu-id="61d18-424">The `searchString` parameter isn't null.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

<span data-ttu-id="61d18-425">La méthode `PaginatedList.CreateAsync` convertit la requête d’étudiant en une seule page d’étudiants dans un type de collection qui prend en charge la pagination.</span><span class="sxs-lookup"><span data-stu-id="61d18-425">The `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="61d18-426">Cette seule page d’élèves est transmise à la :::no-loc(Razor)::: page.</span><span class="sxs-lookup"><span data-stu-id="61d18-426">That single page of students is passed to the :::no-loc(Razor)::: Page.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

<span data-ttu-id="61d18-427">Les deux points d’interrogation dans `PaginatedList.CreateAsync` représentent [l’opérateur de fusion de Null](/dotnet/csharp/language-reference/operators/null-conditional-operator).</span><span class="sxs-lookup"><span data-stu-id="61d18-427">The two question marks in `PaginatedList.CreateAsync` represent the [null-coalescing operator](/dotnet/csharp/language-reference/operators/null-conditional-operator).</span></span> <span data-ttu-id="61d18-428">L’opérateur de fusion de Null définit une valeur par défaut pour un type nullable.</span><span class="sxs-lookup"><span data-stu-id="61d18-428">The null-coalescing operator defines a default value for a nullable type.</span></span> <span data-ttu-id="61d18-429">L’expression `(pageIndex ?? 1)` signifie qu’il faut retourner la valeur de `pageIndex` s’il a une valeur.</span><span class="sxs-lookup"><span data-stu-id="61d18-429">The expression `(pageIndex ?? 1)` means return the value of `pageIndex` if it has a value.</span></span> <span data-ttu-id="61d18-430">Si `pageIndex` n’a pas de valeur, il faut retourner 1.</span><span class="sxs-lookup"><span data-stu-id="61d18-430">If `pageIndex` doesn't have a value, return 1.</span></span>

## <a name="add-paging-links-to-the-student-no-locrazor-page"></a><span data-ttu-id="61d18-431">Ajouter des liens de pagination à la page Student :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="61d18-431">Add paging links to the student :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="61d18-432">Mettez à jour le balisage dans *Students/Index.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="61d18-432">Update the markup in *Students/Index.cshtml* .</span></span> <span data-ttu-id="61d18-433">Les modifications apparaissent en surbrillance :</span><span class="sxs-lookup"><span data-stu-id="61d18-433">The changes are highlighted:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

<span data-ttu-id="61d18-434">Les liens d’en-tête de colonne utilisent la chaîne de requête pour passer la chaîne de recherche actuelle à la méthode `OnGetAsync` afin que l’utilisateur puisse trier dans les résultats du filtrage :</span><span class="sxs-lookup"><span data-stu-id="61d18-434">The column header links use the query string to pass the current search string to the `OnGetAsync` method so that the user can sort within filter results:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

<span data-ttu-id="61d18-435">Les boutons de changement de page sont affichés par des Tag Helpers :</span><span class="sxs-lookup"><span data-stu-id="61d18-435">The paging buttons are displayed by tag helpers:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

<span data-ttu-id="61d18-436">Exécutez l’application et accédez à la page des étudiants.</span><span class="sxs-lookup"><span data-stu-id="61d18-436">Run the app and navigate to the students page.</span></span>

* <span data-ttu-id="61d18-437">Pour vérifier que la pagination fonctionne, cliquez sur les liens de pagination dans différents ordres de tri.</span><span class="sxs-lookup"><span data-stu-id="61d18-437">To make sure paging works, click the paging links in different sort orders.</span></span>
* <span data-ttu-id="61d18-438">Pour vérifier que la pagination fonctionne correctement avec le tri et le filtrage, entrez une chaîne de recherche et essayez de changer de page.</span><span class="sxs-lookup"><span data-stu-id="61d18-438">To verify that paging works correctly with sorting and filtering, enter a search string and try paging.</span></span>

![Page d’index des étudiants avec liens de pagination](sort-filter-page/_static/paging.png)

<span data-ttu-id="61d18-440">Pour mieux comprendre le fonctionnement du code</span><span class="sxs-lookup"><span data-stu-id="61d18-440">To get a better understanding of the code:</span></span>

* <span data-ttu-id="61d18-441">Dans *Students/Index.cshtml.cs* , définissez un point d’arrêt sur `switch (sortOrder)`.</span><span class="sxs-lookup"><span data-stu-id="61d18-441">In *Students/Index.cshtml.cs* , set a breakpoint on `switch (sortOrder)`.</span></span>
* <span data-ttu-id="61d18-442">Ajoutez un espion pour `NameSort`, `DateSort`, `CurrentSort` et `Model.Student.PageIndex`.</span><span class="sxs-lookup"><span data-stu-id="61d18-442">Add a watch for `NameSort`, `DateSort`, `CurrentSort`, and `Model.Student.PageIndex`.</span></span>
* <span data-ttu-id="61d18-443">Dans *Students/Index.cshtml* , définissez un point d’arrêt sur `@Html.DisplayNameFor(model => model.Student[0].LastName)`.</span><span class="sxs-lookup"><span data-stu-id="61d18-443">In *Students/Index.cshtml* , set a breakpoint on `@Html.DisplayNameFor(model => model.Student[0].LastName)`.</span></span>

<span data-ttu-id="61d18-444">Effectuez un pas à pas détaillé dans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="61d18-444">Step through the debugger.</span></span>

## <a name="update-the-about-page-to-show-student-statistics"></a><span data-ttu-id="61d18-445">Mettez à jour la page About pour afficher les statistiques sur les étudiants</span><span class="sxs-lookup"><span data-stu-id="61d18-445">Update the About page to show student statistics</span></span>

<span data-ttu-id="61d18-446">Lors de cette étape, nous allons mettre à jour *Pages/About.cshtml* afin d’afficher le nombre d’étudiants qui se sont inscrits pour chaque date d’inscription.</span><span class="sxs-lookup"><span data-stu-id="61d18-446">In this step, *Pages/About.cshtml* is updated to display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="61d18-447">La mise à jour utilise le regroupement et comprend les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="61d18-447">The update uses grouping and includes the following steps:</span></span>

* <span data-ttu-id="61d18-448">Créer un modèle de vue pour les données utilisées par la page **About** .</span><span class="sxs-lookup"><span data-stu-id="61d18-448">Create a view model for the data used by the **About** Page.</span></span>
* <span data-ttu-id="61d18-449">Mettre à jour la page About pour utiliser le modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="61d18-449">Update the About page to use the view model.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="61d18-450">Créer le modèle d’affichage</span><span class="sxs-lookup"><span data-stu-id="61d18-450">Create the view model</span></span>

<span data-ttu-id="61d18-451">Créez un dossier *SchoolViewModels* dans le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="61d18-451">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="61d18-452">Dans le dossier *SchoolViewModels* , ajoutez un *EnrollmentDateGroup.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="61d18-452">In the *SchoolViewModels* folder, add a *EnrollmentDateGroup.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a><span data-ttu-id="61d18-453">Mettre à jour le modèle de page About</span><span class="sxs-lookup"><span data-stu-id="61d18-453">Update the About page model</span></span>

<span data-ttu-id="61d18-454">Les modèles web dans ASP.NET Core 2.2 n’incluent pas la page About.</span><span class="sxs-lookup"><span data-stu-id="61d18-454">The web templates in ASP.NET Core 2.2 do not include the About page.</span></span> <span data-ttu-id="61d18-455">Si vous utilisez ASP.NET Core 2,2, créez la page about :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="61d18-455">If you are using ASP.NET Core 2.2, create the About :::no-loc(Razor)::: Page.</span></span>

<span data-ttu-id="61d18-456">Mettez à jour le fichier *Pages/About.cshtml.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="61d18-456">Update the *Pages/About.cshtml.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

<span data-ttu-id="61d18-457">L’instruction LINQ regroupe les entités Student par date d’inscription, calcule le nombre d’entités dans chaque groupe et stocke les résultats dans une collection d’objets de modèle de vue `EnrollmentDateGroup`.</span><span class="sxs-lookup"><span data-stu-id="61d18-457">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="modify-the-about-no-locrazor-page"></a><span data-ttu-id="61d18-458">Modifier la page à propos de :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="61d18-458">Modify the About :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="61d18-459">Remplacez le code du fichier *Pages/About.cshtml* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="61d18-459">Replace the code in the *Pages/About.cshtml* file with the following code:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/About.cshtml)]

<span data-ttu-id="61d18-460">Exécutez l’application et accédez à la page About.</span><span class="sxs-lookup"><span data-stu-id="61d18-460">Run the app and navigate to the About page.</span></span> <span data-ttu-id="61d18-461">Le nombre d’étudiants pour chaque date d’inscription s’affiche dans une table.</span><span class="sxs-lookup"><span data-stu-id="61d18-461">The count of students for each enrollment date is displayed in a table.</span></span>

<span data-ttu-id="61d18-462">Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez [l’application terminée pour cette phase](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).</span><span class="sxs-lookup"><span data-stu-id="61d18-462">If you run into problems you can't solve, download the [completed app for this stage](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).</span></span>

![Page About](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a><span data-ttu-id="61d18-464">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="61d18-464">Additional resources</span></span>

* [<span data-ttu-id="61d18-465">Débogage d’une source ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="61d18-465">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)
* [<span data-ttu-id="61d18-466">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="61d18-466">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=MDs7PFpoMqI)

<span data-ttu-id="61d18-467">Dans le didacticiel suivant, l’application utilise des migrations pour mettre à jour le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="61d18-467">In the next tutorial, the app uses migrations to update the data model.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="61d18-468">[Précédent](xref:data/ef-rp/crud) 
>  [Suivant](xref:data/ef-rp/migrations)</span><span class="sxs-lookup"><span data-stu-id="61d18-468">[Previous](xref:data/ef-rp/crud)
[Next](xref:data/ef-rp/migrations)</span></span>

::: moniker-end

