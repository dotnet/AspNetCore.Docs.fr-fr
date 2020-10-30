---
title: 'Didacticiel : ajouter le tri, le filtrage et l’échange de ASP.NET MVC avec EF Core'
description: Dans ce didacticiel, vous allez ajouter les fonctionnalités de tri, de filtrage et de changement de page à la page d’index des étudiants. Vous allez également créer une page qui effectue un regroupement simple.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 8e425d413471912c763c4892a90e9d12039efec4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053980"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="4349e-104">Didacticiel : ajouter le tri, le filtrage et l’échange de ASP.NET MVC avec EF Core</span><span class="sxs-lookup"><span data-stu-id="4349e-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="4349e-105">Dans le didacticiel précédent, vous avez implémenté un ensemble de pages web pour les opérations CRUD de base pour les entités Student.</span><span class="sxs-lookup"><span data-stu-id="4349e-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="4349e-106">Dans ce didacticiel, vous allez ajouter les fonctionnalités de tri, de filtrage et de changement de page à la page d’index des étudiants.</span><span class="sxs-lookup"><span data-stu-id="4349e-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="4349e-107">Vous allez également créer une page qui effectue un regroupement simple.</span><span class="sxs-lookup"><span data-stu-id="4349e-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="4349e-108">L’illustration suivante montre à quoi ressemblera la page quand vous aurez terminé.</span><span class="sxs-lookup"><span data-stu-id="4349e-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="4349e-109">Les en-têtes des colonnes sont des liens sur lesquels l’utilisateur peut cliquer pour trier selon les colonnes.</span><span class="sxs-lookup"><span data-stu-id="4349e-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="4349e-110">Cliquer de façon répétée sur un en-tête de colonne permet de changer l’ordre de tri (croissant ou décroissant).</span><span class="sxs-lookup"><span data-stu-id="4349e-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Page d’index des étudiants](sort-filter-page/_static/paging.png)

<span data-ttu-id="4349e-112">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="4349e-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4349e-113">Ajouter des liens de tri de colonne</span><span class="sxs-lookup"><span data-stu-id="4349e-113">Add column sort links</span></span>
> * <span data-ttu-id="4349e-114">Ajouter une zone Rechercher</span><span class="sxs-lookup"><span data-stu-id="4349e-114">Add a Search box</span></span>
> * <span data-ttu-id="4349e-115">Ajouter la pagination à l'index des étudiants</span><span class="sxs-lookup"><span data-stu-id="4349e-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="4349e-116">Ajouter la pagination à la méthode Index</span><span class="sxs-lookup"><span data-stu-id="4349e-116">Add paging to Index method</span></span>
> * <span data-ttu-id="4349e-117">Ajouter des liens de pagination</span><span class="sxs-lookup"><span data-stu-id="4349e-117">Add paging links</span></span>
> * <span data-ttu-id="4349e-118">Créer une page À propos</span><span class="sxs-lookup"><span data-stu-id="4349e-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4349e-119">Prérequis</span><span class="sxs-lookup"><span data-stu-id="4349e-119">Prerequisites</span></span>

* [<span data-ttu-id="4349e-120">Implémenter la fonctionnalité CRUD</span><span class="sxs-lookup"><span data-stu-id="4349e-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="4349e-121">Ajouter des liens de tri de colonne</span><span class="sxs-lookup"><span data-stu-id="4349e-121">Add column sort links</span></span>

<span data-ttu-id="4349e-122">Pour ajouter le tri à la page d’index des étudiants, vous allez modifier la méthode `Index` du contrôleur Students et ajouter du code à la vue de l’index des étudiants.</span><span class="sxs-lookup"><span data-stu-id="4349e-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="4349e-123">Ajouter la fonctionnalité de tri à la méthode Index</span><span class="sxs-lookup"><span data-stu-id="4349e-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="4349e-124">Dans *StudentsController.cs* , remplacez la `Index` méthode par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4349e-124">In *StudentsController.cs* , replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="4349e-125">Ce code reçoit un paramètre `sortOrder` à partir de la chaîne de requête dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="4349e-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="4349e-126">La valeur de chaîne de requête est fournie par ASP.NET Core MVC en tant que paramètre à la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4349e-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="4349e-127">Le paramètre sera la chaîne « Name » ou « Date », éventuellement suivie d’un trait de soulignement et de la chaîne « desc » pour spécifier l’ordre décroissant.</span><span class="sxs-lookup"><span data-stu-id="4349e-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="4349e-128">L’ordre de tri par défaut est croissant.</span><span class="sxs-lookup"><span data-stu-id="4349e-128">The default sort order is ascending.</span></span>

<span data-ttu-id="4349e-129">La première fois que la page d’index est demandée, il n’y a pas de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="4349e-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="4349e-130">Les étudiants sont affichés dans l’ordre croissant par leur nom, ce qui correspond au paramétrage par défaut de l’instruction `switch`.</span><span class="sxs-lookup"><span data-stu-id="4349e-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="4349e-131">Quand l’utilisateur clique sur un lien hypertexte d’en-tête de colonne, la valeur `sortOrder` appropriée est fournie dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="4349e-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="4349e-132">Les deux éléments `ViewData` (NameSortParm et DateSortParm) sont utilisés par la vue pour configurer les liens hypertexte d’en-tête de colonne avec les valeurs de chaîne de requête appropriées.</span><span class="sxs-lookup"><span data-stu-id="4349e-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="4349e-133">Il s’agit d’instructions ternaires.</span><span class="sxs-lookup"><span data-stu-id="4349e-133">These are ternary statements.</span></span> <span data-ttu-id="4349e-134">La première spécifie que si le paramètre `sortOrder` est null ou vide, NameSortParm doit être défini sur « name_desc » ; sinon, il doit être défini sur une chaîne vide.</span><span class="sxs-lookup"><span data-stu-id="4349e-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="4349e-135">Ces deux instructions permettent à la vue de définir les liens hypertexte d’en-tête de colonne comme suit :</span><span class="sxs-lookup"><span data-stu-id="4349e-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="4349e-136">Ordre de tri actuel</span><span class="sxs-lookup"><span data-stu-id="4349e-136">Current sort order</span></span>  | <span data-ttu-id="4349e-137">Lien hypertexte Nom de famille</span><span class="sxs-lookup"><span data-stu-id="4349e-137">Last Name Hyperlink</span></span> | <span data-ttu-id="4349e-138">Lien hypertexte Date</span><span class="sxs-lookup"><span data-stu-id="4349e-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="4349e-139">Nom de famille croissant</span><span class="sxs-lookup"><span data-stu-id="4349e-139">Last Name ascending</span></span>  | <span data-ttu-id="4349e-140">descending</span><span class="sxs-lookup"><span data-stu-id="4349e-140">descending</span></span>          | <span data-ttu-id="4349e-141">ascending</span><span class="sxs-lookup"><span data-stu-id="4349e-141">ascending</span></span>      |
| <span data-ttu-id="4349e-142">Nom de famille décroissant</span><span class="sxs-lookup"><span data-stu-id="4349e-142">Last Name descending</span></span> | <span data-ttu-id="4349e-143">ascending</span><span class="sxs-lookup"><span data-stu-id="4349e-143">ascending</span></span>           | <span data-ttu-id="4349e-144">ascending</span><span class="sxs-lookup"><span data-stu-id="4349e-144">ascending</span></span>      |
| <span data-ttu-id="4349e-145">Date croissante</span><span class="sxs-lookup"><span data-stu-id="4349e-145">Date ascending</span></span>       | <span data-ttu-id="4349e-146">ascending</span><span class="sxs-lookup"><span data-stu-id="4349e-146">ascending</span></span>           | <span data-ttu-id="4349e-147">descending</span><span class="sxs-lookup"><span data-stu-id="4349e-147">descending</span></span>     |
| <span data-ttu-id="4349e-148">Date décroissante</span><span class="sxs-lookup"><span data-stu-id="4349e-148">Date descending</span></span>      | <span data-ttu-id="4349e-149">ascending</span><span class="sxs-lookup"><span data-stu-id="4349e-149">ascending</span></span>           | <span data-ttu-id="4349e-150">ascending</span><span class="sxs-lookup"><span data-stu-id="4349e-150">ascending</span></span>      |

<span data-ttu-id="4349e-151">La méthode utilise LINQ to Entities pour spécifier la colonne d’après laquelle effectuer le tri.</span><span class="sxs-lookup"><span data-stu-id="4349e-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="4349e-152">Le code crée une variable `IQueryable` avant l’instruction switch, la modifie dans l’instruction switch et appelle la méthode `ToListAsync` après l’instruction `switch`.</span><span class="sxs-lookup"><span data-stu-id="4349e-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="4349e-153">Lorsque vous créez et modifiez des variables `IQueryable`, aucune requête n’est envoyée à la base de données.</span><span class="sxs-lookup"><span data-stu-id="4349e-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="4349e-154">La requête n’est pas exécutée tant que vous ne convertissez pas l’objet `IQueryable` en collection en appelant une méthode telle que `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="4349e-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="4349e-155">Par conséquent, ce code génère une requête unique qui n’est pas exécutée avant l’instruction `return View`.</span><span class="sxs-lookup"><span data-stu-id="4349e-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="4349e-156">Ce code peut devenir très détaillé avec un grand nombre de colonnes.</span><span class="sxs-lookup"><span data-stu-id="4349e-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="4349e-157">[Le dernier didacticiel de cette série](advanced.md#dynamic-linq) montre comment écrire du code qui vous permet de transmettre le nom de la colonne `OrderBy` dans une variable chaîne.</span><span class="sxs-lookup"><span data-stu-id="4349e-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="4349e-158">Ajouter des liens hypertexte d’en-tête de colonne dans la vue de l’index des étudiants</span><span class="sxs-lookup"><span data-stu-id="4349e-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="4349e-159">Remplacez le code dans *Views/Students/Index.cshtml* par le code suivant pour ajouter des liens hypertexte d’en-tête de colonne.</span><span class="sxs-lookup"><span data-stu-id="4349e-159">Replace the code in *Views/Students/Index.cshtml* , with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="4349e-160">Les lignes modifiées apparaissent en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="4349e-160">The changed lines are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="4349e-161">Ce code utilise les informations figurant dans les propriétés `ViewData` pour configurer des liens hypertexte avec les valeurs de chaîne de requête appropriées.</span><span class="sxs-lookup"><span data-stu-id="4349e-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="4349e-162">Exécutez l’application, sélectionnez l’onglet **Students** , puis cliquez sur les en-têtes des colonnes **Last Name** et **Enrollment Date** pour vérifier que le tri fonctionne.</span><span class="sxs-lookup"><span data-stu-id="4349e-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![Page d’index des étudiants dans l’ordre de leurs noms](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="4349e-164">Ajouter une zone Rechercher</span><span class="sxs-lookup"><span data-stu-id="4349e-164">Add a Search box</span></span>

<span data-ttu-id="4349e-165">Pour ajouter le filtrage à la page d’index des étudiants, vous allez ajouter une zone de texte et un bouton d’envoi à la vue et apporter les modifications correspondantes dans la méthode `Index`.</span><span class="sxs-lookup"><span data-stu-id="4349e-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="4349e-166">La zone de texte vous permet d’entrer une chaîne à rechercher dans les champs de prénom et de nom.</span><span class="sxs-lookup"><span data-stu-id="4349e-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="4349e-167">Ajouter la fonctionnalité de filtrage à la méthode Index</span><span class="sxs-lookup"><span data-stu-id="4349e-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="4349e-168">Dans *StudentsController.cs* , remplacez la méthode `Index` par le code suivant (les modifications apparaissent en surbrillance).</span><span class="sxs-lookup"><span data-stu-id="4349e-168">In *StudentsController.cs* , replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="4349e-169">Vous avez ajouté un paramètre `searchString` à la méthode `Index`.</span><span class="sxs-lookup"><span data-stu-id="4349e-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="4349e-170">La valeur de chaîne de recherche est reçue à partir d’une zone de texte que vous ajouterez à la vue Index.</span><span class="sxs-lookup"><span data-stu-id="4349e-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="4349e-171">Vous avez également ajouté à l’instruction LINQ une clause where qui sélectionne uniquement les étudiants dont le prénom ou le nom contient la chaîne de recherche.</span><span class="sxs-lookup"><span data-stu-id="4349e-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="4349e-172">L’instruction qui ajoute la clause where est exécutée uniquement s’il existe une valeur à rechercher.</span><span class="sxs-lookup"><span data-stu-id="4349e-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="4349e-173">Ici, vous appelez la méthode `Where` sur un objet `IQueryable`, et le filtre sera traité sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="4349e-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="4349e-174">Dans certains scénarios, vous pouvez appeler la méthode `Where` en tant que méthode d’extension sur une collection en mémoire.</span><span class="sxs-lookup"><span data-stu-id="4349e-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="4349e-175">(Par exemple, supposons que vous modifiez la référence à `_context.Students` afin qu’à la place d’un EF, `DbSet` elle fasse référence à une méthode de référentiel qui retourne une `IEnumerable` collection.) Le résultat serait normalement le même, mais dans certains cas peut être différent.</span><span class="sxs-lookup"><span data-stu-id="4349e-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="4349e-176">Par exemple, l’implémentation par le .NET Framework de la méthode `Contains` effectue une comparaison respectant la casse par défaut, mais dans SQL Server, cela est déterminé par le paramètre de classement de l’instance SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4349e-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="4349e-177">Ce paramètre définit par défaut le non-respect de la casse.</span><span class="sxs-lookup"><span data-stu-id="4349e-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="4349e-178">Vous pouvez appeler la méthode `ToUpper` pour rendre explicitement le test sensible à la casse :  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())* .</span><span class="sxs-lookup"><span data-stu-id="4349e-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())* .</span></span> <span data-ttu-id="4349e-179">Cela garantit que les résultats resteront les mêmes si vous modifiez le code ultérieurement pour utiliser un référentiel qui renverra une collection `IEnumerable` à la place d’un objet `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="4349e-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="4349e-180">(Lorsque vous appelez la `Contains` méthode sur une `IEnumerable` collection, vous recevez l’implémentation .NET Framework ; lorsque vous l’appelez sur un `IQueryable` objet, vous recevez l’implémentation du fournisseur de base de données.) Toutefois, il y a une baisse des performances pour cette solution.</span><span class="sxs-lookup"><span data-stu-id="4349e-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="4349e-181">Le code `ToUpper` place une fonction dans la clause WHERE de l’instruction TSQL SELECT.</span><span class="sxs-lookup"><span data-stu-id="4349e-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="4349e-182">Elle empêche l’optimiseur d’utiliser un index.</span><span class="sxs-lookup"><span data-stu-id="4349e-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="4349e-183">Étant donné que SQL est généralement installé comme non sensible à la casse, il est préférable d’éviter le code `ToUpper` jusqu’à ce que vous ayez migré vers un magasin de données qui respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="4349e-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="4349e-184">Ajouter une zone de recherche à la vue de l’index des étudiants</span><span class="sxs-lookup"><span data-stu-id="4349e-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="4349e-185">Dans *Views/Student/Index.cshtml* , ajoutez le code en surbrillance immédiatement avant la balise d’ouverture de table afin de créer une légende, une zone de texte et un bouton de **recherche** .</span><span class="sxs-lookup"><span data-stu-id="4349e-185">In *Views/Student/Index.cshtml* , add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="4349e-186">Ce code utilise le  [Tag Helper](xref:mvc/views/tag-helpers/intro)`<form>` pour ajouter le bouton et la zone de texte de recherche.</span><span class="sxs-lookup"><span data-stu-id="4349e-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="4349e-187">Par défaut, le Tag Helper `<form>` envoie les données de formulaire avec un POST, ce qui signifie que les paramètres sont transmis dans le corps du message HTTP et non pas dans l’URL sous forme de chaînes de requête.</span><span class="sxs-lookup"><span data-stu-id="4349e-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="4349e-188">Lorsque vous spécifiez HTTP GET, les données de formulaire sont transmises dans l’URL sous forme de chaînes de requête, ce qui permet aux utilisateurs d’ajouter l’URL aux favoris.</span><span class="sxs-lookup"><span data-stu-id="4349e-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="4349e-189">Les recommandations du W3C stipulent que vous devez utiliser GET quand l’action ne produit pas de mise à jour.</span><span class="sxs-lookup"><span data-stu-id="4349e-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="4349e-190">Exécutez l’application, sélectionnez l’onglet **Students** , entrez une chaîne de recherche, puis cliquez sur Rechercher pour vérifier que le filtrage fonctionne.</span><span class="sxs-lookup"><span data-stu-id="4349e-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![Page d’index des étudiants avec filtrage](sort-filter-page/_static/filtering.png)

<span data-ttu-id="4349e-192">Notez que l’URL contient la chaîne de recherche.</span><span class="sxs-lookup"><span data-stu-id="4349e-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="4349e-193">Si vous marquez cette page d’un signet, vous obtenez la liste filtrée lorsque vous utilisez le signet.</span><span class="sxs-lookup"><span data-stu-id="4349e-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="4349e-194">L’ajout de `method="get"` dans la balise `form` est ce qui a provoqué la génération de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="4349e-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="4349e-195">À ce stade, si vous cliquez sur un lien de tri d’en-tête de colonne, vous perdez la valeur de filtre que vous avez entrée dans la zone **Rechercher** .</span><span class="sxs-lookup"><span data-stu-id="4349e-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="4349e-196">Vous corrigerez cela dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="4349e-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="4349e-197">Ajouter la pagination à l'index des étudiants</span><span class="sxs-lookup"><span data-stu-id="4349e-197">Add paging to Students Index</span></span>

<span data-ttu-id="4349e-198">Pour ajouter le changement de page à la page d’index des étudiants, vous allez créer une classe `PaginatedList` qui utilise les instructions `Skip` et `Take` pour filtrer les données sur le serveur au lieu de toujours récupérer toutes les lignes de la table.</span><span class="sxs-lookup"><span data-stu-id="4349e-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="4349e-199">Ensuite, vous apporterez des modifications supplémentaires dans la méthode `Index` et ajouterez des boutons de changement de page dans la vue `Index`.</span><span class="sxs-lookup"><span data-stu-id="4349e-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="4349e-200">L’illustration suivante montre les boutons de pagination.</span><span class="sxs-lookup"><span data-stu-id="4349e-200">The following illustration shows the paging buttons.</span></span>

![Page d’index des étudiants avec liens de changement de page](sort-filter-page/_static/paging.png)

<span data-ttu-id="4349e-202">Dans le dossier du projet, créez `PaginatedList.cs`, puis remplacez le code du modèle par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="4349e-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="4349e-203">La méthode `CreateAsync` de ce code accepte la taille de page et le numéro de page, et applique les instructions `Skip` et `Take` appropriées à `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="4349e-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="4349e-204">Quand la méthode `ToListAsync` est appelée sur `IQueryable`, elle renvoie une liste contenant uniquement la page demandée.</span><span class="sxs-lookup"><span data-stu-id="4349e-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="4349e-205">Les propriétés `HasPreviousPage` et `HasNextPage` peuvent être utilisées pour activer ou désactiver les boutons de changement de page **Précédent** et **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="4349e-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="4349e-206">Une méthode `CreateAsync` est utilisée à la place d’un constructeur pour créer l’objet `PaginatedList<T>`, car les constructeurs ne peuvent pas exécuter de code asynchrone.</span><span class="sxs-lookup"><span data-stu-id="4349e-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="4349e-207">Ajouter la pagination à la méthode Index</span><span class="sxs-lookup"><span data-stu-id="4349e-207">Add paging to Index method</span></span>

<span data-ttu-id="4349e-208">Dans *StudentsController.cs* , remplacez la méthode `Index` par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="4349e-208">In *StudentsController.cs* , replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="4349e-209">Ce code ajoute un paramètre de numéro de page, un paramètre d’ordre de tri actuel et un paramètre de filtre actuel à la signature de la méthode.</span><span class="sxs-lookup"><span data-stu-id="4349e-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="4349e-210">La première fois que la page s’affiche, ou si l’utilisateur n’a pas cliqué sur un lien de changement de page ni de tri, tous les paramètres sont Null.</span><span class="sxs-lookup"><span data-stu-id="4349e-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="4349e-211">Si l’utilisateur clique sur un lien de changement de page, la variable de page contient le numéro de page à afficher.</span><span class="sxs-lookup"><span data-stu-id="4349e-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="4349e-212">L’élément `ViewData` nommé CurrentSort fournit à l’affichage l’ordre de tri actuel, car il doit être inclus dans les liens de changement de page pour que l’ordre de tri soit conservé lors du changement de page.</span><span class="sxs-lookup"><span data-stu-id="4349e-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="4349e-213">L’élément `ViewData` nommé CurrentFilter fournit à la vue la chaîne de filtre actuelle.</span><span class="sxs-lookup"><span data-stu-id="4349e-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="4349e-214">Cette valeur doit être incluse dans les liens de changement de page pour que les paramètres de filtre soient conservés lors du changement de page, et elle doit être restaurée dans la zone de texte lorsque la page est réaffichée.</span><span class="sxs-lookup"><span data-stu-id="4349e-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="4349e-215">Si la chaîne de recherche est modifiée au cours du changement de page, la page doit être réinitialisée à 1, car le nouveau filtre peut entraîner l’affichage de données différentes.</span><span class="sxs-lookup"><span data-stu-id="4349e-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="4349e-216">La chaîne de recherche est modifiée quand une valeur est entrée dans la zone de texte et que le bouton d’envoi est enfoncé.</span><span class="sxs-lookup"><span data-stu-id="4349e-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="4349e-217">Dans ce cas, le paramètre `searchString` n’est pas Null.</span><span class="sxs-lookup"><span data-stu-id="4349e-217">In that case, the `searchString` parameter isn't null.</span></span>

```csharp
if (searchString != null)
{
    pageNumber = 1;
}
else
{
    searchString = currentFilter;
}
```

<span data-ttu-id="4349e-218">À la fin de la méthode `Index`, la méthode `PaginatedList.CreateAsync` convertit la requête d’étudiant en une page individuelle d’étudiants dans un type de collection qui prend en charge le changement de page.</span><span class="sxs-lookup"><span data-stu-id="4349e-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="4349e-219">Cette page individuelle d’étudiants est alors transmise à la vue.</span><span class="sxs-lookup"><span data-stu-id="4349e-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="4349e-220">La méthode `PaginatedList.CreateAsync` accepte un numéro de page.</span><span class="sxs-lookup"><span data-stu-id="4349e-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="4349e-221">Les deux points d’interrogation représentent l’opérateur de fusion Null.</span><span class="sxs-lookup"><span data-stu-id="4349e-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="4349e-222">L’opérateur de fusion Null définit une valeur par défaut pour un type nullable ; l’expression `(pageNumber ?? 1)` indique de renvoyer la valeur de `pageNumber` si elle a une valeur, ou de renvoyer 1 si `pageNumber` a la valeur Null.</span><span class="sxs-lookup"><span data-stu-id="4349e-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="4349e-223">Ajouter des liens de pagination</span><span class="sxs-lookup"><span data-stu-id="4349e-223">Add paging links</span></span>

<span data-ttu-id="4349e-224">Dans *Views/Instructors/Index.cshtml* , remplacez le code existant par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="4349e-224">In *Views/Students/Index.cshtml* , replace the existing code with the following code.</span></span> <span data-ttu-id="4349e-225">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="4349e-225">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="4349e-226">L’instruction `@model` en haut de la page spécifie que la vue obtient désormais un objet `PaginatedList<T>` à la place d’un objet `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="4349e-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="4349e-227">Les liens d’en-tête de colonne utilisent la chaîne de requête pour transmettre la chaîne de recherche actuelle au contrôleur afin que l’utilisateur puisse trier les résultats de filtrage :</span><span class="sxs-lookup"><span data-stu-id="4349e-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="4349e-228">Les boutons de changement de page sont affichés par des Tag Helpers :</span><span class="sxs-lookup"><span data-stu-id="4349e-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="4349e-229">Exécutez l’application et accédez à la page des étudiants.</span><span class="sxs-lookup"><span data-stu-id="4349e-229">Run the app and go to the Students page.</span></span>

![Page d’index des étudiants avec liens de changement de page](sort-filter-page/_static/paging.png)

<span data-ttu-id="4349e-231">Cliquez sur les liens de changement de page dans différents ordres de tri pour vérifier que le changement de page fonctionne.</span><span class="sxs-lookup"><span data-stu-id="4349e-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="4349e-232">Ensuite, entrez une chaîne de recherche et essayez de changer de page à nouveau pour vérifier que le changement de page fonctionne correctement avec le tri et le filtrage.</span><span class="sxs-lookup"><span data-stu-id="4349e-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="4349e-233">Créer une page À propos</span><span class="sxs-lookup"><span data-stu-id="4349e-233">Create an About page</span></span>

<span data-ttu-id="4349e-234">Pour la page **About** du site web de Contoso University, vous afficherez le nombre d’étudiants inscrits pour chaque date d’inscription.</span><span class="sxs-lookup"><span data-stu-id="4349e-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="4349e-235">Cela nécessite un regroupement et des calculs simples sur les groupes.</span><span class="sxs-lookup"><span data-stu-id="4349e-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="4349e-236">Pour ce faire, vous devez effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="4349e-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="4349e-237">Créez une classe de modèle de vue pour les données que vous devez transmettre à la vue.</span><span class="sxs-lookup"><span data-stu-id="4349e-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="4349e-238">Créez la méthode About dans le contrôleur Home.</span><span class="sxs-lookup"><span data-stu-id="4349e-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="4349e-239">Créer la vue About.</span><span class="sxs-lookup"><span data-stu-id="4349e-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="4349e-240">Créer le modèle d’affichage</span><span class="sxs-lookup"><span data-stu-id="4349e-240">Create the view model</span></span>

<span data-ttu-id="4349e-241">Créez un dossier *SchoolViewModels* dans le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="4349e-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="4349e-242">Dans le nouveau dossier, ajoutez un fichier de classe *EnrollmentDateGroup.cs* et remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4349e-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="4349e-243">Modifier le contrôleur Home</span><span class="sxs-lookup"><span data-stu-id="4349e-243">Modify the Home Controller</span></span>

<span data-ttu-id="4349e-244">Dans *HomeController.cs* , ajoutez les instructions using suivantes en haut du fichier :</span><span class="sxs-lookup"><span data-stu-id="4349e-244">In *HomeController.cs* , add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="4349e-245">Ajoutez une variable de classe pour le contexte de base de données immédiatement après l’accolade ouvrante de la classe et obtenez une instance du contexte à partir d’ASP.NET Core DI :</span><span class="sxs-lookup"><span data-stu-id="4349e-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="4349e-246">Ajoutez une méthode `About` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4349e-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="4349e-247">L’instruction LINQ regroupe les entités Student par date d’inscription, calcule le nombre d’entités dans chaque groupe et stocke les résultats dans une collection d’objets de modèle de vue `EnrollmentDateGroup`.</span><span class="sxs-lookup"><span data-stu-id="4349e-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="4349e-248">Créer la vue About</span><span class="sxs-lookup"><span data-stu-id="4349e-248">Create the About View</span></span>

<span data-ttu-id="4349e-249">Ajoutez un fichier *Views/Home/About.cshtml* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4349e-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="4349e-250">Exécutez l’application et accédez à la page About.</span><span class="sxs-lookup"><span data-stu-id="4349e-250">Run the app and go to the About page.</span></span> <span data-ttu-id="4349e-251">Le nombre d’étudiants pour chaque date d’inscription s’affiche dans une table.</span><span class="sxs-lookup"><span data-stu-id="4349e-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="4349e-252">Obtenir le code</span><span class="sxs-lookup"><span data-stu-id="4349e-252">Get the code</span></span>

[<span data-ttu-id="4349e-253">Télécharger ou afficher l’application complète.</span><span class="sxs-lookup"><span data-stu-id="4349e-253">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="4349e-254">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="4349e-254">Next steps</span></span>

<span data-ttu-id="4349e-255">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="4349e-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4349e-256">Ajouter des liens de tri de colonne</span><span class="sxs-lookup"><span data-stu-id="4349e-256">Added column sort links</span></span>
> * <span data-ttu-id="4349e-257">Ajouter une zone Rechercher</span><span class="sxs-lookup"><span data-stu-id="4349e-257">Added a Search box</span></span>
> * <span data-ttu-id="4349e-258">Ajouter la pagination à l'index des étudiants</span><span class="sxs-lookup"><span data-stu-id="4349e-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="4349e-259">Ajouter la pagination à la méthode Index</span><span class="sxs-lookup"><span data-stu-id="4349e-259">Added paging to Index method</span></span>
> * <span data-ttu-id="4349e-260">Ajouter des liens de pagination</span><span class="sxs-lookup"><span data-stu-id="4349e-260">Added paging links</span></span>
> * <span data-ttu-id="4349e-261">Page À propos créée</span><span class="sxs-lookup"><span data-stu-id="4349e-261">Created an About page</span></span>

<span data-ttu-id="4349e-262">Passez au tutoriel suivant pour découvrir comment gérer les modifications du modèle de données à l’aide de migrations.</span><span class="sxs-lookup"><span data-stu-id="4349e-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="4349e-263">Suivant : gérer les modifications du modèle de données</span><span class="sxs-lookup"><span data-stu-id="4349e-263">Next: Handle data model changes</span></span>](migrations.md)
