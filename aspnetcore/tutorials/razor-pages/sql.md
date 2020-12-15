---
title: 'Partie 4 : travailler avec une base de données'
author: rick-anderson
description: Partie 4 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.date: 09/26/2020
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 552a74016c281af248ce735c6ed6a5a55768a16a
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486243"
---
# <a name="part-4-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="b8d9e-103">Partie 4 de la série de didacticiels sur les Razor pages</span><span class="sxs-lookup"><span data-stu-id="b8d9e-103">Part 4 of tutorial series on Razor Pages</span></span>

<span data-ttu-id="b8d9e-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="b8d9e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b8d9e-105">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b8d9e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b8d9e-106">L’objet `RazorPagesMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-106">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="b8d9e-107">Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8d9e-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8d9e-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b8d9e-109">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b8d9e-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="b8d9e-110">Le système de [Configuration](xref:fundamentals/configuration/index) ASP.net Core lit la `ConnectionString` clé.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="b8d9e-111">Pour le développement local, la configuration obtient la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-111">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8d9e-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8d9e-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b8d9e-113">La chaîne de connexion générée est semblable à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-113">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b8d9e-114">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b8d9e-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="b8d9e-115">Lorsque l’application est déployée sur un serveur de test ou de production, une variable d’environnement peut être utilisée pour définir la chaîne de connexion à un serveur de base de données de test ou de production.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="b8d9e-116">Pour plus d’informations, consultez [configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b8d9e-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8d9e-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8d9e-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="b8d9e-118">Base de données locale SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="b8d9e-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="b8d9e-119">LocalDB est une version allégée du moteur de base de données SQL Server Express, qui est ciblée pour le développement de programmes.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="b8d9e-120">LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="b8d9e-121">Par défaut, la base de données LocalDB crée des fichiers `*.mdf` dans le répertoire `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="b8d9e-122">Dans le menu **Affichage**, ouvrez **l’Explorateur d’objets SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="b8d9e-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![Menu Affichage](sql/_static/5/ssox.png)

1. <span data-ttu-id="b8d9e-124">Cliquez avec le bouton droit sur la `Movie` table et sélectionnez **Concepteur de vues**:</span><span class="sxs-lookup"><span data-stu-id="b8d9e-124">Right-click on the `Movie` table and select **View Designer**:</span></span>

   ![Les menus contextuels s’ouvrent dans la table Movie](sql/_static/5/design.png)

   ![Les tables Movie s’ouvrent dans le concepteur](sql/_static/dv.png)

   <span data-ttu-id="b8d9e-127">Notez l’icône de clé en regard de `ID`.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="b8d9e-128">Par défaut, EF crée une propriété nommée `ID` pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="b8d9e-129">Cliquez avec le bouton droit sur la `Movie` table et sélectionnez **afficher les données**:</span><span class="sxs-lookup"><span data-stu-id="b8d9e-129">Right-click on the `Movie` table and select **View Data**:</span></span>

   ![Table Movie ouverte, affichant des données de table](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b8d9e-131">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b8d9e-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="b8d9e-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="b8d9e-132">SQLite</span></span>

<span data-ttu-id="b8d9e-133">Le site web [SQLite](https://www.sqlite.org/) indique :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="b8d9e-134">SQLite est un moteur de base de données SQL autonome, embarqué, à haute fiabilité, aux fonctionnalités complètes et accessible dans le domaine public.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="b8d9e-135">SQLite est le moteur de base de données le plus utilisé au monde.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="b8d9e-136">Vous pouvez télécharger de nombreux outils tiers pour gérer et afficher une base de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="b8d9e-137">L’image ci-dessous provient de [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="b8d9e-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="b8d9e-138">Si vous avez un outil SQLite préféré, laissez un commentaire pour nous dire ce que vous aimez chez lui.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Explorateur de bases de données pour SQLite avec la base de données Movie](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="b8d9e-140">Pour ce didacticiel, la fonctionnalité de *migrations* Entity Framework Core est utilisée dans la mesure du possible.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="b8d9e-141">Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="b8d9e-142">Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="b8d9e-143">Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="b8d9e-144">Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="b8d9e-145">En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="b8d9e-146">Au lieu de cela, lorsque le schéma est modifié, la base de données est supprimée et recréée.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="b8d9e-147">Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="b8d9e-148">La regénération d’un tableau implique :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="b8d9e-149">La création d’un nouveau tableau.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-149">Creating a new table.</span></span>
>* <span data-ttu-id="b8d9e-150">La copie de données de l’ancien tableau vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="b8d9e-151">La suppression de l’ancien tableau.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-151">Dropping the old table.</span></span>
>* <span data-ttu-id="b8d9e-152">Renommer la nouvelle table.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-152">Renaming the new table.</span></span>
>
><span data-ttu-id="b8d9e-153">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="b8d9e-154">Limites d’un fournisseur de base de données EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="b8d9e-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="b8d9e-155">Personnaliser le code de migration</span><span class="sxs-lookup"><span data-stu-id="b8d9e-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="b8d9e-156">Amorçage des données</span><span class="sxs-lookup"><span data-stu-id="b8d9e-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="b8d9e-157">Instruction SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="b8d9e-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="b8d9e-158">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="b8d9e-158">Seed the database</span></span>

<span data-ttu-id="b8d9e-159">Créez une classe nommée `SeedData` dans le dossier *Modèles* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-159">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="b8d9e-160">Si la base de données contient des films, l’initialiseur de valeur initiale retourne et aucun film n’est ajouté.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="b8d9e-161">Ajouter l’initialiseur de valeur initiale</span><span class="sxs-lookup"><span data-stu-id="b8d9e-161">Add the seed initializer</span></span>

<span data-ttu-id="b8d9e-162">Remplacez le contenu du fichier *Program.cs* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

<span data-ttu-id="b8d9e-163">Dans le code précédent, la `Main` méthode a été modifiée pour effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="b8d9e-164">Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="b8d9e-165">Appelez la `seedData.Initialize` méthode, en lui transmettant l’instance de contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="b8d9e-166">Supprimer le contexte une fois la méthode seed terminée.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="b8d9e-167">L' [instruction using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantit que le contexte est supprimé.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-167">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="b8d9e-168">L’exception suivante se produit lorsque `Update-Database` n’a pas été exécuté :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="b8d9e-169">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="b8d9e-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8d9e-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8d9e-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b8d9e-171">Supprimez tous les enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-171">Delete all the records in the database.</span></span> <span data-ttu-id="b8d9e-172">Utilisez les liens supprimer dans le navigateur ou à partir de [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="b8d9e-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="b8d9e-173">Force l’initialisation de l’application en appelant les méthodes de la `Startup` classe, de sorte que la méthode Seed s’exécute.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="b8d9e-174">Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="b8d9e-175">Arrêtez et redémarrez IIS avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="b8d9e-176">Cliquez avec le bouton droit sur l’icône IIS Express de la barre d’état système dans la zone de notification et sélectionnez **quitter** ou **arrêter le site**:</span><span class="sxs-lookup"><span data-stu-id="b8d9e-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site**:</span></span>

      ![Icône de la barre d’état système IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Menu contextuel](sql/_static/stopIIS.png)

   1. <span data-ttu-id="b8d9e-179">Si l’application s’exécute en mode sans débogage, appuyez sur <kbd>F5</kbd> pour l’exécuter en mode débogage.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="b8d9e-180">Si l’application est en mode débogage, arrêtez le débogueur et appuyez sur <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b8d9e-181">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b8d9e-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b8d9e-182">Supprimez tous les enregistrements de la base de données, afin que la méthode Seed s’exécute.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-182">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="b8d9e-183">Arrêtez et démarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="b8d9e-184">L’application affiche les données de départ :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-184">The app shows the seeded data:</span></span>

![Application vidéo ouverte dans le navigateur pour afficher les données de film](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="b8d9e-186">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b8d9e-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b8d9e-187">[Précédent : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page) 
>  [suivant : mettre à jour les pages](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="b8d9e-187">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="b8d9e-188">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b8d9e-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b8d9e-189">L’objet `RazorPagesMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-189">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="b8d9e-190">Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8d9e-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8d9e-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b8d9e-192">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b8d9e-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="b8d9e-193">Le système de [Configuration](xref:fundamentals/configuration/index) ASP.net Core lit la `ConnectionString` clé.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="b8d9e-194">Pour le développement local, la configuration obtient la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-194">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8d9e-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8d9e-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b8d9e-196">La chaîne de connexion générée est semblable à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b8d9e-197">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b8d9e-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="b8d9e-198">Lorsque l’application est déployée sur un serveur de test ou de production, une variable d’environnement peut être utilisée pour définir la chaîne de connexion à un serveur de base de données de test ou de production.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="b8d9e-199">Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b8d9e-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8d9e-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8d9e-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="b8d9e-201">Base de données locale SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="b8d9e-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="b8d9e-202">LocalDB est une version allégée du moteur de base de données SQL Server Express, qui est ciblée pour le développement de programmes.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="b8d9e-203">LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="b8d9e-204">Par défaut, la base de données LocalDB crée des fichiers `*.mdf` dans le répertoire `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="b8d9e-205">Dans le menu **Affichage**, ouvrez **l’Explorateur d’objets SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="b8d9e-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Affichage](sql/_static/ssox.png)

* <span data-ttu-id="b8d9e-207">Cliquez avec le bouton droit sur la `Movie` table et sélectionnez **Concepteur de vues**:</span><span class="sxs-lookup"><span data-stu-id="b8d9e-207">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Les menus contextuels s’ouvrent dans la table Movie](sql/_static/design.png)

  ![Les tables Movie s’ouvrent dans le concepteur](sql/_static/dv.png)

<span data-ttu-id="b8d9e-210">Notez l’icône de clé en regard de `ID`.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="b8d9e-211">Par défaut, EF crée une propriété nommée `ID` pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="b8d9e-212">Cliquez avec le bouton droit sur la `Movie` table et sélectionnez **afficher les données**:</span><span class="sxs-lookup"><span data-stu-id="b8d9e-212">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Table Movie ouverte, affichant des données de table](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b8d9e-214">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b8d9e-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="b8d9e-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="b8d9e-215">SQLite</span></span>

<span data-ttu-id="b8d9e-216">Le site web [SQLite](https://www.sqlite.org/) indique :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="b8d9e-217">SQLite est un moteur de base de données SQL autonome, embarqué, à haute fiabilité, aux fonctionnalités complètes et accessible dans le domaine public.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="b8d9e-218">SQLite est le moteur de base de données le plus utilisé au monde.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="b8d9e-219">Vous pouvez télécharger de nombreux outils tiers pour gérer et afficher une base de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="b8d9e-220">L’image ci-dessous provient de [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="b8d9e-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="b8d9e-221">Si vous avez un outil SQLite préféré, laissez un commentaire pour nous dire ce que vous aimez chez lui.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Explorateur de bases de données pour SQLite avec la base de données Movie](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="b8d9e-223">Pour ce didacticiel, la fonctionnalité de *migrations* Entity Framework Core est utilisée dans la mesure du possible.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="b8d9e-224">Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="b8d9e-225">Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="b8d9e-226">Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="b8d9e-227">Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="b8d9e-228">En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="b8d9e-229">Au lieu de cela, lorsque le schéma est modifié, la base de données est supprimée et recréée.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="b8d9e-230">Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="b8d9e-231">La regénération d’un tableau implique :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="b8d9e-232">La création d’un nouveau tableau.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-232">Creating a new table.</span></span>
>* <span data-ttu-id="b8d9e-233">La copie de données de l’ancien tableau vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="b8d9e-234">La suppression de l’ancien tableau.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-234">Dropping the old table.</span></span>
>* <span data-ttu-id="b8d9e-235">Renommer la nouvelle table.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-235">Renaming the new table.</span></span>
>
><span data-ttu-id="b8d9e-236">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="b8d9e-237">Limites d’un fournisseur de base de données EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="b8d9e-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="b8d9e-238">Personnaliser le code de migration</span><span class="sxs-lookup"><span data-stu-id="b8d9e-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="b8d9e-239">Amorçage des données</span><span class="sxs-lookup"><span data-stu-id="b8d9e-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="b8d9e-240">Instruction SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="b8d9e-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="b8d9e-241">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="b8d9e-241">Seed the database</span></span>

<span data-ttu-id="b8d9e-242">Créez une classe nommée `SeedData` dans le dossier *Modèles* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-242">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="b8d9e-243">Si la base de données contient des films, l’initialiseur de valeur initiale retourne et aucun film n’est ajouté.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="b8d9e-244">Ajouter l’initialiseur de valeur initiale</span><span class="sxs-lookup"><span data-stu-id="b8d9e-244">Add the seed initializer</span></span>

<span data-ttu-id="b8d9e-245">Remplacez le contenu du fichier *Program.cs* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="b8d9e-246">Dans le code précédent, la `Main` méthode a été modifiée pour effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="b8d9e-247">Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="b8d9e-248">Appelez la `seedData.Initialize` méthode, en lui transmettant l’instance de contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="b8d9e-249">Supprimer le contexte une fois la méthode seed terminée.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="b8d9e-250">L' [instruction using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantit que le contexte est supprimé.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-250">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="b8d9e-251">L’exception suivante se produit lorsque `Update-Database` n’a pas été exécuté :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="b8d9e-252">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="b8d9e-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8d9e-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8d9e-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b8d9e-254">Supprimez tous les enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-254">Delete all the records in the database.</span></span> <span data-ttu-id="b8d9e-255">Utilisez les liens supprimer dans le navigateur ou à partir de [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="b8d9e-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="b8d9e-256">Force l’initialisation de l’application en appelant les méthodes de la `Startup` classe, de sorte que la méthode Seed s’exécute.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="b8d9e-257">Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="b8d9e-258">Arrêtez et redémarrez IIS avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="b8d9e-259">Cliquez avec le bouton droit sur l’icône de barre d’état système IIS Express dans la zone de notification, puis appuyez sur **Quitter** ou sur **Arrêter le site** :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Icône de la barre d’état système IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextuel](sql/_static/stopIIS.png)

    * <span data-ttu-id="b8d9e-262">Si l’application s’exécute en mode sans débogage, appuyez sur <kbd>F5</kbd> pour l’exécuter en mode débogage.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="b8d9e-263">Si l’application est en mode débogage, arrêtez le débogueur et appuyez sur <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b8d9e-264">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b8d9e-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b8d9e-265">Supprimez tous les enregistrements de la base de données, afin que la méthode Seed s’exécute.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-265">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="b8d9e-266">Arrêtez et démarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="b8d9e-267">L’application affiche les données de départ :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-267">The app shows the seeded data:</span></span>

![Application Movie ouverte dans Chrome, affichant les données relatives aux films](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="b8d9e-269">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b8d9e-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b8d9e-270">[Précédent : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page) 
>  [suivant : mettre à jour les pages](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="b8d9e-270">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b8d9e-271">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b8d9e-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b8d9e-272">L’objet `RazorPagesMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-272">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="b8d9e-273">Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8d9e-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8d9e-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b8d9e-275">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b8d9e-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="b8d9e-276">Pour plus d’informations sur les méthodes utilisées dans `ConfigureServices`, consultez :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="b8d9e-277">[Prise en charge du règlement général sur la protection des données (RGPD) de l’Union Européenne dans ASP.NET Core](xref:security/gdpr) pour `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="b8d9e-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="b8d9e-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="b8d9e-279">Le système de [Configuration](xref:fundamentals/configuration/index) ASP.net Core lit la `ConnectionString` clé.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="b8d9e-280">Pour le développement local, la configuration obtient la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-280">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8d9e-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8d9e-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b8d9e-282">La chaîne de connexion générée est semblable à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b8d9e-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b8d9e-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b8d9e-284">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b8d9e-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="b8d9e-285">Lorsque l’application est déployée sur un serveur de test ou de production, une variable d’environnement peut être utilisée pour définir la chaîne de connexion à un serveur de base de données de test ou de production.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="b8d9e-286">Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b8d9e-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8d9e-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8d9e-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="b8d9e-288">Base de données locale SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="b8d9e-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="b8d9e-289">LocalDB est une version allégée du moteur de base de données SQL Server Express, qui est ciblée pour le développement de programmes.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="b8d9e-290">LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="b8d9e-291">Par défaut, la base de données LocalDB crée des fichiers `*.mdf` dans le répertoire `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="b8d9e-292">Dans le menu **Affichage**, ouvrez **l’Explorateur d’objets SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="b8d9e-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Affichage](sql/_static/ssox.png)

* <span data-ttu-id="b8d9e-294">Cliquez avec le bouton droit sur la `Movie` table et sélectionnez **Concepteur de vues**:</span><span class="sxs-lookup"><span data-stu-id="b8d9e-294">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Menu contextuel ouvert sur la table Movie](sql/_static/design.png)

  ![Table Movie ouverte dans le Concepteur](sql/_static/dv.png)

<span data-ttu-id="b8d9e-297">Notez l’icône de clé en regard de `ID`.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="b8d9e-298">Par défaut, EF crée une propriété nommée `ID` pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="b8d9e-299">Cliquez avec le bouton droit sur la `Movie` table et sélectionnez **afficher les données**:</span><span class="sxs-lookup"><span data-stu-id="b8d9e-299">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Table Movie ouverte, affichant des données de table](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b8d9e-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b8d9e-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b8d9e-302">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b8d9e-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="b8d9e-303">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="b8d9e-303">Seed the database</span></span>

<span data-ttu-id="b8d9e-304">Créez une classe nommée `SeedData` dans le dossier *Modèles* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-304">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="b8d9e-305">Si la base de données contient des films, l’initialiseur de valeur initiale retourne et aucun film n’est ajouté.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="b8d9e-306">Ajouter l’initialiseur de valeur initiale</span><span class="sxs-lookup"><span data-stu-id="b8d9e-306">Add the seed initializer</span></span>

<span data-ttu-id="b8d9e-307">Remplacez le contenu du fichier *Program.cs* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="b8d9e-308">Dans le code précédent, la `Main` méthode a été modifiée pour effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="b8d9e-309">Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="b8d9e-310">Appelez la `seedData.Initialize` méthode, en lui transmettant l’instance de contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="b8d9e-311">Supprimer le contexte une fois la méthode seed terminée.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="b8d9e-312">L' [instruction using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantit que le contexte est supprimé.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-312">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="b8d9e-313">Une application de production n’appelle pas `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="b8d9e-314">Il est ajouté au code précédent afin d’éviter l’exception suivante quand `Update-Database` n’a pas été exécutée :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="b8d9e-315">SqlException : impossible d’ouvrir la base de données « Razor PagesMovieContext-21 » demandée par la connexion.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-315">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="b8d9e-316">La connexion a échoué.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-316">The login failed.</span></span>
<span data-ttu-id="b8d9e-317">Échec de la connexion de l’utilisateur 'nom utilisateur'.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b8d9e-318">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="b8d9e-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8d9e-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8d9e-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b8d9e-320">Supprimez tous les enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-320">Delete all the records in the database.</span></span> <span data-ttu-id="b8d9e-321">Vous pouvez le faire avec les liens supprimer dans le navigateur ou à partir de [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="b8d9e-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="b8d9e-322">Force l’initialisation de l’application en appelant les méthodes de la `Startup` classe, de sorte que la méthode Seed s’exécute.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="b8d9e-323">Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="b8d9e-324">Pour cela, adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="b8d9e-325">Cliquez avec le bouton droit sur l’icône de barre d’état système IIS Express dans la zone de notification, puis appuyez sur **Quitter** ou sur **Arrêter le site** :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Icône de la barre d’état système IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextuel](sql/_static/stopIIS.png)

    * <span data-ttu-id="b8d9e-328">Si l’application s’exécute en mode sans débogage, appuyez sur <kbd>F5</kbd> pour l’exécuter en mode débogage.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="b8d9e-329">Si l’application est en mode débogage, arrêtez le débogueur et appuyez sur <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b8d9e-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b8d9e-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b8d9e-331">Supprimez tous les enregistrements de la base de données, afin que la méthode Seed s’exécute.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-331">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="b8d9e-332">Arrêtez et démarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b8d9e-333">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b8d9e-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b8d9e-334">Supprimez tous les enregistrements de la base de données, afin que la méthode Seed s’exécute.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-334">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="b8d9e-335">Arrêtez et démarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="b8d9e-336">L’application affiche les données de départ :</span><span class="sxs-lookup"><span data-stu-id="b8d9e-336">The app shows the seeded data:</span></span>

![Application Movie ouverte dans Chrome, affichant les données relatives aux films](sql/_static/m55https.png)

<span data-ttu-id="b8d9e-338">Le didacticiel suivant nettoie la présentation des données.</span><span class="sxs-lookup"><span data-stu-id="b8d9e-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b8d9e-339">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b8d9e-339">Additional resources</span></span>

* [<span data-ttu-id="b8d9e-340">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="b8d9e-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="b8d9e-341">[Précédent : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page) 
>  [suivant : mettre à jour les pages](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="b8d9e-341">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
