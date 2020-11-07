---
title: Partie 7, ajouter un nouveau champ
author: rick-anderson
description: 'Partie 7 de la série de didacticiels sur les :::no-loc(Razor)::: pages.'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
no-loc:
- ':::no-loc(Index):::'
- ':::no-loc(Create):::'
- ':::no-loc(Delete):::'
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 2dca5a9552dd2800212f8cd78ace0578b3d38cdb
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360877"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="a7fc8-103">Partie 7, ajouter un nouveau champ à une :::no-loc(Razor)::: page dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="a7fc8-103">Part 7, add a new field to a :::no-loc(Razor)::: Page in ASP.NET Core</span></span>

<span data-ttu-id="a7fc8-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a7fc8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a7fc8-105">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a7fc8-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="a7fc8-106">Dans cette section, Migrations [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First est utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="a7fc8-107">Ajouter un nouveau champ au modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-107">Add a new field to the model.</span></span>
* <span data-ttu-id="a7fc8-108">Migrer la nouvelle modification du schéma des champs vers la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="a7fc8-109">Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="a7fc8-110">Ajoute une [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="a7fc8-111">Si les classes de modèle ne sont pas synchronisées avec la base de données, EF lève une exception.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="a7fc8-112">Vérification automatique que le schéma et le modèle sont synchronisés facilite la recherche de problèmes de code de base de données incohérents.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="a7fc8-113">Ajout d’une propriété Rating au modèle Movie</span><span class="sxs-lookup"><span data-stu-id="a7fc8-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="a7fc8-114">Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="a7fc8-115">Générez l'application.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-115">Build the app.</span></span>

1. <span data-ttu-id="a7fc8-116">Modifiez *pages/movies/ :::no-loc(Index)::: . cshtml* et ajoutez un `Rating` champ :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-116">Edit *Pages/Movies/:::no-loc(Index):::.cshtml* , and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/SnapShots/:::no-loc(Index):::Rating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="a7fc8-117">Mettez à jour les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-117">Update the following pages:</span></span>
   1. <span data-ttu-id="a7fc8-118">Ajoutez le `Rating` champ aux :::no-loc(Delete)::: pages détails et.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-118">Add the `Rating` field to the :::no-loc(Delete)::: and Details pages.</span></span>
   1. <span data-ttu-id="a7fc8-119">Mettez à jour [ :::no-loc(Create)::: . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Pages/Movies/:::no-loc(Create):::.cshtml) avec un `Rating` champ.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-119">Update [:::no-loc(Create):::.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Pages/Movies/:::no-loc(Create):::.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="a7fc8-120">Ajoutez le champ `Rating` à la Page Edit.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="a7fc8-121">L’application ne fonctionne pas tant que la base de données n’a pas été mise à jour pour inclure le nouveau champ.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="a7fc8-122">L’exécution de l’application sans mise à jour de la base de données lève une exception `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="a7fc8-123">L' `SqlException` exception est due au fait que la classe de modèle Movie mise à jour est différente du schéma de la table Movie de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="a7fc8-124">Il n’y a aucune `Rating` colonne dans la table de base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="a7fc8-125">Plusieurs approches sont possibles pour résoudre l’erreur :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="a7fc8-126">Laisser Entity Framework supprimer et recréer automatiquement la base de données avec le nouveau schéma de classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="a7fc8-127">Cette approche est pratique au début du cycle de développement, ce qui vous permet de faire évoluer rapidement le schéma de base de données et le modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="a7fc8-128">L’inconvénient est que vous perdez les données existantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="a7fc8-129">N’utilisez pas cette approche sur une base de données de production !</span><span class="sxs-lookup"><span data-stu-id="a7fc8-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="a7fc8-130">La suppression de la base de données sur les modifications de schéma et l’utilisation d’un initialiseur pour amorcer automatiquement la base de données avec des données de test est souvent un moyen productif de développer une application.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="a7fc8-131">Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="a7fc8-132">L’avantage de cette approche est de conserver les données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="a7fc8-133">Effectuez cette modification manuellement ou en créant un script de modification de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="a7fc8-134">Utilisez les migrations Code First pour mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="a7fc8-135">Pour ce didacticiel, nous allons utiliser les migrations Code First.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="a7fc8-136">Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="a7fc8-137">Un exemple de modification est présenté ci-dessous, mais apporte cette modification pour chaque `new Movie` bloc.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="a7fc8-138">Consultez le [fichier SeedData.cs complet](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="a7fc8-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="a7fc8-139">Générez la solution.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a7fc8-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a7fc8-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="a7fc8-141">Ajouter une migration pour le champ d’évaluation</span><span class="sxs-lookup"><span data-stu-id="a7fc8-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="a7fc8-142">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="a7fc8-143">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="a7fc8-144">La commande `Add-Migration` indique au framework qu’il doit :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="a7fc8-145">Comparez le `Movie` modèle au `Movie` schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="a7fc8-146">:::no-loc(Create)::: code pour migrer le schéma de base de données vers le nouveau modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-146">:::no-loc(Create)::: code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="a7fc8-147">Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="a7fc8-148">Il est utile d’utiliser un nom explicite pour le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="a7fc8-149">La `Update-Database` commande indique à l’infrastructure d’appliquer les modifications de schéma à la base de données et de conserver les données existantes.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="a7fc8-150">Si vous supprimez tous les enregistrements de la base de données, l’initialiseur amorce la base de données et inclut le `Rating` champ.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="a7fc8-151">Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox).</span><span class="sxs-lookup"><span data-stu-id="a7fc8-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="a7fc8-152">Une autre option consiste à supprimer la base de données et à utiliser des migrations pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="a7fc8-153">Pour supprimer la base de données dans SSOX :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="a7fc8-154">Sélectionnez la base de données dans SSOX.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="a7fc8-155">Cliquez avec le bouton droit sur la base de données, puis sélectionnez **:::no-loc(Delete):::** .</span><span class="sxs-lookup"><span data-stu-id="a7fc8-155">Right-click on the database, and select **:::no-loc(Delete):::**.</span></span>
1. <span data-ttu-id="a7fc8-156">Cochez **Fermer les connexions existantes**.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="a7fc8-157">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-157">Select **OK**.</span></span>
1. <span data-ttu-id="a7fc8-158">Dans le [PMC](xref:tutorials/razor-pages/new-field#pmc), mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a7fc8-159">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a7fc8-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="a7fc8-160">Supprimer et recréer la base de données</span><span class="sxs-lookup"><span data-stu-id="a7fc8-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="a7fc8-161">Pour ce didacticiel, vous utilisez la fonctionnalité *migrations* Entity Framework Core dans la mesure du possible.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="a7fc8-162">Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="a7fc8-163">Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="a7fc8-164">Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="a7fc8-165">Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="a7fc8-166">En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="a7fc8-167">À la place, vous supprimez puis recréez la base de données quand le schéma change.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="a7fc8-168">Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="a7fc8-169">La regénération d’un tableau implique :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="a7fc8-170">La création d’un nouveau tableau.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-170">Creating a new table.</span></span>
>* <span data-ttu-id="a7fc8-171">La copie de données de l’ancien tableau vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="a7fc8-172">La suppression de l’ancien tableau.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-172">Dropping the old table.</span></span>
>* <span data-ttu-id="a7fc8-173">Renommer la nouvelle table.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-173">Renaming the new table.</span></span>
>
><span data-ttu-id="a7fc8-174">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="a7fc8-175">Limites d’un fournisseur de base de données EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="a7fc8-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="a7fc8-176">Personnaliser le code de migration</span><span class="sxs-lookup"><span data-stu-id="a7fc8-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="a7fc8-177">Amorçage des données</span><span class="sxs-lookup"><span data-stu-id="a7fc8-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="a7fc8-178">Instruction SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="a7fc8-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="a7fc8-179">:::no-loc(Delete)::: dossier de migration.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-179">:::no-loc(Delete)::: the migration folder.</span></span>  

1. <span data-ttu-id="a7fc8-180">Utilisez les commandes suivantes pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add Initial:::no-loc(Create):::
   dotnet ef database update
   ```

---

<span data-ttu-id="a7fc8-181">Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="a7fc8-182">Si la base de données n’est pas amorcée, définissez un point d’arrêt dans la méthode `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7fc8-183">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a7fc8-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a7fc8-184">[Précédent : ajouter une recherche](xref:tutorials/razor-pages/search) 
>  [Suivant : ajouter une validation](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="a7fc8-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="a7fc8-185">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a7fc8-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="a7fc8-186">Dans cette section, Migrations [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First est utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="a7fc8-187">Ajouter un nouveau champ au modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-187">Add a new field to the model.</span></span>
* <span data-ttu-id="a7fc8-188">Migrer la nouvelle modification du schéma des champs vers la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="a7fc8-189">Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="a7fc8-190">Ajoute une [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="a7fc8-191">Si les classes de modèle ne sont pas synchronisées avec la base de données, EF lève une exception.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="a7fc8-192">Vérification automatique que le schéma et le modèle sont synchronisés facilite la recherche de problèmes de code de base de données incohérents.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="a7fc8-193">Ajout d’une propriété Rating au modèle Movie</span><span class="sxs-lookup"><span data-stu-id="a7fc8-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="a7fc8-194">Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="a7fc8-195">Générez l'application.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-195">Build the app.</span></span>

1. <span data-ttu-id="a7fc8-196">Modifiez *pages/movies/ :::no-loc(Index)::: . cshtml* et ajoutez un `Rating` champ :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-196">Edit *Pages/Movies/:::no-loc(Index):::.cshtml* , and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/SnapShots/:::no-loc(Index):::Rating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="a7fc8-197">Mettez à jour les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-197">Update the following pages:</span></span>
   1. <span data-ttu-id="a7fc8-198">Ajoutez le `Rating` champ aux :::no-loc(Delete)::: pages détails et.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-198">Add the `Rating` field to the :::no-loc(Delete)::: and Details pages.</span></span>
   1. <span data-ttu-id="a7fc8-199">Mettez à jour [ :::no-loc(Create)::: . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml) avec un `Rating` champ.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-199">Update [:::no-loc(Create):::.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/:::no-loc(Create):::.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="a7fc8-200">Ajoutez le champ `Rating` à la Page Edit.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="a7fc8-201">L’application ne fonctionne pas tant que la base de données n’a pas été mise à jour pour inclure le nouveau champ.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="a7fc8-202">L’exécution de l’application sans mise à jour de la base de données lève une exception `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="a7fc8-203">L' `SqlException` exception est due au fait que la classe de modèle Movie mise à jour est différente du schéma de la table Movie de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="a7fc8-204">Il n’y a aucune `Rating` colonne dans la table de base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="a7fc8-205">Plusieurs approches sont possibles pour résoudre l’erreur :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="a7fc8-206">Laisser Entity Framework supprimer et recréer automatiquement la base de données avec le nouveau schéma de classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="a7fc8-207">Cette approche est pratique au début du cycle de développement, ce qui vous permet de faire évoluer rapidement le schéma de base de données et le modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="a7fc8-208">L’inconvénient est que vous perdez les données existantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="a7fc8-209">N’utilisez pas cette approche sur une base de données de production !</span><span class="sxs-lookup"><span data-stu-id="a7fc8-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="a7fc8-210">La suppression de la base de données sur les modifications de schéma et l’utilisation d’un initialiseur pour amorcer automatiquement la base de données avec des données de test est souvent un moyen productif de développer une application.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="a7fc8-211">Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="a7fc8-212">L’avantage de cette approche est de conserver les données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="a7fc8-213">Effectuez cette modification manuellement ou en créant un script de modification de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="a7fc8-214">Utilisez les migrations Code First pour mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="a7fc8-215">Pour ce didacticiel, nous allons utiliser les migrations Code First.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="a7fc8-216">Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="a7fc8-217">Un exemple de modification est présenté ci-dessous, mais apporte cette modification pour chaque `new Movie` bloc.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="a7fc8-218">Consultez le [fichier SeedData.cs complet](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="a7fc8-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="a7fc8-219">Générez la solution.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a7fc8-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a7fc8-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="a7fc8-221">Ajouter une migration pour le champ d’évaluation</span><span class="sxs-lookup"><span data-stu-id="a7fc8-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="a7fc8-222">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="a7fc8-223">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="a7fc8-224">La commande `Add-Migration` indique au framework qu’il doit :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="a7fc8-225">Comparez le `Movie` modèle au `Movie` schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="a7fc8-226">:::no-loc(Create)::: code pour migrer le schéma de base de données vers le nouveau modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-226">:::no-loc(Create)::: code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="a7fc8-227">Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="a7fc8-228">Il est utile d’utiliser un nom explicite pour le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="a7fc8-229">La `Update-Database` commande indique à l’infrastructure d’appliquer les modifications de schéma à la base de données et de conserver les données existantes.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="a7fc8-230">Si vous supprimez tous les enregistrements de la base de données, l’initialiseur amorce la base de données et inclut le `Rating` champ.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="a7fc8-231">Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox).</span><span class="sxs-lookup"><span data-stu-id="a7fc8-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="a7fc8-232">Une autre option consiste à supprimer la base de données et à utiliser des migrations pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="a7fc8-233">Pour supprimer la base de données dans SSOX :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="a7fc8-234">Sélectionnez la base de données dans SSOX.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="a7fc8-235">Cliquez avec le bouton droit sur la base de données, puis sélectionnez **:::no-loc(Delete):::** .</span><span class="sxs-lookup"><span data-stu-id="a7fc8-235">Right-click on the database, and select **:::no-loc(Delete):::**.</span></span>
* <span data-ttu-id="a7fc8-236">Cochez **Fermer les connexions existantes**.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="a7fc8-237">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-237">Select **OK**.</span></span>
* <span data-ttu-id="a7fc8-238">Dans le [PMC](xref:tutorials/razor-pages/new-field#pmc), mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a7fc8-239">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a7fc8-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="a7fc8-240">Supprimer et recréer la base de données</span><span class="sxs-lookup"><span data-stu-id="a7fc8-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="a7fc8-241">Pour ce didacticiel, vous utilisez la fonctionnalité de *migrations* Entity Framework Core dans la mesure du possible.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="a7fc8-242">Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="a7fc8-243">Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="a7fc8-244">Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="a7fc8-245">Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="a7fc8-246">En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="a7fc8-247">À la place, vous supprimez puis recréez la base de données quand le schéma change.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="a7fc8-248">Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="a7fc8-249">La regénération d’un tableau implique :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="a7fc8-250">La création d’un nouveau tableau.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-250">Creating a new table.</span></span>
>* <span data-ttu-id="a7fc8-251">La copie de données de l’ancien tableau vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="a7fc8-252">La suppression de l’ancien tableau.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-252">Dropping the old table.</span></span>
>* <span data-ttu-id="a7fc8-253">Renommer la nouvelle table.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-253">Renaming the new table.</span></span>
>
><span data-ttu-id="a7fc8-254">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="a7fc8-255">Limites d’un fournisseur de base de données EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="a7fc8-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="a7fc8-256">Personnaliser le code de migration</span><span class="sxs-lookup"><span data-stu-id="a7fc8-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="a7fc8-257">Amorçage des données</span><span class="sxs-lookup"><span data-stu-id="a7fc8-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="a7fc8-258">Instruction SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="a7fc8-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="a7fc8-259">:::no-loc(Delete)::: dossier de migration.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-259">:::no-loc(Delete)::: the migration folder.</span></span>  

1. <span data-ttu-id="a7fc8-260">Utilisez les commandes suivantes pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add Initial:::no-loc(Create):::
   dotnet ef database update
   ```

---

<span data-ttu-id="a7fc8-261">Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="a7fc8-262">Si la base de données n’est pas amorcée, définissez un point d’arrêt dans la méthode `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7fc8-263">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a7fc8-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a7fc8-264">[Précédent : ajouter une recherche](xref:tutorials/razor-pages/search) 
>  [Suivant : ajouter une validation](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="a7fc8-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a7fc8-265">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a7fc8-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="a7fc8-266">Dans cette section, Migrations [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First est utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="a7fc8-267">Ajouter un nouveau champ au modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-267">Add a new field to the model.</span></span>
* <span data-ttu-id="a7fc8-268">Migrer la nouvelle modification du schéma des champs vers la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="a7fc8-269">Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="a7fc8-270">Ajoute une [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="a7fc8-271">Si les classes de modèle ne sont pas synchronisées avec la base de données, EF lève une exception.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="a7fc8-272">Vérification automatique que le schéma et le modèle sont synchronisés facilite la recherche de problèmes de code de base de données incohérents.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="a7fc8-273">Ajout d’une propriété Rating au modèle Movie</span><span class="sxs-lookup"><span data-stu-id="a7fc8-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="a7fc8-274">Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="a7fc8-275">Générez l'application.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-275">Build the app.</span></span>

<span data-ttu-id="a7fc8-276">Modifiez *pages/movies/ :::no-loc(Index)::: . cshtml* et ajoutez un `Rating` champ :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-276">Edit *Pages/Movies/:::no-loc(Index):::.cshtml* , and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/:::no-loc(Index):::Rating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="a7fc8-277">Mettez à jour les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-277">Update the following pages:</span></span>

* <span data-ttu-id="a7fc8-278">Ajoutez le `Rating` champ aux :::no-loc(Delete)::: pages détails et.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-278">Add the `Rating` field to the :::no-loc(Delete)::: and Details pages.</span></span>
* <span data-ttu-id="a7fc8-279">Mettez à jour [ :::no-loc(Create)::: . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/:::no-loc(Create):::.cshtml) avec un `Rating` champ.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-279">Update [:::no-loc(Create):::.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/:::no-loc(Create):::.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="a7fc8-280">Ajoutez le champ `Rating` à la Page Edit.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="a7fc8-281">L’application ne fonctionne pas tant que la base de données n’a pas été mise à jour pour inclure le nouveau champ.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="a7fc8-282">Si l’application est exécutée maintenant, l’application lève une exception `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="a7fc8-283">Cette erreur est due au fait que la classe du modèle Movie mise à jour est différente du schéma de la table Movie de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="a7fc8-284">Il n’y a aucune `Rating` colonne dans la table de base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="a7fc8-285">Plusieurs approches sont possibles pour résoudre l’erreur :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="a7fc8-286">Laisser Entity Framework supprimer et recréer automatiquement la base de données avec le nouveau schéma de classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="a7fc8-287">Cette approche est pratique au début du cycle de développement, ce qui vous permet de faire évoluer rapidement le schéma de base de données et le modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="a7fc8-288">L’inconvénient est que vous perdez les données existantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="a7fc8-289">N’utilisez pas cette approche sur une base de données de production !</span><span class="sxs-lookup"><span data-stu-id="a7fc8-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="a7fc8-290">La suppression de la base de données sur les modifications de schéma et l’utilisation d’un initialiseur pour amorcer automatiquement la base de données avec des données de test est souvent un moyen productif de développer une application.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="a7fc8-291">Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="a7fc8-292">L’avantage de cette approche est de conserver les données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="a7fc8-293">Effectuez cette modification manuellement ou en créant un script de modification de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="a7fc8-294">Utilisez les migrations Code First pour mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="a7fc8-295">Pour ce didacticiel, nous allons utiliser les migrations Code First.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="a7fc8-296">Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="a7fc8-297">Un exemple de modification est présenté ci-dessous, mais apporte cette modification pour chaque `new Movie` bloc.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="a7fc8-298">Consultez le [fichier SeedData.cs complet](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="a7fc8-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="a7fc8-299">Générez la solution.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a7fc8-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a7fc8-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="a7fc8-301">Ajouter une migration pour le champ d’évaluation</span><span class="sxs-lookup"><span data-stu-id="a7fc8-301">Add a migration for the rating field</span></span>

<span data-ttu-id="a7fc8-302">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="a7fc8-303">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="a7fc8-304">La commande `Add-Migration` indique au framework qu’il doit :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="a7fc8-305">Comparez le `Movie` modèle au `Movie` schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="a7fc8-306">:::no-loc(Create)::: code pour migrer le schéma de base de données vers le nouveau modèle.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-306">:::no-loc(Create)::: code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="a7fc8-307">Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="a7fc8-308">Il est utile d’utiliser un nom explicite pour le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="a7fc8-309">La commande `Update-Database` demande à l’infrastructure d’appliquer les modifications de schéma à la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="a7fc8-310">Si vous supprimez tous les enregistrements dans DdatabaseB, l’initialiseur amorce le DdatabaseB et inclut le `Rating` champ.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-310">If you delete all the records in the DdatabaseB, the initializer will seed the DdatabaseB and include the `Rating` field.</span></span> <span data-ttu-id="a7fc8-311">Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox).</span><span class="sxs-lookup"><span data-stu-id="a7fc8-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="a7fc8-312">Une autre option consiste à supprimer la base de données et à utiliser des migrations pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="a7fc8-313">Pour supprimer la base de données dans SSOX :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="a7fc8-314">Sélectionnez la base de données dans SSOX.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="a7fc8-315">Cliquez avec le bouton droit sur la base de données, puis sélectionnez **:::no-loc(Delete):::** .</span><span class="sxs-lookup"><span data-stu-id="a7fc8-315">Right-click on the database, and select **:::no-loc(Delete):::**.</span></span>
* <span data-ttu-id="a7fc8-316">Cochez **Fermer les connexions existantes**.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="a7fc8-317">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-317">Select **OK**.</span></span>
* <span data-ttu-id="a7fc8-318">Dans le [PMC](xref:tutorials/razor-pages/new-field#pmc), mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a7fc8-319">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a7fc8-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="a7fc8-320">Supprimer et recréer la base de données</span><span class="sxs-lookup"><span data-stu-id="a7fc8-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="a7fc8-321">Pour ce didacticiel, vous utilisez la fonctionnalité de *migrations* Entity Framework Core dans la mesure du possible.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="a7fc8-322">Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="a7fc8-323">Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="a7fc8-324">Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="a7fc8-325">Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="a7fc8-326">En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="a7fc8-327">À la place, vous supprimez puis recréez la base de données quand le schéma change.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="a7fc8-328">Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="a7fc8-329">La regénération d’un tableau implique :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="a7fc8-330">La création d’un nouveau tableau.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-330">Creating a new table.</span></span>
>* <span data-ttu-id="a7fc8-331">La copie de données de l’ancien tableau vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="a7fc8-332">La suppression de l’ancien tableau.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-332">Dropping the old table.</span></span>
>* <span data-ttu-id="a7fc8-333">Renommer la nouvelle table.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-333">Renaming the new table.</span></span>
>
><span data-ttu-id="a7fc8-334">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="a7fc8-335">Limites d’un fournisseur de base de données EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="a7fc8-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="a7fc8-336">Personnaliser le code de migration</span><span class="sxs-lookup"><span data-stu-id="a7fc8-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="a7fc8-337">Amorçage des données</span><span class="sxs-lookup"><span data-stu-id="a7fc8-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="a7fc8-338">Instruction SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="a7fc8-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="a7fc8-339">:::no-loc(Delete)::: la base de données et utilisent des migrations pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-339">:::no-loc(Delete)::: the database and use migrations to re-create the database.</span></span> <span data-ttu-id="a7fc8-340">Pour supprimer la base de données, supprimez le fichier de base de données ( *MvcMovie.db* ).</span><span class="sxs-lookup"><span data-stu-id="a7fc8-340">To delete the database, delete the database file ( *MvcMovie.db* ).</span></span> <span data-ttu-id="a7fc8-341">Ensuite, exécutez la commande `ef database update` :</span><span class="sxs-lookup"><span data-stu-id="a7fc8-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="a7fc8-342">Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="a7fc8-343">Si la base de données n’est pas amorcée, définissez un point d’arrêt dans la méthode `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="a7fc8-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7fc8-344">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a7fc8-344">Additional resources</span></span>

* [<span data-ttu-id="a7fc8-345">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="a7fc8-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="a7fc8-346">[Précédent : ajouter une recherche](xref:tutorials/razor-pages/search) 
>  [Suivant : ajouter une validation](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="a7fc8-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
