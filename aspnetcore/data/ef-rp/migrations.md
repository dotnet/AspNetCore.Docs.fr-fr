---
title: 'Partie 4 : :::no-loc(Razor)::: pages avec EF Core dans ASP.net Core migrations'
author: rick-anderson
description: 'Partie 4 des :::no-loc(Razor)::: pages et Entity Framework de la série de didacticiels.'
ms.author: riande
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
uid: data/ef-rp/migrations
ms.openlocfilehash: e6d1b9f041e892aaa37840c28fdb3153bf098b0d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061104"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a><span data-ttu-id="2472d-103">Partie 4 : :::no-loc(Razor)::: pages avec EF Core migrations dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="2472d-103">Part 4, :::no-loc(Razor)::: Pages with EF Core migrations in ASP.NET Core</span></span>

<span data-ttu-id="2472d-104">Par [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2472d-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2472d-105">Ce tutoriel présente la fonctionnalité de migrations EF Core pour gérer les modifications du modèle de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="2472d-106">Quand une nouvelle application est développée, le modèle de données change fréquemment.</span><span class="sxs-lookup"><span data-stu-id="2472d-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="2472d-107">Chaque fois que le modèle change, il est désynchronisé avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="2472d-108">Cette série de tutoriels a commencé par la configuration d’Entity Framework pour créer la base de données si elle n’existait pas.</span><span class="sxs-lookup"><span data-stu-id="2472d-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="2472d-109">Chaque fois que le modèle de données change, vous devez supprimer la base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="2472d-110">À l’exécution suivante de l’application, l’appel à `EnsureCreated` a pour effet de recréer la base de données en fonction du nouveau modèle de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="2472d-111">La classe `DbInitializer` s’exécute ensuite pour amorcer la nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="2472d-112">Cette approche consistant à maintenir la base de données synchronisée avec le modèle de données fonctionne bien tant que vous ne déployez pas l’application en production.</span><span class="sxs-lookup"><span data-stu-id="2472d-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="2472d-113">Quand l’application s’exécute en production, elle stocke généralement des données qui doivent être tenues à jour.</span><span class="sxs-lookup"><span data-stu-id="2472d-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="2472d-114">L’application ne peut pas démarrer avec une base de données de test chaque fois qu’une modification est apportée (comme l’ajout d’une nouvelle colonne).</span><span class="sxs-lookup"><span data-stu-id="2472d-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="2472d-115">La fonctionnalité Migrations d’EF Core résout ce problème en permettant à EF Core de mettre à jour le schéma de base de données au lieu de créer une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="2472d-116">Au lieu de supprimer et de recréer la base de données quand le modèle de données change, les migrations mettent à jour le schéma et conservent les données existantes.</span><span class="sxs-lookup"><span data-stu-id="2472d-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="2472d-117">Supprimer la base de données</span><span class="sxs-lookup"><span data-stu-id="2472d-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2472d-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2472d-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2472d-119">Utilisez l’ **Explorateur d’objets SQL Server** (SSOX) pour supprimer la base de données ou exécutez la commande suivante dans la **console du Gestionnaire de package** (PMC) :</span><span class="sxs-lookup"><span data-stu-id="2472d-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2472d-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2472d-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2472d-121">Exécutez la commande suivante à l’invite de commandes pour installer l’interface de ligne de commande EF :</span><span class="sxs-lookup"><span data-stu-id="2472d-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="2472d-122">Dans l’invite de commandes, accédez au dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="2472d-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="2472d-123">Le dossier du projet contient le fichier *ContosoUniversity.csproj* .</span><span class="sxs-lookup"><span data-stu-id="2472d-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="2472d-124">Supprimez le fichier *CU.db* ou exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="2472d-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="2472d-125">Créer une migration initiale</span><span class="sxs-lookup"><span data-stu-id="2472d-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2472d-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2472d-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2472d-127">Exécutez les commandes suivantes dans PMC :</span><span class="sxs-lookup"><span data-stu-id="2472d-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2472d-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2472d-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2472d-129">Vérifiez que l’invite de commandes se trouve dans le dossier du projet et exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="2472d-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="2472d-130">Méthodes Up et Down</span><span class="sxs-lookup"><span data-stu-id="2472d-130">Up and Down methods</span></span>

<span data-ttu-id="2472d-131">La commande EF Core `migrations add` a généré du code pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="2472d-132">Ce code de migration se trouve dans le fichier *migrations \<timestamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="2472d-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="2472d-133">La méthode `Up` de la classe `InitialCreate` crée les tables de base de données qui correspondent aux jeux d’entités du modèle de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="2472d-134">La méthode `Down` les supprime, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="2472d-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="2472d-135">Le code précédent concerne la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="2472d-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="2472d-136">Le code :</span><span class="sxs-lookup"><span data-stu-id="2472d-136">The code:</span></span>

* <span data-ttu-id="2472d-137">A été généré par la commande `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="2472d-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="2472d-138">Est exécuté par la commande `database update`.</span><span class="sxs-lookup"><span data-stu-id="2472d-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="2472d-139">Crée une base de données pour le modèle de données spécifié par la classe du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="2472d-140">Le paramètre de nom de migration (« InitialCreate » dans l’exemple) est utilisé comme nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="2472d-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="2472d-141">Le nom de la migration peut être n’importe quel nom de fichier valide.</span><span class="sxs-lookup"><span data-stu-id="2472d-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="2472d-142">Nous vous conseillons néanmoins de choisir un mot ou une expression qui résume ce qui est effectué dans la migration.</span><span class="sxs-lookup"><span data-stu-id="2472d-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="2472d-143">Par exemple, une migration ajoutant une table de département pourrait se nommer « TableAjoutDépartement ».</span><span class="sxs-lookup"><span data-stu-id="2472d-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="2472d-144">Table d’historique des migrations</span><span class="sxs-lookup"><span data-stu-id="2472d-144">The migrations history table</span></span>

* <span data-ttu-id="2472d-145">Utilisez SSOX ou votre outil SQLite pour inspecter la base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="2472d-146">Notez l’ajout d’une table `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="2472d-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="2472d-147">La table `__EFMigrationsHistory` effectue le suivi des migrations qui ont été appliquées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="2472d-148">Examinez les données contenues dans la table `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="2472d-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="2472d-149">Elle présente une ligne pour la première migration.</span><span class="sxs-lookup"><span data-stu-id="2472d-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="2472d-150">Capture instantanée du modèle de données</span><span class="sxs-lookup"><span data-stu-id="2472d-150">The data model snapshot</span></span>

<span data-ttu-id="2472d-151">Les migrations créent une *capture instantanée* du modèle de données actif dans *Migrations/SchoolContextModelSnapshot.cs* .</span><span class="sxs-lookup"><span data-stu-id="2472d-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs* .</span></span> <span data-ttu-id="2472d-152">Quand vous ajoutez une migration, EF détermine ce qui a changé en comparant le modèle de données actif au fichier de capture instantanée.</span><span class="sxs-lookup"><span data-stu-id="2472d-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="2472d-153">Comme le fichier de capture instantané suit l’état du modèle de données, vous ne pouvez pas supprimer une migration en supprimant le fichier `<timestamp>_<migrationname>.cs`.</span><span class="sxs-lookup"><span data-stu-id="2472d-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="2472d-154">Pour annuler la migration la plus récente, vous devez utiliser la commande `migrations remove`.</span><span class="sxs-lookup"><span data-stu-id="2472d-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="2472d-155">Cette commande supprime la migration et vérifie que la capture instantanée est correctement réinitialisée.</span><span class="sxs-lookup"><span data-stu-id="2472d-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="2472d-156">Pour plus d’informations, voir [migrations dotnet EF supprimer](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="2472d-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="2472d-157">Supprimer EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="2472d-157">Remove EnsureCreated</span></span>

<span data-ttu-id="2472d-158">Cette série de tutoriels a commencé en utilisant `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="2472d-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="2472d-159">La méthode `EnsureCreated` ne crée pas de table d’historique des migrations et ne peut donc pas être utilisée avec les migrations.</span><span class="sxs-lookup"><span data-stu-id="2472d-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="2472d-160">Elle est destinée à effectuer des tests et un prototypage rapide, où la base de données est supprimée et recréée fréquemment.</span><span class="sxs-lookup"><span data-stu-id="2472d-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="2472d-161">À partir de là, les tutoriels utilisent des migrations.</span><span class="sxs-lookup"><span data-stu-id="2472d-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="2472d-162">Dans *Data/DBInitializer. cs* , commentez la ligne suivante :</span><span class="sxs-lookup"><span data-stu-id="2472d-162">In *Data/DBInitializer.cs* , comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="2472d-163">Exécutez l’application et vérifiez que la base de données est amorcée.</span><span class="sxs-lookup"><span data-stu-id="2472d-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="2472d-164">Application de migrations en production</span><span class="sxs-lookup"><span data-stu-id="2472d-164">Applying migrations in production</span></span>

<span data-ttu-id="2472d-165">Nous **déconseillons** l’appel de [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) dans les applications de production pendant leur démarrage.</span><span class="sxs-lookup"><span data-stu-id="2472d-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="2472d-166">`Migrate` ne doit pas être appelé à partir d’une application déployée sur une batterie de serveurs.</span><span class="sxs-lookup"><span data-stu-id="2472d-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="2472d-167">Si un scale-out de plusieurs instances de serveur a lieu sur l’application, il est difficile de vérifier que les mises à jour du schéma de base de données ne se produisent pas à partir de plusieurs serveurs ou qu’elles ne sont pas en conflit avec un accès en lecture/écriture.</span><span class="sxs-lookup"><span data-stu-id="2472d-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="2472d-168">La migration de base de données doit être effectuée dans le cadre du déploiement et de manière contrôlée.</span><span class="sxs-lookup"><span data-stu-id="2472d-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="2472d-169">Parmi les approches de migration de base de données de production, citons :</span><span class="sxs-lookup"><span data-stu-id="2472d-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="2472d-170">L’utilisation de migrations pour créer des scripts SQL et l’utilisation de scripts SQL dans le déploiement</span><span class="sxs-lookup"><span data-stu-id="2472d-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="2472d-171">L’exécution de `dotnet ef database update` à partir d’un environnement contrôlé</span><span class="sxs-lookup"><span data-stu-id="2472d-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="2472d-172">Dépannage</span><span class="sxs-lookup"><span data-stu-id="2472d-172">Troubleshooting</span></span>

<span data-ttu-id="2472d-173">Si l’application utilise la Base de données locale SQL Server et affiche l’exception suivante :</span><span class="sxs-lookup"><span data-stu-id="2472d-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="2472d-174">La solution peut consister à exécuter `dotnet ef database update` à partir d’une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="2472d-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="2472d-175">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2472d-175">Additional resources</span></span>

* <span data-ttu-id="2472d-176">[CLI EF Core](/ef/core/miscellaneous/cli/dotnet)</span><span class="sxs-lookup"><span data-stu-id="2472d-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="2472d-177">Console du Gestionnaire de package (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="2472d-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="2472d-178">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="2472d-178">Next steps</span></span>

<span data-ttu-id="2472d-179">Le tutoriel suivant crée le modèle de données en ajoutant des propriétés d’entité et de nouvelles entités.</span><span class="sxs-lookup"><span data-stu-id="2472d-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2472d-180">[Didacticiel précédent](xref:data/ef-rp/sort-filter-page) 
>  [Didacticiel suivant](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="2472d-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2472d-181">Dans ce didacticiel, nous allons utiliser la fonctionnalité de migrations EF Core pour gérer les modifications du modèle de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="2472d-182">Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez [l’application terminée](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="2472d-182">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="2472d-183">Quand une nouvelle application est développée, le modèle de données change fréquemment.</span><span class="sxs-lookup"><span data-stu-id="2472d-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="2472d-184">Chaque fois que le modèle change, il est désynchronisé avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="2472d-185">Ce didacticiel commence par configurer Entity Framework pour créer la base de données si elle n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="2472d-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="2472d-186">Chaque fois que le modèle de données change :</span><span class="sxs-lookup"><span data-stu-id="2472d-186">Each time the data model changes:</span></span>

* <span data-ttu-id="2472d-187">La base de données est supprimée</span><span class="sxs-lookup"><span data-stu-id="2472d-187">The DB is dropped.</span></span>
* <span data-ttu-id="2472d-188">EF crée une nouvelle base de données qui correspond au modèle</span><span class="sxs-lookup"><span data-stu-id="2472d-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="2472d-189">L’application amorce la base de données avec des données de test</span><span class="sxs-lookup"><span data-stu-id="2472d-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="2472d-190">Cette approche pour conserver la synchronisation de la base de données avec le modèle de données fonctionne bien jusqu’à ce que vous déployiez l’application en production.</span><span class="sxs-lookup"><span data-stu-id="2472d-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="2472d-191">Quand l’application s’exécute en production, elle stocke généralement des données qui doivent être tenues à jour.</span><span class="sxs-lookup"><span data-stu-id="2472d-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="2472d-192">L’application ne peut pas commencer avec une base de données de test chaque fois qu’une modification est apportée (par exemple en cas d’ajout d’une nouvelle colonne).</span><span class="sxs-lookup"><span data-stu-id="2472d-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="2472d-193">La fonctionnalité Migrations d’EF Core résout ce problème en permettant à EF Core de mettre à jour le schéma de base de données au lieu de créer une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="2472d-194">Plutôt que de supprimer et de recréer la base de données quand le modèle de données change, les migrations mettent à jour le schéma et conservent les données existantes.</span><span class="sxs-lookup"><span data-stu-id="2472d-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="2472d-195">Supprimer la base de données</span><span class="sxs-lookup"><span data-stu-id="2472d-195">Drop the database</span></span>

<span data-ttu-id="2472d-196">Utilisez **l’Explorateur d’objets SQL Server** (SSOX) ou la commande `database drop` :</span><span class="sxs-lookup"><span data-stu-id="2472d-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2472d-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2472d-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2472d-198">Dans la **console du Gestionnaire de package** , exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="2472d-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="2472d-199">Exécutez `Get-Help about_EntityFrameworkCore` à partir de la console du Gestionnaire de package pour obtenir des informations d’aide.</span><span class="sxs-lookup"><span data-stu-id="2472d-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2472d-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2472d-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2472d-201">Ouvrez une fenêtre de commande et accédez au dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="2472d-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="2472d-202">Le dossier du projet contient le fichier *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="2472d-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="2472d-203">Entrez ce qui suit dans la fenêtre de commande :</span><span class="sxs-lookup"><span data-stu-id="2472d-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="2472d-204">Créer une migration initiale et mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="2472d-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="2472d-205">Générez le projet et créez la première migration.</span><span class="sxs-lookup"><span data-stu-id="2472d-205">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2472d-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2472d-206">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2472d-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2472d-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="2472d-208">Examiner les méthodes Up et Down</span><span class="sxs-lookup"><span data-stu-id="2472d-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="2472d-209">La commande EF Core `migrations add` a généré du code pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="2472d-210">Ce code de migration se trouve dans le fichier *migrations \<timestamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="2472d-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="2472d-211">La méthode `Up` de la classe `InitialCreate` crée les tables de base de données qui correspondent aux jeux d’entités du modèle de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="2472d-212">La méthode `Down` les supprime, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="2472d-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="2472d-213">La fonctionnalité Migrations appelle la méthode `Up` pour implémenter les modifications du modèle de données pour une migration.</span><span class="sxs-lookup"><span data-stu-id="2472d-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="2472d-214">Quand vous entrez une commande pour restaurer la mise à jour, les migrations appellent la méthode `Down`.</span><span class="sxs-lookup"><span data-stu-id="2472d-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="2472d-215">Le code précédent concerne la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="2472d-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="2472d-216">Ce code a été créé quand la commande `migrations add InitialCreate` a été exécutée.</span><span class="sxs-lookup"><span data-stu-id="2472d-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="2472d-217">Le paramètre de nom de migration (« InitialCreate » dans l’exemple) est utilisé comme nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="2472d-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="2472d-218">Le nom de la migration peut être n’importe quel nom de fichier valide.</span><span class="sxs-lookup"><span data-stu-id="2472d-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="2472d-219">Nous vous conseillons néanmoins de choisir un mot ou une expression qui résume ce qui est effectué dans la migration.</span><span class="sxs-lookup"><span data-stu-id="2472d-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="2472d-220">Par exemple, une migration ajoutant une table de département pourrait se nommer « TableAjoutDépartement ».</span><span class="sxs-lookup"><span data-stu-id="2472d-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="2472d-221">Si la migration initiale est créée et que la base de données existe :</span><span class="sxs-lookup"><span data-stu-id="2472d-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="2472d-222">Le code de création de base de données est généré</span><span class="sxs-lookup"><span data-stu-id="2472d-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="2472d-223">Le code de création de base de données n’a pas besoin de s’exécuter, car la base de données correspond déjà au modèle de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="2472d-224">Si le code de création de base de données est exécuté, il n’apporte aucune modification, car la base de données correspond déjà au modèle de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="2472d-225">Quand l’application est déployée sur un nouvel environnement, vous devez exécuter le code de création de base de données pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="2472d-226">Comme la base de données a été supprimée et n’existe pas, les migrations créent une autre base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="2472d-227">Capture instantanée du modèle de données</span><span class="sxs-lookup"><span data-stu-id="2472d-227">The data model snapshot</span></span>

<span data-ttu-id="2472d-228">Les migrations créent un *instantané* du schéma de base de données actuel dans *Migrations/SchoolContextModelSnapshot.cs* .</span><span class="sxs-lookup"><span data-stu-id="2472d-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs* .</span></span> <span data-ttu-id="2472d-229">Quand vous ajoutez une migration, EF détermine ce qui a changé en comparant le modèle de données au fichier de capture instantanée.</span><span class="sxs-lookup"><span data-stu-id="2472d-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="2472d-230">Pour supprimer une migration, utilisez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="2472d-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2472d-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2472d-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2472d-232">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="2472d-232">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2472d-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2472d-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="2472d-234">Pour plus d’informations, voir [migrations dotnet EF supprimer](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="2472d-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="2472d-235">Pour supprimer les migrations, la commande supprime la migration et garantit que l’instantané est correctement réinitialisé.</span><span class="sxs-lookup"><span data-stu-id="2472d-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="2472d-236">Supprimer EnsureCreated et tester l’application</span><span class="sxs-lookup"><span data-stu-id="2472d-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="2472d-237">Dans les phases initiales de développement, nous avons utilisé `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="2472d-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="2472d-238">Dans ce tutoriel, nous utilisons des migrations.</span><span class="sxs-lookup"><span data-stu-id="2472d-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="2472d-239">La commande `EnsureCreated` a les limitations suivantes :</span><span class="sxs-lookup"><span data-stu-id="2472d-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="2472d-240">Elle ignore les migrations et crée la base de données et le schéma</span><span class="sxs-lookup"><span data-stu-id="2472d-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="2472d-241">Elle ne crée pas de table de migrations</span><span class="sxs-lookup"><span data-stu-id="2472d-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="2472d-242">Elle ne peut *pas* être utilisée avec des migrations</span><span class="sxs-lookup"><span data-stu-id="2472d-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="2472d-243">Elle est conçue pour effectuer des tests et un prototypage rapide, où la base de données est supprimée et recréée fréquemment.</span><span class="sxs-lookup"><span data-stu-id="2472d-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="2472d-244">Supprimez `EnsureCreated` :</span><span class="sxs-lookup"><span data-stu-id="2472d-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="2472d-245">Exécutez l’application et vérifiez que la base de données est amorcée.</span><span class="sxs-lookup"><span data-stu-id="2472d-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="2472d-246">Inspecter la base de données</span><span class="sxs-lookup"><span data-stu-id="2472d-246">Inspect the database</span></span>

<span data-ttu-id="2472d-247">Utilisez **l’Explorateur d’objets SQL Server** pour inspecter la base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="2472d-248">Notez l’ajout d’une table `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="2472d-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="2472d-249">La table `__EFMigrationsHistory` effectue le suivi des migrations qui ont été appliquées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="2472d-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="2472d-250">Visualisez les données dans la table `__EFMigrationsHistory` ; elle affiche une ligne pour la première migration.</span><span class="sxs-lookup"><span data-stu-id="2472d-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="2472d-251">Le dernier journal dans l’exemple de sortie CLI précédent montre l’instruction INSERT qui crée cette ligne.</span><span class="sxs-lookup"><span data-stu-id="2472d-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="2472d-252">Exécutez l’application et vérifiez que tout fonctionne.</span><span class="sxs-lookup"><span data-stu-id="2472d-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="2472d-253">Application de migrations en production</span><span class="sxs-lookup"><span data-stu-id="2472d-253">Applying migrations in production</span></span>

<span data-ttu-id="2472d-254">Nous vous recommandons de faire en sorte que les applications de production n’appellent **pas**[Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="2472d-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="2472d-255">`Migrate` ne doit pas être appelée à partir d’une application dans la batterie de serveurs,</span><span class="sxs-lookup"><span data-stu-id="2472d-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="2472d-256">par exemple si l’application a été déployée dans le cloud avec montée en puissance parallèle (plusieurs instances de l’application sont en cours d’exécution).</span><span class="sxs-lookup"><span data-stu-id="2472d-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="2472d-257">La migration de base de données doit être effectuée dans le cadre du déploiement et de manière contrôlée.</span><span class="sxs-lookup"><span data-stu-id="2472d-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="2472d-258">Parmi les approches de migration de base de données de production, citons :</span><span class="sxs-lookup"><span data-stu-id="2472d-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="2472d-259">L’utilisation de migrations pour créer des scripts SQL et l’utilisation de scripts SQL dans le déploiement</span><span class="sxs-lookup"><span data-stu-id="2472d-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="2472d-260">L’exécution de `dotnet ef database update` à partir d’un environnement contrôlé</span><span class="sxs-lookup"><span data-stu-id="2472d-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="2472d-261">EF Core utilise la table `__MigrationsHistory` pour voir si des migrations doivent s’exécuter.</span><span class="sxs-lookup"><span data-stu-id="2472d-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="2472d-262">Si la base de données est à jour, aucune migration n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="2472d-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="2472d-263">Dépannage</span><span class="sxs-lookup"><span data-stu-id="2472d-263">Troubleshooting</span></span>

<span data-ttu-id="2472d-264">Téléchargez [l’application terminée](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span><span class="sxs-lookup"><span data-stu-id="2472d-264">Download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="2472d-265">L’application génère l’exception suivante :</span><span class="sxs-lookup"><span data-stu-id="2472d-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="2472d-266">Solution : Exécutez `dotnet ef database update`.</span><span class="sxs-lookup"><span data-stu-id="2472d-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="2472d-267">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2472d-267">Additional resources</span></span>

* [<span data-ttu-id="2472d-268">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="2472d-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="2472d-269">[CLI .net Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="2472d-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="2472d-270">Console du Gestionnaire de package (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="2472d-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="2472d-271">[Précédent](xref:data/ef-rp/sort-filter-page) 
>  [Suivant](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="2472d-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

