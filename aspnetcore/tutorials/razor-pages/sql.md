---
title: Partie 4, avec une base de données et une ASP.NET Core
author: rick-anderson
description: 'Partie 4 de la série de didacticiels sur les :::no-loc(Razor)::: pages.'
ms.author: riande
ms.date: 7/22/2019
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: d592cf7d8a96a7e4ec2e53418843a186488951be
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058153"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a><span data-ttu-id="5b552-103">Partie 4, avec une base de données et une ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5b552-103">Part 4, with a database and ASP.NET Core</span></span>

<span data-ttu-id="5b552-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="5b552-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="5b552-105">L’objet `:::no-loc(Razor):::PagesMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="5b552-105">The `:::no-loc(Razor):::PagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="5b552-106">Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` de *Startup.cs*  :</span><span class="sxs-lookup"><span data-stu-id="5b552-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b552-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b552-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5b552-108">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="5b552-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="5b552-109">Le système de [configuration](xref:fundamentals/configuration/index) d’ASP.NET Core lit `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="5b552-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="5b552-110">Pour le développement local, elle obtient la chaîne de connexion à partir du *:::no-loc(appsettings.json):::* fichier.</span><span class="sxs-lookup"><span data-stu-id="5b552-110">For local development, it gets the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b552-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b552-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5b552-112">La valeur du nom de la base de données (`Database={Database name}`) est différent pour votre code généré.</span><span class="sxs-lookup"><span data-stu-id="5b552-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="5b552-113">La valeur du nom est arbitraire.</span><span class="sxs-lookup"><span data-stu-id="5b552-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5b552-114">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="5b552-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="5b552-115">Quand l’application est déployée sur un serveur de test ou de production, une variable d’environnement peut être utilisée pour définir la chaîne de connexion à un serveur de base de données réel.</span><span class="sxs-lookup"><span data-stu-id="5b552-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="5b552-116">Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="5b552-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b552-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b552-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="5b552-118">Base de données locale SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="5b552-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="5b552-119">LocalDB est une version allégée du moteur de base de données SQL Server Express, qui est ciblée pour le développement de programmes.</span><span class="sxs-lookup"><span data-stu-id="5b552-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="5b552-120">LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe.</span><span class="sxs-lookup"><span data-stu-id="5b552-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="5b552-121">Par défaut, la base de données LocalDB crée des fichiers `*.mdf` dans le répertoire `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="5b552-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="5b552-122">Dans le menu **Affichage** , ouvrez **l’Explorateur d’objets SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="5b552-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Affichage](sql/_static/ssox.png)

* <span data-ttu-id="5b552-124">Cliquez avec le bouton droit sur la table `Movie` et sélectionnez **Concepteur de vues** :</span><span class="sxs-lookup"><span data-stu-id="5b552-124">Right click on the `Movie` table and select **View Designer** :</span></span>

  ![Les menus contextuels s’ouvrent dans la table Movie](sql/_static/design.png)

  ![Les tables Movie s’ouvrent dans le concepteur](sql/_static/dv.png)

<span data-ttu-id="5b552-127">Notez l’icône de clé en regard de `ID`.</span><span class="sxs-lookup"><span data-stu-id="5b552-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="5b552-128">Par défaut, EF crée une propriété nommée `ID` pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="5b552-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="5b552-129">Cliquez avec le bouton droit sur la table `Movie` et sélectionnez **Afficher les données** :</span><span class="sxs-lookup"><span data-stu-id="5b552-129">Right click on the `Movie` table and select **View Data** :</span></span>

  ![Table Movie ouverte, affichant des données de table](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5b552-131">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="5b552-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="5b552-132">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="5b552-132">Seed the database</span></span>

<span data-ttu-id="5b552-133">Créez une classe nommée `SeedData` dans le dossier *Modèles* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="5b552-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="5b552-134">Si la base de données contient des films, l’initialiseur de valeur initiale retourne une valeur et aucun film n’est ajouté.</span><span class="sxs-lookup"><span data-stu-id="5b552-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="5b552-135">Ajouter l’initialiseur de valeur initiale</span><span class="sxs-lookup"><span data-stu-id="5b552-135">Add the seed initializer</span></span>

<span data-ttu-id="5b552-136">Dans *Program.cs* , modifiez la méthode `Main` pour effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="5b552-136">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="5b552-137">Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="5b552-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="5b552-138">Appeler la méthode de remplissage initial, en lui transmettant le contexte.</span><span class="sxs-lookup"><span data-stu-id="5b552-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="5b552-139">Supprimer le contexte une fois la méthode seed terminée.</span><span class="sxs-lookup"><span data-stu-id="5b552-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="5b552-140">Le code suivant montre le fichier *Program.cs* mis à jour.</span><span class="sxs-lookup"><span data-stu-id="5b552-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Program.cs)]

<span data-ttu-id="5b552-141">L’exception suivante se produit lorsque `Update-Database` n’a pas été exécuté :</span><span class="sxs-lookup"><span data-stu-id="5b552-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="5b552-142">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="5b552-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b552-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b552-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5b552-144">Supprimez tous les enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="5b552-144">Delete all the records in the DB.</span></span> <span data-ttu-id="5b552-145">Vous pouvez le faire avec les liens supprimer dans le navigateur ou à partir de [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="5b552-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="5b552-146">Forcez l’application à s’initialiser (appelez les méthodes de la classe `Startup`) pour que la méthode seed s’exécute.</span><span class="sxs-lookup"><span data-stu-id="5b552-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="5b552-147">Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré.</span><span class="sxs-lookup"><span data-stu-id="5b552-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="5b552-148">Pour cela, adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="5b552-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="5b552-149">Cliquez avec le bouton droit sur l’icône IIS Express de la barre d’état système dans la zone de notification, puis appuyez sur **quitter** ou sur **arrêter le site** :</span><span class="sxs-lookup"><span data-stu-id="5b552-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site** :</span></span>

    ![Icône de la barre d’état système IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextuel](sql/_static/stopIIS.png)

    * <span data-ttu-id="5b552-152">Si vous exécutiez Visual Studio en mode de non-débogage, appuyez sur F5 pour l’exécuter en mode de débogage.</span><span class="sxs-lookup"><span data-stu-id="5b552-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="5b552-153">Si vous exécutiez Visual Studio en mode de débogage, arrêtez le débogueur et appuyez sur F5.</span><span class="sxs-lookup"><span data-stu-id="5b552-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5b552-154">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="5b552-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="5b552-155">Supprimez tous les enregistrements dans la base de données (pour que la méthode seed s’exécute).</span><span class="sxs-lookup"><span data-stu-id="5b552-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="5b552-156">Arrêtez et démarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="5b552-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="5b552-157">L’application affiche les données de départ.</span><span class="sxs-lookup"><span data-stu-id="5b552-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="5b552-158">Le tutoriel suivant sert à améliorer la présentation des données.</span><span class="sxs-lookup"><span data-stu-id="5b552-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b552-159">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5b552-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5b552-160">[Précédent : génération de modèles :::no-loc(Razor)::: automatique Pages](xref:tutorials/razor-pages/page)suivantes 
>  [: mise à jour des pages](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="5b552-160">[Previous: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="5b552-161">L’objet `:::no-loc(Razor):::PagesMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="5b552-161">The `:::no-loc(Razor):::PagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="5b552-162">Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` de *Startup.cs*  :</span><span class="sxs-lookup"><span data-stu-id="5b552-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b552-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b552-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5b552-164">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="5b552-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="5b552-165">Pour plus d’informations sur les méthodes utilisées dans `ConfigureServices`, consultez :</span><span class="sxs-lookup"><span data-stu-id="5b552-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="5b552-166">[Prise en charge du règlement général sur la protection des données (RGPD) de l’Union Européenne dans ASP.NET Core](xref:security/gdpr) pour `:::no-loc(Cookie):::PolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="5b552-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `:::no-loc(Cookie):::PolicyOptions`.</span></span>
* [<span data-ttu-id="5b552-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="5b552-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="5b552-168">Le système de [configuration](xref:fundamentals/configuration/index) d’ASP.NET Core lit `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="5b552-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="5b552-169">Pour le développement local, elle obtient la chaîne de connexion à partir du *:::no-loc(appsettings.json):::* fichier.</span><span class="sxs-lookup"><span data-stu-id="5b552-169">For local development, it gets the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b552-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b552-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5b552-171">La valeur du nom de la base de données (`Database={Database name}`) est différent pour votre code généré.</span><span class="sxs-lookup"><span data-stu-id="5b552-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="5b552-172">La valeur du nom est arbitraire.</span><span class="sxs-lookup"><span data-stu-id="5b552-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/:::no-loc(appsettings.json):::)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b552-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b552-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b552-174">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="5b552-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="5b552-175">Quand l’application est déployée sur un serveur de test ou de production, une variable d’environnement peut être utilisée pour définir la chaîne de connexion à un serveur de base de données réel.</span><span class="sxs-lookup"><span data-stu-id="5b552-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="5b552-176">Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="5b552-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b552-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b552-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="5b552-178">Base de données locale SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="5b552-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="5b552-179">LocalDB est une version allégée du moteur de base de données SQL Server Express, qui est ciblée pour le développement de programmes.</span><span class="sxs-lookup"><span data-stu-id="5b552-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="5b552-180">LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe.</span><span class="sxs-lookup"><span data-stu-id="5b552-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="5b552-181">Par défaut, la base de données LocalDB crée des fichiers `*.mdf` dans le répertoire `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="5b552-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="5b552-182">Dans le menu **Affichage** , ouvrez **l’Explorateur d’objets SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="5b552-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Affichage](sql/_static/ssox.png)

* <span data-ttu-id="5b552-184">Cliquez avec le bouton droit sur la table `Movie` et sélectionnez **Concepteur de vues** :</span><span class="sxs-lookup"><span data-stu-id="5b552-184">Right click on the `Movie` table and select **View Designer** :</span></span>

  ![Menu contextuel ouvert sur la table Movie](sql/_static/design.png)

  ![Table Movie ouverte dans le Concepteur](sql/_static/dv.png)

<span data-ttu-id="5b552-187">Notez l’icône de clé en regard de `ID`.</span><span class="sxs-lookup"><span data-stu-id="5b552-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="5b552-188">Par défaut, EF crée une propriété nommée `ID` pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="5b552-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="5b552-189">Cliquez avec le bouton droit sur la table `Movie` et sélectionnez **Afficher les données** :</span><span class="sxs-lookup"><span data-stu-id="5b552-189">Right click on the `Movie` table and select **View Data** :</span></span>

  ![Table Movie ouverte, affichant des données de table](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b552-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b552-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b552-192">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="5b552-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="5b552-193">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="5b552-193">Seed the database</span></span>

<span data-ttu-id="5b552-194">Créez une classe nommée `SeedData` dans le dossier *Modèles* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="5b552-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="5b552-195">Si la base de données contient des films, l’initialiseur de valeur initiale retourne une valeur et aucun film n’est ajouté.</span><span class="sxs-lookup"><span data-stu-id="5b552-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="5b552-196">Ajouter l’initialiseur de valeur initiale</span><span class="sxs-lookup"><span data-stu-id="5b552-196">Add the seed initializer</span></span>

<span data-ttu-id="5b552-197">Dans *Program.cs* , modifiez la méthode `Main` pour effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="5b552-197">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="5b552-198">Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="5b552-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="5b552-199">Appeler la méthode de remplissage initial, en lui transmettant le contexte.</span><span class="sxs-lookup"><span data-stu-id="5b552-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="5b552-200">Supprimer le contexte une fois la méthode seed terminée.</span><span class="sxs-lookup"><span data-stu-id="5b552-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="5b552-201">Le code suivant montre le fichier *Program.cs* mis à jour.</span><span class="sxs-lookup"><span data-stu-id="5b552-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Program.cs)]

<span data-ttu-id="5b552-202">Une application de production n’appelle pas `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="5b552-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="5b552-203">Il est ajouté au code précédent afin d’éviter l’exception suivante quand `Update-Database` n’a pas été exécutée :</span><span class="sxs-lookup"><span data-stu-id="5b552-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="5b552-204">SqlException : impossible d’ouvrir la base de données « :::no-loc(Razor)::: PagesMovieContext-21 » demandée par la connexion.</span><span class="sxs-lookup"><span data-stu-id="5b552-204">SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="5b552-205">La connexion a échoué.</span><span class="sxs-lookup"><span data-stu-id="5b552-205">The login failed.</span></span>
<span data-ttu-id="5b552-206">Échec de la connexion de l’utilisateur 'nom utilisateur'.</span><span class="sxs-lookup"><span data-stu-id="5b552-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="5b552-207">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="5b552-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b552-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b552-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5b552-209">Supprimez tous les enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="5b552-209">Delete all the records in the DB.</span></span> <span data-ttu-id="5b552-210">Vous pouvez le faire avec les liens supprimer dans le navigateur ou à partir de [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="5b552-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="5b552-211">Forcez l’application à s’initialiser (appelez les méthodes de la classe `Startup`) pour que la méthode seed s’exécute.</span><span class="sxs-lookup"><span data-stu-id="5b552-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="5b552-212">Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré.</span><span class="sxs-lookup"><span data-stu-id="5b552-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="5b552-213">Pour cela, adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="5b552-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="5b552-214">Cliquez avec le bouton droit sur l’icône de barre d’état système IIS Express dans la zone de notification, puis appuyez sur **Quitter** ou sur **Arrêter le site** :</span><span class="sxs-lookup"><span data-stu-id="5b552-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site** :</span></span>

    ![Icône de la barre d’état système IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextuel](sql/_static/stopIIS.png)

    * <span data-ttu-id="5b552-217">Si vous exécutiez Visual Studio en mode de non-débogage, appuyez sur F5 pour l’exécuter en mode de débogage.</span><span class="sxs-lookup"><span data-stu-id="5b552-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="5b552-218">Si vous exécutiez Visual Studio en mode de débogage, arrêtez le débogueur et appuyez sur F5.</span><span class="sxs-lookup"><span data-stu-id="5b552-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b552-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b552-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5b552-220">Supprimez tous les enregistrements dans la base de données (pour que la méthode seed s’exécute).</span><span class="sxs-lookup"><span data-stu-id="5b552-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="5b552-221">Arrêtez et démarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="5b552-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="5b552-222">L’application affiche les données de départ.</span><span class="sxs-lookup"><span data-stu-id="5b552-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b552-223">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="5b552-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5b552-224">Supprimez tous les enregistrements dans la base de données (pour que la méthode seed s’exécute).</span><span class="sxs-lookup"><span data-stu-id="5b552-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="5b552-225">Arrêtez et démarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="5b552-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="5b552-226">L’application affiche les données de départ.</span><span class="sxs-lookup"><span data-stu-id="5b552-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="5b552-227">L’application affiche les données de départ :</span><span class="sxs-lookup"><span data-stu-id="5b552-227">The app shows the seeded data:</span></span>

![Application Movie ouverte dans Chrome, affichant les données relatives aux films](sql/_static/m55.png)

<span data-ttu-id="5b552-229">Le didacticiel suivant nettoie la présentation des données.</span><span class="sxs-lookup"><span data-stu-id="5b552-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b552-230">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5b552-230">Additional resources</span></span>

* [<span data-ttu-id="5b552-231">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="5b552-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="5b552-232">[Précédent : génération de modèles :::no-loc(Razor)::: automatique Pages](xref:tutorials/razor-pages/page)suivantes 
>  [: mise à jour des pages](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="5b552-232">[Previous: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
