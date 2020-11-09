---
title: Partie 8, ajouter un nouveau champ à une application ASP.NET Core MVC
author: rick-anderson
description: Partie 8 de la série de didacticiels sur ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: d2b3b22a94e3119712e331565cc74ffa60ada726
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050704"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="05ff7-103">Partie 8, ajouter un nouveau champ à une application ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="05ff7-103">Part 8, add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="05ff7-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="05ff7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="05ff7-105">Dans cette section, Migrations [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First est utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="05ff7-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="05ff7-106">Ajouter un nouveau champ au modèle.</span><span class="sxs-lookup"><span data-stu-id="05ff7-106">Add a new field to the model.</span></span>
* <span data-ttu-id="05ff7-107">Migrer le nouveau champ vers la base de données.</span><span class="sxs-lookup"><span data-stu-id="05ff7-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="05ff7-108">Quand EF Code First est utilisé pour créer automatiquement une base de données, Code First :</span><span class="sxs-lookup"><span data-stu-id="05ff7-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="05ff7-109">Ajoute une table à la base de données pour en suivre le schéma.</span><span class="sxs-lookup"><span data-stu-id="05ff7-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="05ff7-110">Vérifie que la base de données est synchronisée avec les classes de modèle à partir desquelles elle a été générée.</span><span class="sxs-lookup"><span data-stu-id="05ff7-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="05ff7-111">S’ils ne sont pas synchronisés, EF lève une exception.</span><span class="sxs-lookup"><span data-stu-id="05ff7-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="05ff7-112">Cela facilite la recherche de problèmes d’incohérence de code/de bases de données.</span><span class="sxs-lookup"><span data-stu-id="05ff7-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="05ff7-113">Ajouter une propriété Rating au modèle Movie</span><span class="sxs-lookup"><span data-stu-id="05ff7-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="05ff7-114">Ajouter une propriété `Rating` à *Models/Movie.cs* :</span><span class="sxs-lookup"><span data-stu-id="05ff7-114">Add a `Rating` property to *Models/Movie.cs* :</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="05ff7-115">Générer l’application</span><span class="sxs-lookup"><span data-stu-id="05ff7-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="05ff7-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05ff7-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="05ff7-117">Ctrl+Maj+B</span><span class="sxs-lookup"><span data-stu-id="05ff7-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="05ff7-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05ff7-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05ff7-119">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="05ff7-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="05ff7-120">Commande ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="05ff7-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="05ff7-121">Étant donné que vous avez ajouté un nouveau champ à la `Movie` classe, vous devez mettre à jour la liste des liaisons de propriétés pour inclure cette nouvelle propriété.</span><span class="sxs-lookup"><span data-stu-id="05ff7-121">Because you've added a new field to the `Movie` class, you need to update the property binding list so this new property will be included.</span></span> <span data-ttu-id="05ff7-122">Dans *MoviesController.cs* , mettez à jour l’attribut `[Bind]` des méthodes d’action `Create` et `Edit` pour y inclure la propriété `Rating` :</span><span class="sxs-lookup"><span data-stu-id="05ff7-122">In *MoviesController.cs* , update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="05ff7-123">Mettez à jour les modèles de vue pour afficher, créer et modifier la nouvelle propriété `Rating` dans la vue du navigateur.</span><span class="sxs-lookup"><span data-stu-id="05ff7-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="05ff7-124">Ouvrez le fichier */Views/Movies/Index.cshtml* et ajoutez un champ `Rating` :</span><span class="sxs-lookup"><span data-stu-id="05ff7-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="05ff7-125">Mettez à jour le fichier */Views/Movies/Create.cshtml* avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="05ff7-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="05ff7-126">Visual Studio / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="05ff7-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="05ff7-127">Vous pouvez copier/coller le « groupe de formulaire » précédent et laisser IntelliSense vous aider à mettre à jour les champs.</span><span class="sxs-lookup"><span data-stu-id="05ff7-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="05ff7-128">IntelliSense fonctionne avec des [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="05ff7-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Le développeur a tapé la lettre R comme valeur d’attribut asp-for dans le deuxième élément étiquette de la vue.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="05ff7-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05ff7-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="05ff7-133">Mettez à jour les modèles restants.</span><span class="sxs-lookup"><span data-stu-id="05ff7-133">Update the remaining templates.</span></span>

<span data-ttu-id="05ff7-134">Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne.</span><span class="sxs-lookup"><span data-stu-id="05ff7-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="05ff7-135">Vous pouvez voir un exemple de modification ci-dessous, mais elle doit être appliquée à chaque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="05ff7-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="05ff7-136">L’application ne fonctionne pas tant que la base de données n’est pas mise à jour pour inclure le nouveau champ.</span><span class="sxs-lookup"><span data-stu-id="05ff7-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="05ff7-137">Si elle est exécutée maintenant, l’erreur `SqlException` est levée :</span><span class="sxs-lookup"><span data-stu-id="05ff7-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="05ff7-138">Cette erreur survient car la classe du modèle Movie mise à jour est différente du schéma de la table Movie de la base de données existante.</span><span class="sxs-lookup"><span data-stu-id="05ff7-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="05ff7-139">(Il n’existe pas de colonne `Rating` dans la table de base de données.)</span><span class="sxs-lookup"><span data-stu-id="05ff7-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="05ff7-140">Plusieurs approches sont possibles pour résoudre l’erreur :</span><span class="sxs-lookup"><span data-stu-id="05ff7-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="05ff7-141">Laissez Entity Framework supprimer et recréer automatiquement la base de données sur la base du nouveau schéma de classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="05ff7-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="05ff7-142">Cette approche est très utile au début du cycle de développement quand vous effectuez un développement actif sur une base de données de test. Elle permet de faire évoluer rapidement le schéma de modèle et de base de données ensemble.</span><span class="sxs-lookup"><span data-stu-id="05ff7-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="05ff7-143">L’inconvénient, cependant, est que vous perdez les données existantes dans la base de données. Par conséquent, n’utilisez pas cette approche sur une base de données de production.</span><span class="sxs-lookup"><span data-stu-id="05ff7-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="05ff7-144">L’utilisation d’un initialiseur pour amorcer automatiquement une base de données avec des données de test est souvent un moyen efficace pour développer une application.</span><span class="sxs-lookup"><span data-stu-id="05ff7-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="05ff7-145">Il s’agit d’une bonne approche pour le développement initial et lors de l’utilisation de SQLite.</span><span class="sxs-lookup"><span data-stu-id="05ff7-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="05ff7-146">Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="05ff7-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="05ff7-147">L’avantage de cette approche est que vous conservez vos données.</span><span class="sxs-lookup"><span data-stu-id="05ff7-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="05ff7-148">Vous pouvez apporter cette modification manuellement ou en créant un script de modification de la base de données.</span><span class="sxs-lookup"><span data-stu-id="05ff7-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="05ff7-149">Utilisez les migrations Code First pour mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="05ff7-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="05ff7-150">Pour ce didacticiel, les migrations Code First sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="05ff7-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05ff7-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05ff7-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="05ff7-152">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package** .</span><span class="sxs-lookup"><span data-stu-id="05ff7-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console** .</span></span>

  ![Menu Console du Gestionnaire de package](adding-model/_static/pmc.png)

<span data-ttu-id="05ff7-154">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="05ff7-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="05ff7-155">La commande `Add-Migration` indique au framework de migration d’examiner le modèle `Movie` actuel avec le schéma de la base de données `Movie` actuel et de créer le code nécessaire pour migrer la base de données vers le nouveau modèle.</span><span class="sxs-lookup"><span data-stu-id="05ff7-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="05ff7-156">Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="05ff7-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="05ff7-157">Il est utile d’utiliser un nom explicite pour le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="05ff7-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="05ff7-158">Si tous les enregistrements de la base de données sont supprimés, la méthode d’initialisation amorce la base de données et inclut le champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="05ff7-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="05ff7-159">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="05ff7-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="05ff7-160">Supprimez la base de données et la migration précédente et utilisez les migrations pour recréer la base de données :</span><span class="sxs-lookup"><span data-stu-id="05ff7-160">Delete the database and the previous migration and use migrations to re-create the database:</span></span>

```dotnetcli
dotnet ef migrations remove
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

<span data-ttu-id="05ff7-161">`dotnet ef migrations remove` supprime la dernière migration.</span><span class="sxs-lookup"><span data-stu-id="05ff7-161">`dotnet ef migrations remove` removes the last migration.</span></span> <span data-ttu-id="05ff7-162">S’il y a plus d’une migration, supprimez le dossier migrations.</span><span class="sxs-lookup"><span data-stu-id="05ff7-162">If there are more than one migration, delete the Migrations folder.</span></span>

---
<!-- End of VS tabs -->

<span data-ttu-id="05ff7-163">Exécutez l’application et vérifiez que vous pouvez créer, modifier et afficher des films avec un `Rating` champ.</span><span class="sxs-lookup"><span data-stu-id="05ff7-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="05ff7-164">[Précédent](search.md) 
>  [Suivant](validation.md)</span><span class="sxs-lookup"><span data-stu-id="05ff7-164">[Previous](search.md)
[Next](validation.md)</span></span>
