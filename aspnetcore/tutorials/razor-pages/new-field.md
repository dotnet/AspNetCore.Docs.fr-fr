---
title: Partie 7, ajouter un nouveau champ
author: rick-anderson
description: Partie 7 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 852c26f89b229d89797d9ce6fce2983319defe9c
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101144"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="4ead2-103">Partie 7, ajouter un nouveau champ à une Razor page dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="4ead2-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="4ead2-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4ead2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4ead2-105">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4ead2-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4ead2-106">Dans cette section, Migrations [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First est utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="4ead2-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="4ead2-107">Ajouter un nouveau champ au modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-107">Add a new field to the model.</span></span>
* <span data-ttu-id="4ead2-108">Migrer la nouvelle modification du schéma des champs vers la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="4ead2-109">Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First :</span><span class="sxs-lookup"><span data-stu-id="4ead2-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="4ead2-110">Ajoute une [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré.</span><span class="sxs-lookup"><span data-stu-id="4ead2-110">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="4ead2-111">Si les classes de modèle ne sont pas synchronisées avec la base de données, EF lève une exception.</span><span class="sxs-lookup"><span data-stu-id="4ead2-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="4ead2-112">Vérification automatique que le schéma et le modèle sont synchronisés facilite la recherche de problèmes de code de base de données incohérents.</span><span class="sxs-lookup"><span data-stu-id="4ead2-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="4ead2-113">Ajout d’une propriété Rating au modèle Movie</span><span class="sxs-lookup"><span data-stu-id="4ead2-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="4ead2-114">Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :</span><span class="sxs-lookup"><span data-stu-id="4ead2-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="4ead2-115">Générez l'application.</span><span class="sxs-lookup"><span data-stu-id="4ead2-115">Build the app.</span></span>

1. <span data-ttu-id="4ead2-116">Modifiez *pages/movies/ Index . cshtml* et ajoutez un `Rating` champ :</span><span class="sxs-lookup"><span data-stu-id="4ead2-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="4ead2-117">Mettez à jour les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ead2-117">Update the following pages:</span></span>
   1. <span data-ttu-id="4ead2-118">Ajoutez le champ `Rating` aux pages Delete et Details.</span><span class="sxs-lookup"><span data-stu-id="4ead2-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="4ead2-119">Mettez à jour [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="4ead2-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="4ead2-120">Ajoutez le champ `Rating` à la Page Edit.</span><span class="sxs-lookup"><span data-stu-id="4ead2-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="4ead2-121">L’application ne fonctionne pas tant que la base de données n’a pas été mise à jour pour inclure le nouveau champ.</span><span class="sxs-lookup"><span data-stu-id="4ead2-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="4ead2-122">L’exécution de l’application sans mise à jour de la base de données lève une exception `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="4ead2-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="4ead2-123">L' `SqlException` exception est due au fait que la classe de modèle Movie mise à jour est différente du schéma de la table Movie de la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="4ead2-124">Il n’y a aucune `Rating` colonne dans la table de base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="4ead2-125">Plusieurs approches sont possibles pour résoudre l’erreur :</span><span class="sxs-lookup"><span data-stu-id="4ead2-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="4ead2-126">Laisser Entity Framework supprimer et recréer automatiquement la base de données avec le nouveau schéma de classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="4ead2-127">Cette approche est pratique au début du cycle de développement, ce qui vous permet de faire évoluer rapidement le schéma de base de données et le modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="4ead2-128">L’inconvénient est que vous perdez les données existantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="4ead2-129">N’utilisez pas cette approche sur une base de données de production !</span><span class="sxs-lookup"><span data-stu-id="4ead2-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="4ead2-130">La suppression de la base de données sur les modifications de schéma et l’utilisation d’un initialiseur pour amorcer automatiquement la base de données avec des données de test est souvent un moyen productif de développer une application.</span><span class="sxs-lookup"><span data-stu-id="4ead2-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="4ead2-131">Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="4ead2-132">L’avantage de cette approche est de conserver les données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="4ead2-133">Effectuez cette modification manuellement ou en créant un script de modification de la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="4ead2-134">Utilisez les migrations Code First pour mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="4ead2-135">Pour ce didacticiel, nous allons utiliser les migrations Code First.</span><span class="sxs-lookup"><span data-stu-id="4ead2-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="4ead2-136">Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne.</span><span class="sxs-lookup"><span data-stu-id="4ead2-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="4ead2-137">Un exemple de modification est présenté ci-dessous, mais apporte cette modification pour chaque `new Movie` bloc.</span><span class="sxs-lookup"><span data-stu-id="4ead2-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="4ead2-138">Consultez le [fichier SeedData.cs complet](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="4ead2-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="4ead2-139">Générez la solution.</span><span class="sxs-lookup"><span data-stu-id="4ead2-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ead2-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ead2-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="4ead2-141">Ajouter une migration pour le champ d’évaluation</span><span class="sxs-lookup"><span data-stu-id="4ead2-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="4ead2-142">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="4ead2-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="4ead2-143">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ead2-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="4ead2-144">La commande `Add-Migration` indique au framework qu’il doit :</span><span class="sxs-lookup"><span data-stu-id="4ead2-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="4ead2-145">Comparez le `Movie` modèle au `Movie` schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="4ead2-146">Créez du code pour migrer le schéma de base de données vers le nouveau modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="4ead2-147">Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="4ead2-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="4ead2-148">Il est utile d’utiliser un nom explicite pour le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="4ead2-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="4ead2-149">La `Update-Database` commande indique à l’infrastructure d’appliquer les modifications de schéma à la base de données et de conserver les données existantes.</span><span class="sxs-lookup"><span data-stu-id="4ead2-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="4ead2-150">Si vous supprimez tous les enregistrements de la base de données, l’initialiseur amorce la base de données et inclut le `Rating` champ.</span><span class="sxs-lookup"><span data-stu-id="4ead2-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="4ead2-151">Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox).</span><span class="sxs-lookup"><span data-stu-id="4ead2-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="4ead2-152">Une autre option consiste à supprimer la base de données et à utiliser des migrations pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="4ead2-153">Pour supprimer la base de données dans SSOX :</span><span class="sxs-lookup"><span data-stu-id="4ead2-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="4ead2-154">Sélectionnez la base de données dans SSOX.</span><span class="sxs-lookup"><span data-stu-id="4ead2-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="4ead2-155">Cliquez avec le bouton droit sur la base de données, puis sélectionnez **supprimer**.</span><span class="sxs-lookup"><span data-stu-id="4ead2-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="4ead2-156">Cochez **Fermer les connexions existantes**.</span><span class="sxs-lookup"><span data-stu-id="4ead2-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="4ead2-157">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="4ead2-157">Select **OK**.</span></span>
1. <span data-ttu-id="4ead2-158">Dans le [PMC](xref:tutorials/razor-pages/new-field#pmc), mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="4ead2-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4ead2-159">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="4ead2-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="4ead2-160">Supprimer et recréer la base de données</span><span class="sxs-lookup"><span data-stu-id="4ead2-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="4ead2-161">Pour ce didacticiel, vous utilisez la fonctionnalité *migrations* Entity Framework Core dans la mesure du possible.</span><span class="sxs-lookup"><span data-stu-id="4ead2-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="4ead2-162">Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="4ead2-163">Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées.</span><span class="sxs-lookup"><span data-stu-id="4ead2-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="4ead2-164">Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification.</span><span class="sxs-lookup"><span data-stu-id="4ead2-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="4ead2-165">Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue.</span><span class="sxs-lookup"><span data-stu-id="4ead2-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="4ead2-166">En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite.</span><span class="sxs-lookup"><span data-stu-id="4ead2-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="4ead2-167">À la place, vous supprimez puis recréez la base de données quand le schéma change.</span><span class="sxs-lookup"><span data-stu-id="4ead2-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="4ead2-168">Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié.</span><span class="sxs-lookup"><span data-stu-id="4ead2-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="4ead2-169">La regénération d’un tableau implique :</span><span class="sxs-lookup"><span data-stu-id="4ead2-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="4ead2-170">La création d’un nouveau tableau.</span><span class="sxs-lookup"><span data-stu-id="4ead2-170">Creating a new table.</span></span>
>* <span data-ttu-id="4ead2-171">La copie de données de l’ancien tableau vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="4ead2-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="4ead2-172">La suppression de l’ancien tableau.</span><span class="sxs-lookup"><span data-stu-id="4ead2-172">Dropping the old table.</span></span>
>* <span data-ttu-id="4ead2-173">Renommer la nouvelle table.</span><span class="sxs-lookup"><span data-stu-id="4ead2-173">Renaming the new table.</span></span>
>
><span data-ttu-id="4ead2-174">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ead2-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="4ead2-175">Limites d’un fournisseur de base de données EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="4ead2-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="4ead2-176">Personnaliser le code de migration</span><span class="sxs-lookup"><span data-stu-id="4ead2-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="4ead2-177">Amorçage des données</span><span class="sxs-lookup"><span data-stu-id="4ead2-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="4ead2-178">Instruction SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="4ead2-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="4ead2-179">Supprimer le dossier de migration.</span><span class="sxs-lookup"><span data-stu-id="4ead2-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="4ead2-180">Utilisez les commandes suivantes pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="4ead2-181">Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="4ead2-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="4ead2-182">Si la base de données n’est pas amorcée, définissez un point d’arrêt dans la méthode `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="4ead2-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ead2-183">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4ead2-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4ead2-184">[Précédent : ajouter une recherche](xref:tutorials/razor-pages/search) 
>  [Suivant : ajouter une validation](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="4ead2-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="4ead2-185">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4ead2-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4ead2-186">Dans cette section, Migrations [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First est utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="4ead2-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="4ead2-187">Ajouter un nouveau champ au modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-187">Add a new field to the model.</span></span>
* <span data-ttu-id="4ead2-188">Migrer la nouvelle modification du schéma des champs vers la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="4ead2-189">Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First :</span><span class="sxs-lookup"><span data-stu-id="4ead2-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="4ead2-190">Ajoute une [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré.</span><span class="sxs-lookup"><span data-stu-id="4ead2-190">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="4ead2-191">Si les classes de modèle ne sont pas synchronisées avec la base de données, EF lève une exception.</span><span class="sxs-lookup"><span data-stu-id="4ead2-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="4ead2-192">Vérification automatique que le schéma et le modèle sont synchronisés facilite la recherche de problèmes de code de base de données incohérents.</span><span class="sxs-lookup"><span data-stu-id="4ead2-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="4ead2-193">Ajout d’une propriété Rating au modèle Movie</span><span class="sxs-lookup"><span data-stu-id="4ead2-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="4ead2-194">Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :</span><span class="sxs-lookup"><span data-stu-id="4ead2-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="4ead2-195">Générez l'application.</span><span class="sxs-lookup"><span data-stu-id="4ead2-195">Build the app.</span></span>

1. <span data-ttu-id="4ead2-196">Modifiez *pages/movies/ Index . cshtml* et ajoutez un `Rating` champ :</span><span class="sxs-lookup"><span data-stu-id="4ead2-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="4ead2-197">Mettez à jour les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ead2-197">Update the following pages:</span></span>
   1. <span data-ttu-id="4ead2-198">Ajoutez le champ `Rating` aux pages Delete et Details.</span><span class="sxs-lookup"><span data-stu-id="4ead2-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="4ead2-199">Mettez à jour [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="4ead2-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="4ead2-200">Ajoutez le champ `Rating` à la Page Edit.</span><span class="sxs-lookup"><span data-stu-id="4ead2-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="4ead2-201">L’application ne fonctionne pas tant que la base de données n’a pas été mise à jour pour inclure le nouveau champ.</span><span class="sxs-lookup"><span data-stu-id="4ead2-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="4ead2-202">L’exécution de l’application sans mise à jour de la base de données lève une exception `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="4ead2-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="4ead2-203">L' `SqlException` exception est due au fait que la classe de modèle Movie mise à jour est différente du schéma de la table Movie de la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="4ead2-204">Il n’y a aucune `Rating` colonne dans la table de base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="4ead2-205">Plusieurs approches sont possibles pour résoudre l’erreur :</span><span class="sxs-lookup"><span data-stu-id="4ead2-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="4ead2-206">Laisser Entity Framework supprimer et recréer automatiquement la base de données avec le nouveau schéma de classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="4ead2-207">Cette approche est pratique au début du cycle de développement, ce qui vous permet de faire évoluer rapidement le schéma de base de données et le modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="4ead2-208">L’inconvénient est que vous perdez les données existantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="4ead2-209">N’utilisez pas cette approche sur une base de données de production !</span><span class="sxs-lookup"><span data-stu-id="4ead2-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="4ead2-210">La suppression de la base de données sur les modifications de schéma et l’utilisation d’un initialiseur pour amorcer automatiquement la base de données avec des données de test est souvent un moyen productif de développer une application.</span><span class="sxs-lookup"><span data-stu-id="4ead2-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="4ead2-211">Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="4ead2-212">L’avantage de cette approche est de conserver les données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="4ead2-213">Effectuez cette modification manuellement ou en créant un script de modification de la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="4ead2-214">Utilisez les migrations Code First pour mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="4ead2-215">Pour ce didacticiel, nous allons utiliser les migrations Code First.</span><span class="sxs-lookup"><span data-stu-id="4ead2-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="4ead2-216">Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne.</span><span class="sxs-lookup"><span data-stu-id="4ead2-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="4ead2-217">Un exemple de modification est présenté ci-dessous, mais apporte cette modification pour chaque `new Movie` bloc.</span><span class="sxs-lookup"><span data-stu-id="4ead2-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="4ead2-218">Consultez le [fichier SeedData.cs complet](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="4ead2-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="4ead2-219">Générez la solution.</span><span class="sxs-lookup"><span data-stu-id="4ead2-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ead2-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ead2-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="4ead2-221">Ajouter une migration pour le champ d’évaluation</span><span class="sxs-lookup"><span data-stu-id="4ead2-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="4ead2-222">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="4ead2-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="4ead2-223">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ead2-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="4ead2-224">La commande `Add-Migration` indique au framework qu’il doit :</span><span class="sxs-lookup"><span data-stu-id="4ead2-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="4ead2-225">Comparez le `Movie` modèle au `Movie` schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="4ead2-226">Créez du code pour migrer le schéma de base de données vers le nouveau modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="4ead2-227">Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="4ead2-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="4ead2-228">Il est utile d’utiliser un nom explicite pour le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="4ead2-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="4ead2-229">La `Update-Database` commande indique à l’infrastructure d’appliquer les modifications de schéma à la base de données et de conserver les données existantes.</span><span class="sxs-lookup"><span data-stu-id="4ead2-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="4ead2-230">Si vous supprimez tous les enregistrements de la base de données, l’initialiseur amorce la base de données et inclut le `Rating` champ.</span><span class="sxs-lookup"><span data-stu-id="4ead2-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="4ead2-231">Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox).</span><span class="sxs-lookup"><span data-stu-id="4ead2-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="4ead2-232">Une autre option consiste à supprimer la base de données et à utiliser des migrations pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="4ead2-233">Pour supprimer la base de données dans SSOX :</span><span class="sxs-lookup"><span data-stu-id="4ead2-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="4ead2-234">Sélectionnez la base de données dans SSOX.</span><span class="sxs-lookup"><span data-stu-id="4ead2-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="4ead2-235">Cliquez avec le bouton droit sur la base de données, puis sélectionnez **supprimer**.</span><span class="sxs-lookup"><span data-stu-id="4ead2-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="4ead2-236">Cochez **Fermer les connexions existantes**.</span><span class="sxs-lookup"><span data-stu-id="4ead2-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="4ead2-237">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="4ead2-237">Select **OK**.</span></span>
* <span data-ttu-id="4ead2-238">Dans le [PMC](xref:tutorials/razor-pages/new-field#pmc), mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="4ead2-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4ead2-239">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="4ead2-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="4ead2-240">Supprimer et recréer la base de données</span><span class="sxs-lookup"><span data-stu-id="4ead2-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="4ead2-241">Pour ce didacticiel, vous utilisez la fonctionnalité de *migrations* Entity Framework Core dans la mesure du possible.</span><span class="sxs-lookup"><span data-stu-id="4ead2-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="4ead2-242">Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="4ead2-243">Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées.</span><span class="sxs-lookup"><span data-stu-id="4ead2-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="4ead2-244">Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification.</span><span class="sxs-lookup"><span data-stu-id="4ead2-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="4ead2-245">Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue.</span><span class="sxs-lookup"><span data-stu-id="4ead2-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="4ead2-246">En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite.</span><span class="sxs-lookup"><span data-stu-id="4ead2-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="4ead2-247">À la place, vous supprimez puis recréez la base de données quand le schéma change.</span><span class="sxs-lookup"><span data-stu-id="4ead2-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="4ead2-248">Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié.</span><span class="sxs-lookup"><span data-stu-id="4ead2-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="4ead2-249">La regénération d’un tableau implique :</span><span class="sxs-lookup"><span data-stu-id="4ead2-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="4ead2-250">La création d’un nouveau tableau.</span><span class="sxs-lookup"><span data-stu-id="4ead2-250">Creating a new table.</span></span>
>* <span data-ttu-id="4ead2-251">La copie de données de l’ancien tableau vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="4ead2-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="4ead2-252">La suppression de l’ancien tableau.</span><span class="sxs-lookup"><span data-stu-id="4ead2-252">Dropping the old table.</span></span>
>* <span data-ttu-id="4ead2-253">Renommer la nouvelle table.</span><span class="sxs-lookup"><span data-stu-id="4ead2-253">Renaming the new table.</span></span>
>
><span data-ttu-id="4ead2-254">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ead2-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="4ead2-255">Limites d’un fournisseur de base de données EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="4ead2-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="4ead2-256">Personnaliser le code de migration</span><span class="sxs-lookup"><span data-stu-id="4ead2-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="4ead2-257">Amorçage des données</span><span class="sxs-lookup"><span data-stu-id="4ead2-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="4ead2-258">Instruction SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="4ead2-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="4ead2-259">Supprimer le dossier de migration.</span><span class="sxs-lookup"><span data-stu-id="4ead2-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="4ead2-260">Utilisez les commandes suivantes pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="4ead2-261">Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="4ead2-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="4ead2-262">Si la base de données n’est pas amorcée, définissez un point d’arrêt dans la méthode `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="4ead2-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ead2-263">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4ead2-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4ead2-264">[Précédent : ajouter une recherche](xref:tutorials/razor-pages/search) 
>  [Suivant : ajouter une validation](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="4ead2-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4ead2-265">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4ead2-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4ead2-266">Dans cette section, Migrations [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First est utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="4ead2-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="4ead2-267">Ajouter un nouveau champ au modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-267">Add a new field to the model.</span></span>
* <span data-ttu-id="4ead2-268">Migrer la nouvelle modification du schéma des champs vers la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="4ead2-269">Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First :</span><span class="sxs-lookup"><span data-stu-id="4ead2-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="4ead2-270">Ajoute une [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré.</span><span class="sxs-lookup"><span data-stu-id="4ead2-270">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="4ead2-271">Si les classes de modèle ne sont pas synchronisées avec la base de données, EF lève une exception.</span><span class="sxs-lookup"><span data-stu-id="4ead2-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="4ead2-272">Vérification automatique que le schéma et le modèle sont synchronisés facilite la recherche de problèmes de code de base de données incohérents.</span><span class="sxs-lookup"><span data-stu-id="4ead2-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="4ead2-273">Ajout d’une propriété Rating au modèle Movie</span><span class="sxs-lookup"><span data-stu-id="4ead2-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="4ead2-274">Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :</span><span class="sxs-lookup"><span data-stu-id="4ead2-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="4ead2-275">Générez l'application.</span><span class="sxs-lookup"><span data-stu-id="4ead2-275">Build the app.</span></span>

<span data-ttu-id="4ead2-276">Modifiez *pages/movies/ Index . cshtml* et ajoutez un `Rating` champ :</span><span class="sxs-lookup"><span data-stu-id="4ead2-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="4ead2-277">Mettez à jour les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ead2-277">Update the following pages:</span></span>

* <span data-ttu-id="4ead2-278">Ajoutez le champ `Rating` aux pages Delete et Details.</span><span class="sxs-lookup"><span data-stu-id="4ead2-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="4ead2-279">Mettez à jour [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="4ead2-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="4ead2-280">Ajoutez le champ `Rating` à la Page Edit.</span><span class="sxs-lookup"><span data-stu-id="4ead2-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="4ead2-281">L’application ne fonctionne pas tant que la base de données n’a pas été mise à jour pour inclure le nouveau champ.</span><span class="sxs-lookup"><span data-stu-id="4ead2-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="4ead2-282">Si l’application est exécutée maintenant, l’application lève une exception `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="4ead2-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="4ead2-283">Cette erreur est due au fait que la classe du modèle Movie mise à jour est différente du schéma de la table Movie de la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="4ead2-284">Il n’y a aucune `Rating` colonne dans la table de base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="4ead2-285">Plusieurs approches sont possibles pour résoudre l’erreur :</span><span class="sxs-lookup"><span data-stu-id="4ead2-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="4ead2-286">Laisser Entity Framework supprimer et recréer automatiquement la base de données avec le nouveau schéma de classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="4ead2-287">Cette approche est pratique au début du cycle de développement, ce qui vous permet de faire évoluer rapidement le schéma de base de données et le modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="4ead2-288">L’inconvénient est que vous perdez les données existantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="4ead2-289">N’utilisez pas cette approche sur une base de données de production !</span><span class="sxs-lookup"><span data-stu-id="4ead2-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="4ead2-290">La suppression de la base de données sur les modifications de schéma et l’utilisation d’un initialiseur pour amorcer automatiquement la base de données avec des données de test est souvent un moyen productif de développer une application.</span><span class="sxs-lookup"><span data-stu-id="4ead2-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="4ead2-291">Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="4ead2-292">L’avantage de cette approche est de conserver les données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="4ead2-293">Effectuez cette modification manuellement ou en créant un script de modification de la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="4ead2-294">Utilisez les migrations Code First pour mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="4ead2-295">Pour ce didacticiel, nous allons utiliser les migrations Code First.</span><span class="sxs-lookup"><span data-stu-id="4ead2-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="4ead2-296">Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne.</span><span class="sxs-lookup"><span data-stu-id="4ead2-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="4ead2-297">Un exemple de modification est présenté ci-dessous, mais apporte cette modification pour chaque `new Movie` bloc.</span><span class="sxs-lookup"><span data-stu-id="4ead2-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="4ead2-298">Consultez le [fichier SeedData.cs complet](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="4ead2-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="4ead2-299">Générez la solution.</span><span class="sxs-lookup"><span data-stu-id="4ead2-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ead2-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ead2-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="4ead2-301">Ajouter une migration pour le champ d’évaluation</span><span class="sxs-lookup"><span data-stu-id="4ead2-301">Add a migration for the rating field</span></span>

<span data-ttu-id="4ead2-302">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="4ead2-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="4ead2-303">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ead2-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="4ead2-304">La commande `Add-Migration` indique au framework qu’il doit :</span><span class="sxs-lookup"><span data-stu-id="4ead2-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="4ead2-305">Comparez le `Movie` modèle au `Movie` schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="4ead2-306">Créez du code pour migrer le schéma de base de données vers le nouveau modèle.</span><span class="sxs-lookup"><span data-stu-id="4ead2-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="4ead2-307">Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="4ead2-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="4ead2-308">Il est utile d’utiliser un nom explicite pour le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="4ead2-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="4ead2-309">La commande `Update-Database` demande à l’infrastructure d’appliquer les modifications de schéma à la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="4ead2-310">Si vous supprimez tous les enregistrements de la base de données, l’initialiseur amorce la base de données et inclut le `Rating` champ.</span><span class="sxs-lookup"><span data-stu-id="4ead2-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="4ead2-311">Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox).</span><span class="sxs-lookup"><span data-stu-id="4ead2-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="4ead2-312">Une autre option consiste à supprimer la base de données et à utiliser des migrations pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="4ead2-313">Pour supprimer la base de données dans SSOX :</span><span class="sxs-lookup"><span data-stu-id="4ead2-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="4ead2-314">Sélectionnez la base de données dans SSOX.</span><span class="sxs-lookup"><span data-stu-id="4ead2-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="4ead2-315">Cliquez avec le bouton droit sur la base de données, puis sélectionnez **supprimer**.</span><span class="sxs-lookup"><span data-stu-id="4ead2-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="4ead2-316">Cochez **Fermer les connexions existantes**.</span><span class="sxs-lookup"><span data-stu-id="4ead2-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="4ead2-317">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="4ead2-317">Select **OK**.</span></span>
* <span data-ttu-id="4ead2-318">Dans le [PMC](xref:tutorials/razor-pages/new-field#pmc), mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="4ead2-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4ead2-319">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="4ead2-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="4ead2-320">Supprimer et recréer la base de données</span><span class="sxs-lookup"><span data-stu-id="4ead2-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="4ead2-321">Pour ce didacticiel, vous utilisez la fonctionnalité de *migrations* Entity Framework Core dans la mesure du possible.</span><span class="sxs-lookup"><span data-stu-id="4ead2-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="4ead2-322">Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="4ead2-323">Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées.</span><span class="sxs-lookup"><span data-stu-id="4ead2-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="4ead2-324">Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification.</span><span class="sxs-lookup"><span data-stu-id="4ead2-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="4ead2-325">Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue.</span><span class="sxs-lookup"><span data-stu-id="4ead2-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="4ead2-326">En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite.</span><span class="sxs-lookup"><span data-stu-id="4ead2-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="4ead2-327">À la place, vous supprimez puis recréez la base de données quand le schéma change.</span><span class="sxs-lookup"><span data-stu-id="4ead2-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="4ead2-328">Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié.</span><span class="sxs-lookup"><span data-stu-id="4ead2-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="4ead2-329">La regénération d’un tableau implique :</span><span class="sxs-lookup"><span data-stu-id="4ead2-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="4ead2-330">La création d’un nouveau tableau.</span><span class="sxs-lookup"><span data-stu-id="4ead2-330">Creating a new table.</span></span>
>* <span data-ttu-id="4ead2-331">La copie de données de l’ancien tableau vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="4ead2-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="4ead2-332">La suppression de l’ancien tableau.</span><span class="sxs-lookup"><span data-stu-id="4ead2-332">Dropping the old table.</span></span>
>* <span data-ttu-id="4ead2-333">Renommer la nouvelle table.</span><span class="sxs-lookup"><span data-stu-id="4ead2-333">Renaming the new table.</span></span>
>
><span data-ttu-id="4ead2-334">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ead2-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="4ead2-335">Limites d’un fournisseur de base de données EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="4ead2-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="4ead2-336">Personnaliser le code de migration</span><span class="sxs-lookup"><span data-stu-id="4ead2-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="4ead2-337">Amorçage des données</span><span class="sxs-lookup"><span data-stu-id="4ead2-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="4ead2-338">Instruction SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="4ead2-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="4ead2-339">Supprimer la base de données et utiliser des migrations pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="4ead2-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="4ead2-340">Pour supprimer la base de données, supprimez le fichier de base de données (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="4ead2-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="4ead2-341">Ensuite, exécutez la commande `ef database update` :</span><span class="sxs-lookup"><span data-stu-id="4ead2-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="4ead2-342">Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="4ead2-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="4ead2-343">Si la base de données n’est pas amorcée, définissez un point d’arrêt dans la méthode `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="4ead2-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ead2-344">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4ead2-344">Additional resources</span></span>

* [<span data-ttu-id="4ead2-345">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="4ead2-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="4ead2-346">[Précédent : ajouter une recherche](xref:tutorials/razor-pages/search) 
>  [Suivant : ajouter une validation](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="4ead2-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end