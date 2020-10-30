---
title: 'Partie 6, :::no-loc(Razor)::: pages avec EF Core dans ASP.net Core-lire les données associées'
author: rick-anderson
description: 'Partie 6 de :::no-loc(Razor)::: pages et Entity Framework série de didacticiels.'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e52e4aefc18b84f85bea28a9724894eed50ca54a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061065"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="7f4f1-103">Partie 6, :::no-loc(Razor)::: pages avec EF Core dans ASP.net Core-lire les données associées</span><span class="sxs-lookup"><span data-stu-id="7f4f1-103">Part 6, :::no-loc(Razor)::: Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="7f4f1-104">Par [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7f4f1-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7f4f1-105">Ce tutoriel montre comment lire et afficher des données associées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="7f4f1-106">Les données associées sont des données qu’EF Core charge dans des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="7f4f1-107">Les illustrations suivantes montrent les pages terminées pour ce didacticiel :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Page d’index des cours](read-related-data/_static/courses-index30.png)

![Page d’index des formateurs](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="7f4f1-110">Chargement hâtif, explicite et différé</span><span class="sxs-lookup"><span data-stu-id="7f4f1-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="7f4f1-111">EF Core peut charger des données associées dans les propriétés de navigation d’une entité de plusieurs manières :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="7f4f1-112">[Chargement hâtif](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="7f4f1-113">Le chargement hâtif a lieu quand une requête pour un type d’entité charge également des entités associées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="7f4f1-114">Quand une entité est lue, ses données associées sont récupérées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="7f4f1-115">Cela génère en général une requête de jointure unique qui récupère toutes les données nécessaires.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="7f4f1-116">EF Core émet plusieurs requêtes pour certains types de chargement hâtif.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="7f4f1-117">Il peut s’avérer plus efficace d’émettre plusieurs requêtes plutôt qu’une seule très grande.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="7f4f1-118">Le chargement hâtif est spécifié avec les méthodes `Include` et `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Exemple de chargement hâtif](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="7f4f1-120">Le chargement hâtif envoie plusieurs requêtes quand une navigation dans la collection est incluse :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="7f4f1-121">Une requête pour la requête principale</span><span class="sxs-lookup"><span data-stu-id="7f4f1-121">One query for the main query</span></span> 
  * <span data-ttu-id="7f4f1-122">Une requête pour chaque « périmètre » de collection dans l’arborescence de la charge.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="7f4f1-123">Requêtes distinctes avec `Load` : les données peuvent être récupérées dans des requêtes distinctes, et EF Core « corrige » les propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="7f4f1-124">Quand EF Core « corrige », cela signifie que les propriétés de navigation sont renseignées automatiquement.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="7f4f1-125">Les requêtes distinctes avec `Load` s’apparentent plus au chargement explicite qu’au chargement hâtif.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Exemple de requêtes distinctes](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="7f4f1-127">**Remarque :** EF Core résout automatiquement les propriétés de navigation vers d’autres entités précédemment chargées dans l’instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-127">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="7f4f1-128">Même si les données pour une propriété de navigation ne sont *pas* explicitement incluses, la propriété peut toujours être renseignée si toutes ou une partie des entités associées ont été précédemment chargées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="7f4f1-129">[Chargement explicite](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="7f4f1-130">Quand l’entité est lue pour la première fois, les données associées ne sont pas récupérées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="7f4f1-131">Vous devez écrire du code pour récupérer les données associées en cas de besoin.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="7f4f1-132">En cas de chargement explicite avec des requêtes distinctes, plusieurs requêtes sont envoyées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="7f4f1-133">Avec le chargement explicite, le code spécifie les propriétés de navigation à charger.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="7f4f1-134">Utilisez la méthode `Load` pour effectuer le chargement explicite.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="7f4f1-135">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-135">For example:</span></span>

  ![Exemple de chargement explicite](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="7f4f1-137">[Chargement différé](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="7f4f1-138">Quand l’entité est lue pour la première fois, les données associées ne sont pas récupérées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-138">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="7f4f1-139">Lors du premier accès à une propriété de navigation, les données requises pour cette propriété de navigation sont récupérées automatiquement.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-139">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="7f4f1-140">Une requête est envoyée à la base de données chaque fois qu’une propriété de navigation fait pour la première fois l’objet d’un accès.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-140">A query is sent to the database each time a navigation property is accessed for the first time.</span></span> <span data-ttu-id="7f4f1-141">Le chargement différé peut nuire aux performances, par exemple lorsque les développeurs utilisent des modèles N + 1, en chargeant un parent et en énumérant les enfants.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-141">Lazy loading can hurt performance, for example when developers use N+1 patterns, loading a parent and enumerating through children.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="7f4f1-142">Créer des pages Course</span><span class="sxs-lookup"><span data-stu-id="7f4f1-142">Create Course pages</span></span>

<span data-ttu-id="7f4f1-143">L’entité `Course` comprend une propriété de navigation qui contient l’entité `Department` associée.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="7f4f1-145">Pour afficher le nom du service (« department ») affecté pour un cours (« course ») :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="7f4f1-146">Chargez l’entité `Department` associée dans la propriété de navigation `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="7f4f1-147">Obtenez le nom à partir de la propriété `Name` de l’entité `Department`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="7f4f1-148">Générer automatiquement des modèles de pages Course</span><span class="sxs-lookup"><span data-stu-id="7f4f1-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f4f1-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f4f1-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f4f1-150">Suivez les instructions dans [Générer automatiquement des modèles de pages Student](xref:data/ef-rp/intro#scaffold-student-pages) avec les exceptions suivantes :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="7f4f1-151">Créez un dossier *Pages/Courses* .</span><span class="sxs-lookup"><span data-stu-id="7f4f1-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="7f4f1-152">Utilisez `Course` pour la classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="7f4f1-153">Utilisez la classe de contexte existante au lieu d’en créer une nouvelle.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f4f1-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f4f1-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7f4f1-155">Créez un dossier *Pages/Courses* .</span><span class="sxs-lookup"><span data-stu-id="7f4f1-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="7f4f1-156">Exécutez la commande suivante pour générer automatiquement des modèles de pages Course.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="7f4f1-157">**Sur Windows :**</span><span class="sxs-lookup"><span data-stu-id="7f4f1-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="7f4f1-158">**Sur Linux ou macOS :**</span><span class="sxs-lookup"><span data-stu-id="7f4f1-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="7f4f1-159">Ouvrez *Pages/Courses/Index.cshtml.cs* et examinez la méthode `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="7f4f1-160">Le moteur de génération de modèles automatique a spécifié le chargement hâtif pour la propriété de navigation `Department`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="7f4f1-161">La méthode `Include` spécifie le chargement hâtif.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="7f4f1-162">Exécutez l’application et sélectionnez le lien **Courses** .</span><span class="sxs-lookup"><span data-stu-id="7f4f1-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="7f4f1-163">La colonne Department affiche le `DepartmentID`, ce qui n’est pas utile.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="7f4f1-164">Afficher le nom du service (« department »)</span><span class="sxs-lookup"><span data-stu-id="7f4f1-164">Display the department name</span></span>

<span data-ttu-id="7f4f1-165">Mettez à jour Pages/Courses/Index.cshtml.cs avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="7f4f1-166">Le code précédent remplace la propriété `Course` par `Courses` et ajoute `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="7f4f1-167">`AsNoTracking` améliore les performances, car les entités retournées ne sont pas suivies.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="7f4f1-168">Les entités n’ont pas besoin d’être suivies, car elles ne sont pas mises à jour dans le contexte actuel.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="7f4f1-169">Mettez à jour *Pages/Courses/Index.cshtml* avec le code suivant.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="7f4f1-170">Les modifications suivantes ont été apportées au code généré automatiquement :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="7f4f1-171">Le nom de propriété `Course` est changé en `Courses`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="7f4f1-172">Ajout d’une colonne **Number** qui affiche la valeur de la propriété `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="7f4f1-173">Par défaut, les clés primaires ne sont pas générées automatiquement, car elles ne sont normalement pas significatives pour les utilisateurs finaux.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="7f4f1-174">Toutefois, dans le cas présent la clé primaire est significative.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="7f4f1-175">Modification de la colonne **Department** afin d’afficher le nom du département.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="7f4f1-176">Le code affiche la propriété `Name` de l’entité `Department` qui est chargée dans la propriété de navigation `Department` :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="7f4f1-177">Exécutez l’application et sélectionnez l’onglet **Courses** pour afficher la liste avec les noms des départements.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Page d’index des cours](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="7f4f1-179">Chargement de données associées avec Select</span><span class="sxs-lookup"><span data-stu-id="7f4f1-179">Loading related data with Select</span></span>

<span data-ttu-id="7f4f1-180">La méthode `OnGetAsync` charge les données associées avec la méthode `Include`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="7f4f1-181">La méthode `Select` est autre solution qui charge uniquement les données associées nécessaires.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="7f4f1-182">Pour les éléments uniques, comme `Department.Name`, il utilise une jointure interne SQL.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="7f4f1-183">Pour les collections, il utilise un autre accès à la base de données, mais c’est aussi le cas de l’opérateur `Include` sur les collections.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="7f4f1-184">Le code suivant charge les données associées avec la méthode `Select` :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="7f4f1-185">Le code précédent ne retourne aucun type d’entité, par conséquent aucun suivi n’est effectué.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="7f4f1-186">Pour plus d’informations sur le suivi EF, consultez [suivi et requêtes de No-Tracking](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="7f4f1-187">`CourseViewModel` :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="7f4f1-188">Pour obtenir un exemple complet, consultez [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) et [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="7f4f1-189">Créer des pages Instructor</span><span class="sxs-lookup"><span data-stu-id="7f4f1-189">Create Instructor pages</span></span>

<span data-ttu-id="7f4f1-190">Cette section génère automatiquement des modèles de pages Instructor et ajoute les cours et les inscriptions associés à la page d’index des formateurs.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="7f4f1-191">![Page d’index des formateurs](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="7f4f1-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="7f4f1-192">Cette page lit et affiche les données associées comme suit :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="7f4f1-193">La liste des formateurs affiche des données associées de l’entité `OfficeAssignment` (Office dans l’image précédente).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="7f4f1-194">Il existe une relation un-à-zéro-ou-un entre les entités `Instructor` et `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="7f4f1-195">Le chargement hâtif est utilisé pour les entités `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="7f4f1-196">Le chargement hâtif est généralement plus efficace quand les données associées doivent être affichées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="7f4f1-197">Ici, les affectations de bureau pour les formateurs sont affichées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="7f4f1-198">Quand l’utilisateur sélectionne un formateur, les entités `Course` associées sont affichées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="7f4f1-199">Il existe une relation plusieurs-à-plusieurs entre les entités `Instructor` et `Course`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="7f4f1-200">Le chargement hâtif est utilisé pour les entités `Course` et leurs entités `Department` associées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="7f4f1-201">Dans le cas présent, des requêtes distinctes peuvent être plus efficaces, car seuls les cours du formateur sélectionné sont nécessaires.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="7f4f1-202">Cet exemple montre comment utiliser le chargement hâtif pour des propriétés de navigation dans des entités qui se trouvent dans des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="7f4f1-203">Quand l’utilisateur sélectionne un cours, les données associées de l’entité `Enrollments` s’affichent.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="7f4f1-204">Dans l’image précédente, le nom et la note de l’étudiant sont affichés.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="7f4f1-205">Il existe une relation un-à-plusieurs entre les entités `Course` et `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="7f4f1-206">Création d'un modèle de vue</span><span class="sxs-lookup"><span data-stu-id="7f4f1-206">Create a view model</span></span>

<span data-ttu-id="7f4f1-207">La page sur les formateurs affiche les données de trois tables différentes.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="7f4f1-208">Un modèle de vue comprenant trois propriétés représentant les trois tables est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="7f4f1-209">Créez *SchoolViewModels/InstructorIndexData.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="7f4f1-210">Générer automatiquement des modèles de pages Instructor</span><span class="sxs-lookup"><span data-stu-id="7f4f1-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f4f1-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f4f1-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f4f1-212">Suivez les instructions dans [Générer automatiquement des modèles de pages Student](xref:data/ef-rp/intro#scaffold-student-pages) avec les exceptions suivantes :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="7f4f1-213">Créez un dossier *Pages/Instructors* .</span><span class="sxs-lookup"><span data-stu-id="7f4f1-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="7f4f1-214">Utilisez `Instructor` pour la classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="7f4f1-215">Utilisez la classe de contexte existante au lieu d’en créer une nouvelle.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f4f1-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f4f1-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7f4f1-217">Créez un dossier *Pages/Instructors* .</span><span class="sxs-lookup"><span data-stu-id="7f4f1-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="7f4f1-218">Exécutez la commande suivante pour générer automatiquement des modèles de pages Instructor.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="7f4f1-219">**Sur Windows :**</span><span class="sxs-lookup"><span data-stu-id="7f4f1-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="7f4f1-220">**Sur Linux ou macOS :**</span><span class="sxs-lookup"><span data-stu-id="7f4f1-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="7f4f1-221">Pour voir à quoi ressemble la page générée automatiquement avant de la mettre à jour, exécutez l’application et accédez à la page Instructors.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="7f4f1-222">Mettez à jour *pages/Instructors/index. cshtml. cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="7f4f1-223">La méthode `OnGetAsync` accepte des données de route facultatives pour l’ID du formateur sélectionné.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="7f4f1-224">Examinez la requête dans le fichier *Pages/Instructors/Index.cshtml.cs*  :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="7f4f1-225">Le code spécifie un chargement hâtif pour les propriétés de navigation suivantes :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="7f4f1-226">Notez la répétition des méthodes `Include` et `ThenInclude` pour `CourseAssignments` et `Course`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="7f4f1-227">Cette répétition est nécessaire pour spécifier un chargement hâtif pour deux propriétés de navigation de l’entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="7f4f1-228">Le code suivant s’exécute quand un formateur est sélectionné (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="7f4f1-229">Le formateur sélectionné est récupéré à partir de la liste des formateurs dans le modèle d’affichage.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="7f4f1-230">La propriété `Courses` du modèle d’affichage est chargée avec les entités `Course` de la propriété de navigation `CourseAssignments` de ce formateur.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="7f4f1-231">La méthode `Where` retourne une collection.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="7f4f1-232">Toutefois, dans ce cas, le filtre sélectionne une entité unique, de sorte que la `Single` méthode est appelée pour convertir la collection en une seule `Instructor` entité.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-232">But in this case, the filter will select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="7f4f1-233">L’entité `Instructor` fournit l’accès à la propriété `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-233">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="7f4f1-234">`CourseAssignments` fournit l’accès aux entités `Course` associées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-234">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instructor-to-Courses m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="7f4f1-236">La méthode `Single` est utilisée sur une collection quand la collection ne compte qu’un seul élément.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-236">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="7f4f1-237">La méthode `Single` lève une exception si la collection est vide ou s’il y a plusieurs éléments.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-237">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="7f4f1-238">Une alternative est `SingleOrDefault`, qui renvoie une valeur par défaut (Null dans ce cas) si la collection est vide.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-238">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="7f4f1-239">Le code suivant renseigne la propriété `Enrollments` du modèle d’affichage quand un cours est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-239">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="7f4f1-240">Mettre à jour la page d’index des formateurs</span><span class="sxs-lookup"><span data-stu-id="7f4f1-240">Update the instructors Index page</span></span>

<span data-ttu-id="7f4f1-241">Mettez à jour *Pages/Instructors/Index.cshtml* avec le code suivant.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-241">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="7f4f1-242">Le code précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-242">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="7f4f1-243">Il met à jour la directive `page` en remplaçant `@page` par `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-243">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="7f4f1-244">`"{id:int?}"` est un modèle de route.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-244">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="7f4f1-245">Le modèle de route change les chaînes de requête entières dans l’URL en données de route.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-245">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="7f4f1-246">Par exemple, si vous cliquez sur le lien **Select** pour un formateur avec seulement la directive `@page`, une URL comme celle-ci est générée :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-246">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="7f4f1-247">Quand la directive de page est `@page "{id:int?}"`, l’URL est :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-247">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="7f4f1-248">Ajoute une colonne **Office** qui affiche `item.OfficeAssignment.Location` seulement si `item.OfficeAssignment` n’a pas la valeur Null.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-248">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="7f4f1-249">Comme il s’agit d’une relation un-à-zéro-ou-un, il se peut qu’il n’y ait pas d’entité OfficeAssignment associée.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-249">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="7f4f1-250">Ajoute une colonne **Courses** qui affiche les cours animés par chaque formateur.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-250">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="7f4f1-251">Pour plus d’informations sur cette syntaxe Razor, consultez [transition de ligne explicite](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="7f4f1-251">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="7f4f1-252">Ajoute du code qui ajoute dynamiquement `class="success"` à l’élément `tr` du formateur et du cours sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-252">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="7f4f1-253">Cela définit une couleur d’arrière-plan pour la ligne sélectionnée à l’aide d’une classe d’amorçage.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-253">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="7f4f1-254">Ajoute un nouveau lien hypertexte libellé **Select** .</span><span class="sxs-lookup"><span data-stu-id="7f4f1-254">Adds a new hyperlink labeled **Select** .</span></span> <span data-ttu-id="7f4f1-255">Ce lien envoie l’ID du formateur sélectionné à la méthode `Index`, et définit une couleur d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-255">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="7f4f1-256">Ajoute un tableau de cours pour l’instructeur sélectionné.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-256">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="7f4f1-257">Ajoute un tableau d’inscriptions d’étudiants pour le cours sélectionné.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-257">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="7f4f1-258">Exécutez l’application et sélectionnez l’onglet **Instructors** . La page affiche le `Location` (Office) à partir de l' `OfficeAssignment` entité associée.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-258">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="7f4f1-259">Si `OfficeAssignment` a la valeur Null, une cellule de tableau vide est affichée.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-259">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="7f4f1-260">Cliquez sur le lien **Select** pour un formateur.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-260">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="7f4f1-261">Le style de ligne change et les cours attribués à ce formateur s’affichent.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-261">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="7f4f1-262">Sélectionnez un cours pour afficher la liste des étudiants inscrits et leurs notes.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-262">Select a course to see the list of enrolled students and their grades.</span></span>

![Page d’index des formateurs avec un formateur et un cours sélectionnés](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="7f4f1-264">Utilisation de Single</span><span class="sxs-lookup"><span data-stu-id="7f4f1-264">Using Single</span></span>

<span data-ttu-id="7f4f1-265">La méthode `Single` peut passer la condition `Where` au lieu d’appeler la méthode `Where` séparément :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-265">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="7f4f1-266">L’utilisation de `Single` avec une condition Where est une question de préférence personnelle.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-266">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="7f4f1-267">Elle n’offre pas d’avantages par rapport à la méthode `Where`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-267">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="7f4f1-268">Chargement explicite</span><span class="sxs-lookup"><span data-stu-id="7f4f1-268">Explicit loading</span></span>

<span data-ttu-id="7f4f1-269">Le code actuel spécifie le chargement hâtif pour `Enrollments` et `Students` :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-269">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="7f4f1-270">Supposez que les utilisateurs souhaitent rarement voir les inscriptions à un cours.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-270">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="7f4f1-271">Dans ce cas, une optimisation consisterait à charger les données d’inscription uniquement si elles sont demandées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-271">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="7f4f1-272">Dans cette section, la méthode `OnGetAsync` est mise à jour de façon à utiliser le chargement explicite de `Enrollments` et `Students`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-272">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="7f4f1-273">Mettez à jour *Pages/Instructors/Index.cshtml.cs* avec le code suivant.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-273">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="7f4f1-274">Le code précédent supprime les appels de méthode *ThenInclude* pour les données sur les inscriptions et les étudiants.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-274">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="7f4f1-275">Si un cours est sélectionné, le code de chargement explicite récupère :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-275">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="7f4f1-276">Les entités `Enrollment` pour le cours sélectionné.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-276">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="7f4f1-277">Les entités `Student` pour chaque `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-277">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="7f4f1-278">Notez que le code précédent commente `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-278">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="7f4f1-279">Les propriétés de navigation peuvent être chargées explicitement uniquement pour les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-279">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="7f4f1-280">Tester l'application.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-280">Test the app.</span></span> <span data-ttu-id="7f4f1-281">Du point de vue d’un utilisateur, l’application se comporte de façon identique à la version précédente.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-281">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7f4f1-282">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="7f4f1-282">Next steps</span></span>

<span data-ttu-id="7f4f1-283">Le didacticiel suivant montre comment mettre à jour les données associées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-283">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="7f4f1-284">[Didacticiel précédent](xref:data/ef-rp/complex-data-model) 
> [Didacticiel suivant](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="7f4f1-284">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7f4f1-285">Dans ce didacticiel, nous allons lire et afficher des données associées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-285">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="7f4f1-286">Les données associées sont des données qu’EF Core charge dans des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-286">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="7f4f1-287">Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, [téléchargez ou affichez l’application terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-287">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="7f4f1-288">[Instructions de téléchargement](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-288">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="7f4f1-289">Les illustrations suivantes montrent les pages terminées pour ce didacticiel :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-289">The following illustrations show the completed pages for this tutorial:</span></span>

![Page d’index des cours](read-related-data/_static/courses-index.png)

![Page d’index des formateurs](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="7f4f1-292">Chargement hâtif, explicite et différé des données associées</span><span class="sxs-lookup"><span data-stu-id="7f4f1-292">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="7f4f1-293">EF Core peut charger des données associées dans les propriétés de navigation d’une entité de plusieurs manières :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-293">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="7f4f1-294">[Chargement hâtif](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-294">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="7f4f1-295">Le chargement hâtif a lieu quand une requête pour un type d’entité charge également des entités associées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-295">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="7f4f1-296">Quand l’entité est lue, ses données associées sont récupérées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-296">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="7f4f1-297">Cela génère en général une requête de jointure unique qui récupère toutes les données nécessaires.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-297">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="7f4f1-298">EF Core émet plusieurs requêtes pour certains types de chargement hâtif.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-298">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="7f4f1-299">L’émission de requêtes multiples peut être plus efficace que ce n’était le cas pour certaines requêtes dans EF6 où une seule requête était émise.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-299">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="7f4f1-300">Le chargement hâtif est spécifié avec les méthodes `Include` et `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-300">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Exemple de chargement hâtif](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="7f4f1-302">Le chargement hâtif envoie plusieurs requêtes quand une navigation dans la collection est incluse :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-302">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="7f4f1-303">Une requête pour la requête principale</span><span class="sxs-lookup"><span data-stu-id="7f4f1-303">One query for the main query</span></span> 
  * <span data-ttu-id="7f4f1-304">Une requête pour chaque « périmètre » de collection dans l’arborescence de la charge.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-304">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="7f4f1-305">Requêtes distinctes avec `Load` : les données peuvent être récupérées dans des requêtes distinctes, et EF Core « corrige » les propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-305">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="7f4f1-306">Le terme « corrige » signifie qu’EF Core renseigne automatiquement les propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-306">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="7f4f1-307">Les requêtes distinctes avec `Load` s’apparentent plus au chargement explicite qu’au chargement hâtif.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-307">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Exemple de requêtes distinctes](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="7f4f1-309">Remarque : EF Core corrige automatiquement les propriétés de navigation vers d’autres entités qui étaient précédemment chargées dans l’instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-309">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="7f4f1-310">Même si les données pour une propriété de navigation ne sont *pas* explicitement incluses, la propriété peut toujours être renseignée si toutes ou une partie des entités associées ont été précédemment chargées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-310">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="7f4f1-311">[Chargement explicite](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-311">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="7f4f1-312">Quand l’entité est lue pour la première fois, les données associées ne sont pas récupérées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-312">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="7f4f1-313">Vous devez écrire du code pour récupérer les données associées en cas de besoin.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-313">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="7f4f1-314">En cas de chargement explicite avec des requêtes distinctes, plusieurs requêtes sont envoyées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-314">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="7f4f1-315">Avec le chargement explicite, le code spécifie les propriétés de navigation à charger.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-315">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="7f4f1-316">Utilisez la méthode `Load` pour effectuer le chargement explicite.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-316">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="7f4f1-317">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-317">For example:</span></span>

  ![Exemple de chargement explicite](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="7f4f1-319">[Chargement différé](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-319">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="7f4f1-320">[Le chargement différé a été ajouté à EF Core dans la version 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-320">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="7f4f1-321">Quand l’entité est lue pour la première fois, les données associées ne sont pas récupérées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-321">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="7f4f1-322">Lors du premier accès à une propriété de navigation, les données requises pour cette propriété de navigation sont récupérées automatiquement.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-322">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="7f4f1-323">Une requête est envoyée à la base de données chaque fois qu’une propriété de navigation est sollicitée pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-323">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="7f4f1-324">L’opérateur `Select` charge uniquement les données associées nécessaires.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-324">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="7f4f1-325">Créer une page Course qui affiche le nom du département</span><span class="sxs-lookup"><span data-stu-id="7f4f1-325">Create a Course page that displays department name</span></span>

<span data-ttu-id="7f4f1-326">L’entité Course comprend une propriété de navigation qui contient l’entité `Department`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-326">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="7f4f1-327">L’entité `Department` contient le département auquel le cours est affecté.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-327">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="7f4f1-328">Pour afficher le nom du département affecté dans une liste de cours</span><span class="sxs-lookup"><span data-stu-id="7f4f1-328">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="7f4f1-329">Obtenez la propriété `Name` à partir de l’entité `Department`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-329">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="7f4f1-330">L’entité `Department` provient de la propriété de navigation `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-330">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="7f4f1-332">Génération automatique du modèle Course</span><span class="sxs-lookup"><span data-stu-id="7f4f1-332">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f4f1-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f4f1-333">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="7f4f1-334">Suivez les instructions fournies dans [Générer automatiquement le modèle d’étudiant](xref:data/ef-rp/intro#scaffold-the-student-model) et utilisez `Course` pour la classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-334">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f4f1-335">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f4f1-335">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="7f4f1-336">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-336">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="7f4f1-337">La commande précédente génère automatiquement le modèle `Course`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-337">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="7f4f1-338">Ouvrez le projet dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-338">Open the project in Visual Studio.</span></span>

<span data-ttu-id="7f4f1-339">Ouvrez *Pages/Courses/Index.cshtml.cs* et examinez la méthode `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-339">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="7f4f1-340">Le moteur de génération de modèles automatique a spécifié le chargement hâtif pour la propriété de navigation `Department`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-340">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="7f4f1-341">La méthode `Include` spécifie le chargement hâtif.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-341">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="7f4f1-342">Exécutez l’application et sélectionnez le lien **Courses** .</span><span class="sxs-lookup"><span data-stu-id="7f4f1-342">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="7f4f1-343">La colonne Department affiche le `DepartmentID`, ce qui n’est pas utile.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-343">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="7f4f1-344">Mettez à jour la méthode `OnGetAsync` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-344">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="7f4f1-345">Le code précédent ajoute `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-345">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="7f4f1-346">`AsNoTracking` améliore les performances, car les entités retournées ne sont pas suivies.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-346">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="7f4f1-347">Les entités ne sont pas suivies, car elles ne sont pas mises à jour dans le contexte actuel.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-347">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="7f4f1-348">Mettez à jour *Pages/Courses/Index.cshtml* avec le balisage en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-348">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="7f4f1-349">Les modifications suivantes ont été apportées au code généré automatiquement :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-349">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="7f4f1-350">Changement de l’en-tête : Index a été remplacé par Courses.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-350">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="7f4f1-351">Ajout d’une colonne **Number** qui affiche la valeur de la propriété `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-351">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="7f4f1-352">Par défaut, les clés primaires ne sont pas générées automatiquement, car elles ne sont normalement pas significatives pour les utilisateurs finaux.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-352">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="7f4f1-353">Toutefois, dans le cas présent la clé primaire est significative.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-353">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="7f4f1-354">Modification de la colonne **Department** afin d’afficher le nom du département.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-354">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="7f4f1-355">Le code affiche la propriété `Name` de l’entité `Department` qui est chargée dans la propriété de navigation `Department` :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-355">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="7f4f1-356">Exécutez l’application et sélectionnez l’onglet **Courses** pour afficher la liste avec les noms des départements.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-356">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Page d’index des cours](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="7f4f1-358">Chargement de données associées avec Select</span><span class="sxs-lookup"><span data-stu-id="7f4f1-358">Loading related data with Select</span></span>

<span data-ttu-id="7f4f1-359">La méthode `OnGetAsync` charge les données associées avec la méthode `Include` :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-359">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="7f4f1-360">L’opérateur `Select` charge uniquement les données associées nécessaires.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-360">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="7f4f1-361">Pour les éléments uniques, comme `Department.Name`, il utilise une jointure interne SQL.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-361">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="7f4f1-362">Pour les collections, il utilise un autre accès à la base de données, mais c’est aussi le cas de l’opérateur `Include` sur les collections.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-362">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="7f4f1-363">Le code suivant charge les données associées avec la méthode `Select` :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-363">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="7f4f1-364">`CourseViewModel` :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-364">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="7f4f1-365">Pour obtenir un exemple complet, consultez [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) et [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-365">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="7f4f1-366">Créer une page Instructors qui affiche les cours et les inscriptions</span><span class="sxs-lookup"><span data-stu-id="7f4f1-366">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="7f4f1-367">Dans cette section, nous allons créer la page Instructors.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-367">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="7f4f1-368">![Page d’index des formateurs](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="7f4f1-368">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="7f4f1-369">Cette page lit et affiche les données associées comme suit :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-369">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="7f4f1-370">La liste des formateurs affiche des données associées de l’entité `OfficeAssignment` (Office dans l’image précédente).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-370">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="7f4f1-371">Il existe une relation un-à-zéro-ou-un entre les entités `Instructor` et `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-371">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="7f4f1-372">Le chargement hâtif est utilisé pour les entités `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-372">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="7f4f1-373">Le chargement hâtif est généralement plus efficace quand les données associées doivent être affichées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-373">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="7f4f1-374">Ici, les affectations de bureau pour les formateurs sont affichées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-374">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="7f4f1-375">Quand l’utilisateur sélectionne un formateur (Harui dans l’image précédente), les entités `Course` associées sont affichées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-375">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="7f4f1-376">Il existe une relation plusieurs-à-plusieurs entre les entités `Instructor` et `Course`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-376">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="7f4f1-377">Le chargement hâtif est utilisé pour les entités `Course` et leurs entités `Department` associées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-377">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="7f4f1-378">Dans le cas présent, des requêtes distinctes peuvent être plus efficaces, car seuls les cours du formateur sélectionné sont nécessaires.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-378">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="7f4f1-379">Cet exemple montre comment utiliser le chargement hâtif pour des propriétés de navigation dans des entités qui se trouvent dans des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-379">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="7f4f1-380">Quand l’utilisateur sélectionne un cours (Chemistry dans l’image précédente), les données associées de l’entité `Enrollments` sont affichées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-380">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="7f4f1-381">Dans l’image précédente, le nom et la note de l’étudiant sont affichés.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-381">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="7f4f1-382">Il existe une relation un-à-plusieurs entre les entités `Course` et `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-382">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="7f4f1-383">Créer un modèle de vue pour la vue d’index des formateurs</span><span class="sxs-lookup"><span data-stu-id="7f4f1-383">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="7f4f1-384">La page sur les formateurs affiche les données de trois tables différentes.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-384">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="7f4f1-385">Nous allons créer un modèle d’affichage qui comprend les trois entités représentant les trois tables.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-385">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="7f4f1-386">Dans le dossier *SchoolViewModels* , créez *InstructorIndexData.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-386">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="7f4f1-387">Générer automatiquement le modèle Instructor</span><span class="sxs-lookup"><span data-stu-id="7f4f1-387">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f4f1-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f4f1-388">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="7f4f1-389">Suivez les instructions fournies dans [Générer automatiquement le modèle d’étudiant](xref:data/ef-rp/intro#scaffold-the-student-model) et utilisez `Instructor` pour la classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-389">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f4f1-390">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f4f1-390">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="7f4f1-391">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-391">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="7f4f1-392">La commande précédente génère automatiquement le modèle `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-392">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="7f4f1-393">Exécutez l’application et accédez à la page des formateurs.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-393">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="7f4f1-394">Remplacez *Pages/Instructors/Index.cshtml.cs* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-394">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="7f4f1-395">La méthode `OnGetAsync` accepte des données de route facultatives pour l’ID du formateur sélectionné.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-395">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="7f4f1-396">Examinez la requête dans le fichier *Pages/Instructors/Index.cshtml.cs*  :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-396">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="7f4f1-397">La requête comporte deux Include :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-397">The query has two includes:</span></span>

* <span data-ttu-id="7f4f1-398">`OfficeAssignment` : affiché dans l’[affichage Instructors](#IP).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-398">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="7f4f1-399">`CourseAssignments` : qui affiche les cours dispensés.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-399">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="7f4f1-400">Mettre à jour la page d’index des formateurs</span><span class="sxs-lookup"><span data-stu-id="7f4f1-400">Update the instructors Index page</span></span>

<span data-ttu-id="7f4f1-401">Mettez à jour *Pages/Instructors/Index.cshtml* avec le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-401">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="7f4f1-402">Le balisage précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-402">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="7f4f1-403">Il met à jour la directive `page` en remplaçant `@page` par `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-403">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="7f4f1-404">`"{id:int?}"` est un modèle de route.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-404">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="7f4f1-405">Le modèle de route change les chaînes de requête entières dans l’URL en données de route.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-405">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="7f4f1-406">Par exemple, si vous cliquez sur le lien **Select** pour un formateur avec seulement la directive `@page`, une URL comme celle-ci est générée :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-406">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="7f4f1-407">Quand la directive de page est `@page "{id:int?}"`, l’URL précédente est :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-407">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="7f4f1-408">Le titre de la page est **Instructors** .</span><span class="sxs-lookup"><span data-stu-id="7f4f1-408">Page title is **Instructors** .</span></span>
* <span data-ttu-id="7f4f1-409">Il ajoute une colonne **Office** qui affiche `item.OfficeAssignment.Location` uniquement si `item.OfficeAssignment` n’est pas Null.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-409">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="7f4f1-410">Comme il s’agit d’une relation un-à-zéro-ou-un, il se peut qu’il n’y ait pas d’entité OfficeAssignment associée.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-410">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="7f4f1-411">Vous avez ajouté une colonne **Courses** qui affiche les cours animés par chaque formateur.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-411">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="7f4f1-412">Pour plus d’informations sur cette syntaxe Razor, consultez [transition de ligne explicite](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="7f4f1-412">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="7f4f1-413">Vous avez ajouté un code qui ajoute dynamiquement `class="success"` à l’élément `tr` du formateur sélectionné.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-413">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="7f4f1-414">Cela définit une couleur d’arrière-plan pour la ligne sélectionnée à l’aide d’une classe d’amorçage.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-414">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="7f4f1-415">Ajout d’un nouveau lien hypertexte libellé **Select** .</span><span class="sxs-lookup"><span data-stu-id="7f4f1-415">Added a new hyperlink labeled **Select** .</span></span> <span data-ttu-id="7f4f1-416">Ce lien envoie l’ID du formateur sélectionné à la méthode `Index`, et définit une couleur d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-416">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="7f4f1-417">Exécutez l’application et sélectionnez l’onglet **Instructors** . La page affiche le `Location` (Office) à partir de l' `OfficeAssignment` entité associée.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-417">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="7f4f1-418">Si OfficeAssignment\` est Null, une cellule de table vide est affichée.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-418">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="7f4f1-419">Cliquez sur le lien **Select** .</span><span class="sxs-lookup"><span data-stu-id="7f4f1-419">Click on the **Select** link.</span></span> <span data-ttu-id="7f4f1-420">Le style de ligne change.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-420">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="7f4f1-421">Ajouter les cours dispensés par le formateur sélectionné</span><span class="sxs-lookup"><span data-stu-id="7f4f1-421">Add courses taught by selected instructor</span></span>

<span data-ttu-id="7f4f1-422">Mettez à jour la méthode `OnGetAsync` dans *Pages/Instructors/Index.cshtml.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-422">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="7f4f1-423">Ajouter `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="7f4f1-423">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="7f4f1-424">Examinez la requête mise à jour :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-424">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="7f4f1-425">La requête précédente ajoute les entités `Department`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-425">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="7f4f1-426">Le code suivant s’exécute quand un formateur est sélectionné (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-426">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="7f4f1-427">Le formateur sélectionné est récupéré à partir de la liste des formateurs dans le modèle d’affichage.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-427">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="7f4f1-428">La propriété `Courses` du modèle d’affichage est chargée avec les entités `Course` de la propriété de navigation `CourseAssignments` de ce formateur.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-428">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="7f4f1-429">La méthode `Where` retourne une collection.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-429">The `Where` method returns a collection.</span></span> <span data-ttu-id="7f4f1-430">Dans la méthode `Where` précédente, une seule entité `Instructor` est retournée.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-430">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="7f4f1-431">La méthode `Single` convertit la collection en une seule entité `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-431">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="7f4f1-432">L’entité `Instructor` fournit l’accès à la propriété `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-432">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="7f4f1-433">`CourseAssignments` fournit l’accès aux entités `Course` associées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-433">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instructor-to-Courses m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="7f4f1-435">La méthode `Single` est utilisée sur une collection quand la collection ne compte qu’un seul élément.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-435">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="7f4f1-436">La méthode `Single` lève une exception si la collection est vide ou s’il y a plusieurs éléments.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-436">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="7f4f1-437">Une alternative est `SingleOrDefault`, qui renvoie une valeur par défaut (Null dans ce cas) si la collection est vide.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-437">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="7f4f1-438">L’utilisation de `SingleOrDefault` sur une collection vide :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-438">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="7f4f1-439">Génère une exception (à cause de la tentative de trouver une propriété `Courses` sur une référence Null).</span><span class="sxs-lookup"><span data-stu-id="7f4f1-439">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="7f4f1-440">Le message d’exception indique moins clairement la cause du problème.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-440">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="7f4f1-441">Le code suivant renseigne la propriété `Enrollments` du modèle d’affichage quand un cours est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-441">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="7f4f1-442">Ajoutez le balisage suivant à la fin de la page *pages/Instructors/index. cshtml* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-442">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="7f4f1-443">Le balisage précédent affiche une liste de cours associés à un formateur quand un formateur est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-443">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="7f4f1-444">Tester l'application.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-444">Test the app.</span></span> <span data-ttu-id="7f4f1-445">Cliquez sur un lien **Select** dans la page des formateurs.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-445">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="7f4f1-446">Afficher les données sur les étudiants</span><span class="sxs-lookup"><span data-stu-id="7f4f1-446">Show student data</span></span>

<span data-ttu-id="7f4f1-447">Dans cette section, nous allons mettre à jour l’application afin d’afficher les données sur les étudiants pour le cours sélectionné.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-447">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="7f4f1-448">Mettez à jour la requête dans la méthode `OnGetAsync` dans *Pages/Instructors/Index.cshtml.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-448">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="7f4f1-449">Mettez à jour *Pages/Instructors/Index.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="7f4f1-449">Update *Pages/Instructors/Index.cshtml* .</span></span> <span data-ttu-id="7f4f1-450">Ajoutez le balisage suivant à la fin du fichier :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-450">Add the following markup to the end of the file:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="7f4f1-451">Le balisage précédent affiche une liste des étudiants qui sont inscrits au cours sélectionné.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-451">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="7f4f1-452">Actualisez la page et sélectionnez un formateur.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-452">Refresh the page and select an instructor.</span></span> <span data-ttu-id="7f4f1-453">Sélectionnez un cours pour afficher la liste des étudiants inscrits et leurs notes.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-453">Select a course to see the list of enrolled students and their grades.</span></span>

![Page d’index des formateurs avec un formateur et un cours sélectionnés](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="7f4f1-455">Utilisation de Single</span><span class="sxs-lookup"><span data-stu-id="7f4f1-455">Using Single</span></span>

<span data-ttu-id="7f4f1-456">La méthode `Single` peut passer la condition `Where` au lieu d’appeler la méthode `Where` séparément :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-456">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="7f4f1-457">L’approche précédente faisant appel à `Single` ne présente aucun avantage par rapport à l’utilisation de `Where`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-457">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="7f4f1-458">Certains développeurs préfèrent le style d’approche `Single`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-458">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="7f4f1-459">Chargement explicite</span><span class="sxs-lookup"><span data-stu-id="7f4f1-459">Explicit loading</span></span>

<span data-ttu-id="7f4f1-460">Le code actuel spécifie le chargement hâtif pour `Enrollments` et `Students` :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-460">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="7f4f1-461">Supposez que les utilisateurs souhaitent rarement voir les inscriptions à un cours.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-461">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="7f4f1-462">Dans ce cas, une optimisation consisterait à charger les données d’inscription uniquement si elles sont demandées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-462">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="7f4f1-463">Dans cette section, la méthode `OnGetAsync` est mise à jour de façon à utiliser le chargement explicite de `Enrollments` et `Students`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-463">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="7f4f1-464">Mettez à jour la méthode `OnGetAsync` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-464">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="7f4f1-465">Le code précédent supprime les appels de méthode *ThenInclude* pour les données sur les inscriptions et les étudiants.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-465">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="7f4f1-466">Si un cours est sélectionné, le code en surbrillance récupère :</span><span class="sxs-lookup"><span data-stu-id="7f4f1-466">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="7f4f1-467">Les entités `Enrollment` pour le cours sélectionné.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-467">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="7f4f1-468">Les entités `Student` pour chaque `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-468">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="7f4f1-469">Notez que le code précédent commente `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-469">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="7f4f1-470">Les propriétés de navigation peuvent être chargées explicitement uniquement pour les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-470">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="7f4f1-471">Tester l'application.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-471">Test the app.</span></span> <span data-ttu-id="7f4f1-472">Du point de vue des utilisateurs, l’application se comporte comme la version précédente.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-472">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="7f4f1-473">Le didacticiel suivant montre comment mettre à jour les données associées.</span><span class="sxs-lookup"><span data-stu-id="7f4f1-473">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f4f1-474">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7f4f1-474">Additional resources</span></span>

* [<span data-ttu-id="7f4f1-475">Version YouTube de ce tutoriel (partie 1)</span><span class="sxs-lookup"><span data-stu-id="7f4f1-475">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="7f4f1-476">Version YouTube de ce tutoriel (partie 2)</span><span class="sxs-lookup"><span data-stu-id="7f4f1-476">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="7f4f1-477">[Précédent](xref:data/ef-rp/complex-data-model) 
> [Suivant](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="7f4f1-477">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
