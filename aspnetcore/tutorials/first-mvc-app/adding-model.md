---
title: 'Partie 4 : ajouter un modèle à une application ASP.NET Core MVC'
author: rick-anderson
description: Partie 4 de la série de didacticiels sur ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
no-loc:
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: bfda45afeea67a11ad775996d94a06125df08bc6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854585"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="93927-103">Partie 4 : ajouter un modèle à une application ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="93927-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="93927-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Ryan Nowak](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="93927-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="93927-105">Dans cette section, vous allez ajouter des classes pour la gestion des films dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="93927-106">Ces classes constituent la partie « **M** odèle » de l’application **M** VC.</span><span class="sxs-lookup"><span data-stu-id="93927-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="93927-107">Vous utilisez ces classes avec [Entity Framework Core](/ef/core) (EF Core) pour travailler avec une base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="93927-108">EF Core est un framework de mappage relationnel d’objets qui simplifie le code d’accès aux données à écrire.</span><span class="sxs-lookup"><span data-stu-id="93927-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="93927-109">Les classes de modèle que vous créez portent le nom de classes OCT (« **O** bjet **C** LR **T** raditionnel »)**,** car elles n’ont pas de dépendances envers EF Core.</span><span class="sxs-lookup"><span data-stu-id="93927-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="93927-110">Elles définissent simplement les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="93927-111">Dans ce didacticiel, vous écrivez d’abord les classes du modèle, puis EF Core crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="93927-112">Ajouter une classe de modèle de données</span><span class="sxs-lookup"><span data-stu-id="93927-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93927-114">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="93927-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="93927-115">Nommez le fichier *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="93927-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="93927-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93927-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="93927-117">Ajoutez un fichier nommé *Movie.cs* au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="93927-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="93927-118">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="93927-119">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** une classe  >    >  **vide** Class.</span><span class="sxs-lookup"><span data-stu-id="93927-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="93927-120">Nommez le fichier *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="93927-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="93927-121">Mettez le fichier *Movie.cs* à jour avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="93927-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="93927-122">La classe `Movie` contient un champ `Id`, qui est nécessaire à la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="93927-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="93927-123">L' <xref:System.ComponentModel.DataAnnotations.DataType> attribut sur `ReleaseDate` spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="93927-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="93927-124">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="93927-124">With this attribute:</span></span>

* <span data-ttu-id="93927-125">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="93927-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="93927-126">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="93927-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="93927-127">Les [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="93927-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="93927-128">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="93927-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93927-130">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="93927-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu Console du Gestionnaire de package](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="93927-132">Dans la console du gestionnaire de package, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="93927-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="93927-133">La commande précédente ajoute le fournisseur EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="93927-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="93927-134">Le package du fournisseur installe le package EF Core en tant que dépendance.</span><span class="sxs-lookup"><span data-stu-id="93927-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="93927-135">Plus loin dans ce tutoriel, d’autres packages sont installés automatiquement lors de l’étape de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="93927-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="93927-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93927-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="93927-137">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="93927-138">Dans le menu **projet** , sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="93927-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="93927-139">Dans le champ de **recherche** en haut à droite, entrez `Microsoft.EntityFrameworkCore.SQLite` et appuyez sur la touche **retour** pour effectuer la recherche.</span><span class="sxs-lookup"><span data-stu-id="93927-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="93927-140">Sélectionnez le package NuGet correspondant, puis cliquez sur le bouton **Ajouter un package** .</span><span class="sxs-lookup"><span data-stu-id="93927-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Ajouter Entity Framework Core package NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="93927-142">La boîte de dialogue **Sélectionner les projets** s’affiche, avec le `MvcMovie` projet sélectionné.</span><span class="sxs-lookup"><span data-stu-id="93927-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="93927-143">Appuyez sur le bouton **OK** .</span><span class="sxs-lookup"><span data-stu-id="93927-143">Press the **Ok** button.</span></span>

<span data-ttu-id="93927-144">Une boîte de dialogue d' **acceptation de licence** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="93927-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="93927-145">Passez en revue les licences comme vous le souhaitez, puis cliquez sur le bouton **accepter** .</span><span class="sxs-lookup"><span data-stu-id="93927-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="93927-146">Répétez les étapes ci-dessus pour installer les packages NuGet suivants :</span><span class="sxs-lookup"><span data-stu-id="93927-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="93927-147">Exécutez la commande CLI .NET suivante :</span><span class="sxs-lookup"><span data-stu-id="93927-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="93927-148">La commande précédente ajoute l' [outil de génération de modèles automatique ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="93927-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="93927-149">Créer une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="93927-149">Create a database context class</span></span>

<span data-ttu-id="93927-150">Une classe de contexte de base de données est nécessaire pour coordonner les fonctionnalités d’EF Core (créer, lire, mettre à jour, supprimer) pour le modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="93927-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="93927-151">Le contexte de base de données est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) et il spécifie les entités à inclure dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="93927-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="93927-152">Créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="93927-152">Create a *Data* folder.</span></span>

<span data-ttu-id="93927-153">Ajoutez un fichier *Data/MvcMovieContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="93927-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="93927-154">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="93927-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="93927-155">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="93927-156">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="93927-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="93927-157">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="93927-157">Register the database context</span></span>

<span data-ttu-id="93927-158">ASP.NET Core comprend [l’injection de dépendances (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="93927-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="93927-159">Les services (tels que le contexte de base de données EF Core) doivent être inscrits auprès de l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="93927-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="93927-160">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="93927-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="93927-161">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="93927-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="93927-162">Dans cette section, vous allez inscrire le contexte de base de données auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="93927-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="93927-163">En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="93927-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="93927-164">Ajoutez le code en surbrillance suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="93927-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-166">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="93927-167">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="93927-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="93927-168">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="93927-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="93927-169">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="93927-169">Add a database connection string</span></span>

<span data-ttu-id="93927-170">Ajoutez une chaîne de connexion au *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="93927-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-172">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="93927-173">Générez le projet en tant que vérification des erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="93927-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="93927-174">Générer automatiquement des pages de film</span><span class="sxs-lookup"><span data-stu-id="93927-174">Scaffold movie pages</span></span>

<span data-ttu-id="93927-175">Utilisez l’outil de génération de modèles automatique pour créer, lire, mettre à jour et supprimer des pages du modèle de film.</span><span class="sxs-lookup"><span data-stu-id="93927-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93927-177">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Contrôleurs*, puis choisissez **Ajouter > Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="93927-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_controller21.png)

<span data-ttu-id="93927-179">Dans la boîte de dialogue **Ajouter un modèle automatique**, sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="93927-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="93927-181">Renseignez la boîte de dialogue **Ajouter un contrôleur** :</span><span class="sxs-lookup"><span data-stu-id="93927-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="93927-182">**Classe de modèle :** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="93927-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="93927-183">**Classe de contexte de données :** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="93927-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Ajouter un contexte de données](adding-model/_static/dc5.png)

* <span data-ttu-id="93927-185">**Affichages :** conservez la valeur par défaut de chaque option activée.</span><span class="sxs-lookup"><span data-stu-id="93927-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="93927-186">**Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="93927-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="93927-187">Sélectionnez **Ajouter**</span><span class="sxs-lookup"><span data-stu-id="93927-187">Select **Add**</span></span>

<span data-ttu-id="93927-188">Visual Studio crée :</span><span class="sxs-lookup"><span data-stu-id="93927-188">Visual Studio creates:</span></span>

* <span data-ttu-id="93927-189">Un contrôleur de films (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="93927-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="93927-190">Razor afficher des fichiers pour les pages Create, Delete, Details, Edit et index (*views/movies/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="93927-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="93927-191">La création automatique de ces fichiers est appelée *génération de modèles automatique*.</span><span class="sxs-lookup"><span data-stu-id="93927-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="93927-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93927-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="93927-193">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="93927-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="93927-194">Sur Linux, exportez le chemin de l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="93927-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="93927-195">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="93927-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="93927-196">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="93927-197">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="93927-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="93927-198">Exportez le chemin de l’outil de structure :</span><span class="sxs-lookup"><span data-stu-id="93927-198">Export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="93927-199">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="93927-199">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="93927-200">Vous ne pouvez pas encore utiliser les pages générées automatiquement, car la base de données n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="93927-200">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="93927-201">Si vous exécutez l’application et cliquez sur le lien de l' **application de film** , vous recevez un message d’erreur Impossible d' *ouvrir la base de données* ou une *table de ce type :* message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="93927-201">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="93927-202">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="93927-202">Initial migration</span></span>

<span data-ttu-id="93927-203">Utilisez la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-203">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="93927-204">La fonctionnalité Migrations est un ensemble d’outils qui vous permettent de créer et de mettre à jour une base de données pour qu’elle corresponde à votre modèle de données.</span><span class="sxs-lookup"><span data-stu-id="93927-204">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-205">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93927-206">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="93927-206">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="93927-207">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="93927-207">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="93927-208">`Add-Migration InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="93927-208">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="93927-209">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="93927-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="93927-210">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="93927-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="93927-211">Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="93927-212">Le schéma de base de données est basé sur le modèle spécifié dans la classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="93927-212">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="93927-213">`Update-Database`: Met à jour la base de données avec la dernière migration, créée par la commande précédente.</span><span class="sxs-lookup"><span data-stu-id="93927-213">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="93927-214">La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs*, ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="93927-215">La commande « database update » génère l’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="93927-215">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="93927-216">Aucun type n’a été spécifié pour la colonne décimale 'Price' sur le type d’entité 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="93927-216">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="93927-217">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="93927-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="93927-218">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant ’HasColumnType()’.</span><span class="sxs-lookup"><span data-stu-id="93927-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="93927-219">Vous pouvez ignorer cet avertissement, il sera corrigé dans un prochain tutoriel.</span><span class="sxs-lookup"><span data-stu-id="93927-219">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-220">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="93927-221">Exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="93927-221">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="93927-222">`ef migrations add InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="93927-222">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="93927-223">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="93927-223">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="93927-224">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="93927-224">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="93927-225">Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-225">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="93927-226">Le schéma de base de donénes est basé sur le modèle spécifié dans la classe `MvcMovieContext` (dans *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="93927-226">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="93927-227">`ef database update`: Met à jour la base de données avec la dernière migration, créée par la commande précédente.</span><span class="sxs-lookup"><span data-stu-id="93927-227">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="93927-228">La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs*, ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-228">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="93927-229">La classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="93927-229">The InitialCreate class</span></span>

<span data-ttu-id="93927-230">Examinez le fichier de migration *Migrations/{horodatage}_InitialCreate.cs* :</span><span class="sxs-lookup"><span data-stu-id="93927-230">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="93927-231">La méthode `Up` crée la table Movie et configure `Id` comme la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="93927-231">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="93927-232">La méthode `Down` rétablit les modifications de schéma provoquées par la migration `Up`.</span><span class="sxs-lookup"><span data-stu-id="93927-232">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="93927-233">Test de l'application</span><span class="sxs-lookup"><span data-stu-id="93927-233">Test the app</span></span>

* <span data-ttu-id="93927-234">Exécutez l’application et cliquez sur le lien **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="93927-234">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="93927-235">Si vous obtenez une exception similaire à celle-ci :</span><span class="sxs-lookup"><span data-stu-id="93927-235">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-236">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-237">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-237">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="93927-238">Il est probable que vous n’ayez pas effectué l’[étape de migration](#migration).</span><span class="sxs-lookup"><span data-stu-id="93927-238">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="93927-239">Testez la page **Create**.</span><span class="sxs-lookup"><span data-stu-id="93927-239">Test the **Create** page.</span></span> <span data-ttu-id="93927-240">Entrez et envoyez des données.</span><span class="sxs-lookup"><span data-stu-id="93927-240">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="93927-241">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="93927-241">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="93927-242">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="93927-242">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="93927-243">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="93927-243">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="93927-244">Testez les pages **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="93927-244">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="93927-245">Injection de dépendances dans le contrôleur</span><span class="sxs-lookup"><span data-stu-id="93927-245">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93927-247">Ouvrez le fichier *Controllers/MoviesController.cs* et examinez le constructeur :</span><span class="sxs-lookup"><span data-stu-id="93927-247">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="93927-248">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="93927-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="93927-249">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="93927-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-250">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="93927-251">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="93927-251">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="93927-252">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="93927-252">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="93927-253">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="93927-253">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="93927-254">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="93927-254">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="93927-255">Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage.</span><span class="sxs-lookup"><span data-stu-id="93927-255">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="93927-256">`IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="93927-256">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="93927-257">Modèles fortement typés et mot clé @model</span><span class="sxs-lookup"><span data-stu-id="93927-257">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="93927-258">Plus tôt dans ce didacticiel, vous avez vu comment un contrôleur peut passer des données ou des objets à une vue en utilisant le dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="93927-258">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="93927-259">Le dictionnaire `ViewData` est un objet dynamique qui fournit un moyen pratique d’effectuer une liaison tardive pour passer des informations à une vue.</span><span class="sxs-lookup"><span data-stu-id="93927-259">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="93927-260">Le modèle MVC fournit également la possibilité de passer des objets de modèle fortement typés à une vue.</span><span class="sxs-lookup"><span data-stu-id="93927-260">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="93927-261">Cette approche fortement typée permet de vérifier votre code au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="93927-261">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="93927-262">Le mécanisme de génération de modèles automatique a utilisé cette approche (c’est-à-dire passer un modèle fortement typé) avec la classe `MoviesController` et des vues.</span><span class="sxs-lookup"><span data-stu-id="93927-262">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="93927-263">Examinez la méthode `Details` générée dans le fichier *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="93927-263">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="93927-264">Le paramètre `id` est généralement passé en tant que données de routage.</span><span class="sxs-lookup"><span data-stu-id="93927-264">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="93927-265">Par exemple, `https://localhost:5001/movies/details/1` définit :</span><span class="sxs-lookup"><span data-stu-id="93927-265">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="93927-266">Le contrôleur sur le contrôleur `movies` (le premier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="93927-266">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="93927-267">L’action sur `details` (le deuxième segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="93927-267">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="93927-268">L’ID sur 1 (le dernier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="93927-268">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="93927-269">Vous pouvez aussi passer `id` avec une requête de chaîne, comme suit :</span><span class="sxs-lookup"><span data-stu-id="93927-269">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="93927-270">Le `id` paramètre est défini en tant que [type Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) au cas où une valeur d’ID n’est pas fournie.</span><span class="sxs-lookup"><span data-stu-id="93927-270">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="93927-271">Une [expression lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) est passée à `FirstOrDefaultAsync` pour sélectionner les entités de film qui correspondent aux données de routage ou à la valeur de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="93927-271">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="93927-272">Si un film est trouvé, une instance du modèle `Movie` est passée à la vue `Details` :</span><span class="sxs-lookup"><span data-stu-id="93927-272">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="93927-273">Examinez le contenu du fichier *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="93927-273">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="93927-274">L’instruction `@model` située en haut du fichier de la vue spécifie le type d’objet attendu par la vue.</span><span class="sxs-lookup"><span data-stu-id="93927-274">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="93927-275">Lorsque le contrôleur de film était créé, l’instruction `@model` suivante était incluse :</span><span class="sxs-lookup"><span data-stu-id="93927-275">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="93927-276">Cette directive `@model` autorise l’accès au film que le contrôleur a passé à la vue.</span><span class="sxs-lookup"><span data-stu-id="93927-276">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="93927-277">L’objet `Model` est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="93927-277">The `Model` object is strongly typed.</span></span> <span data-ttu-id="93927-278">Par exemple, dans la vue *Details.cshtml*, le code passe chaque champ du film aux Helpers HTML `DisplayNameFor` et `DisplayFor` avec l’objet `Model` fortement typé.</span><span class="sxs-lookup"><span data-stu-id="93927-278">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="93927-279">Les méthodes et les vues `Create` et `Edit` passent aussi un objet du modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="93927-279">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="93927-280">Examinez la vue *Index.cshtml* et la méthode `Index` dans le contrôleur Movies.</span><span class="sxs-lookup"><span data-stu-id="93927-280">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="93927-281">Notez comment le code crée un objet `List` quand il appelle la méthode `View`.</span><span class="sxs-lookup"><span data-stu-id="93927-281">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="93927-282">Le code passe cette liste `Movies` de la méthode d’action `Index` à la vue :</span><span class="sxs-lookup"><span data-stu-id="93927-282">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="93927-283">Lors de la création du contrôleur du film, la génération de modèles automatique a inclus l’instruction `@model` suivante en haut du fichier *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="93927-283">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="93927-284">La directive `@model` vous permet d’accéder à la liste des films que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="93927-284">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="93927-285">Par exemple, dans la vue *Index.cshtml*, le code boucle dans les films avec une instruction `foreach` sur l’objet `Model` fortement typé :</span><span class="sxs-lookup"><span data-stu-id="93927-285">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="93927-286">Comme l’objet `Model` est fortement typé (en tant qu’objet `IEnumerable<Movie>`), chaque élément de la boucle est typé en tant que `Movie`.</span><span class="sxs-lookup"><span data-stu-id="93927-286">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="93927-287">Entre autres avantages, cela signifie que votre code est vérifié au moment de la compilation :</span><span class="sxs-lookup"><span data-stu-id="93927-287">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93927-288">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="93927-288">Additional resources</span></span>

* [<span data-ttu-id="93927-289">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="93927-289">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="93927-290">Globalisation et localisation</span><span class="sxs-lookup"><span data-stu-id="93927-290">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="93927-291">[Ajout d’une vue précédente](adding-view.md) 
>  [Utilisation de SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="93927-291">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="93927-292">Ajouter une classe de modèle de données</span><span class="sxs-lookup"><span data-stu-id="93927-292">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-293">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-293">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93927-294">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="93927-294">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="93927-295">Nommez le fichier *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="93927-295">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="93927-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93927-296">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="93927-297">Ajoutez un fichier nommé *Movie.cs* au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="93927-297">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="93927-298">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="93927-299">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** une classe  >    >  **vide** Class.</span><span class="sxs-lookup"><span data-stu-id="93927-299">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="93927-300">Nommez le fichier *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="93927-300">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="93927-301">Mettez le fichier *Movie.cs* à jour avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="93927-301">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="93927-302">La classe `Movie` contient un champ `Id`, qui est nécessaire à la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="93927-302">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="93927-303">L' <xref:System.ComponentModel.DataAnnotations.DataType> attribut sur `ReleaseDate` spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="93927-303">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="93927-304">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="93927-304">With this attribute:</span></span>

* <span data-ttu-id="93927-305">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="93927-305">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="93927-306">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="93927-306">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="93927-307">Les [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="93927-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="93927-308">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="93927-308">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-309">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93927-310">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="93927-310">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu Console du Gestionnaire de package](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="93927-312">Dans la console du gestionnaire de package, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="93927-312">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="93927-313">La commande précédente ajoute le fournisseur EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="93927-313">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="93927-314">Le package du fournisseur installe le package EF Core en tant que dépendance.</span><span class="sxs-lookup"><span data-stu-id="93927-314">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="93927-315">Plus loin dans ce tutoriel, d’autres packages sont installés automatiquement lors de l’étape de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="93927-315">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="93927-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93927-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="93927-317">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="93927-318">Dans le menu **projet** , sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="93927-318">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="93927-319">Dans le champ de **recherche** en haut à droite, entrez `Microsoft.EntityFrameworkCore.SQLite` et appuyez sur la touche **retour** pour effectuer la recherche.</span><span class="sxs-lookup"><span data-stu-id="93927-319">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="93927-320">Sélectionnez le package NuGet correspondant, puis cliquez sur le bouton **Ajouter un package** .</span><span class="sxs-lookup"><span data-stu-id="93927-320">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Ajouter Entity Framework Core package NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="93927-322">La boîte de dialogue **Sélectionner les projets** s’affiche, avec le `MvcMovie` projet sélectionné.</span><span class="sxs-lookup"><span data-stu-id="93927-322">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="93927-323">Appuyez sur le bouton **OK** .</span><span class="sxs-lookup"><span data-stu-id="93927-323">Press the **Ok** button.</span></span>

<span data-ttu-id="93927-324">Une boîte de dialogue d' **acceptation de licence** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="93927-324">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="93927-325">Passez en revue les licences comme vous le souhaitez, puis cliquez sur le bouton **accepter** .</span><span class="sxs-lookup"><span data-stu-id="93927-325">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="93927-326">Répétez les étapes ci-dessus pour installer les packages NuGet suivants :</span><span class="sxs-lookup"><span data-stu-id="93927-326">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="93927-327">Créer une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="93927-327">Create a database context class</span></span>

<span data-ttu-id="93927-328">Une classe de contexte de base de données est nécessaire pour coordonner les fonctionnalités d’EF Core (créer, lire, mettre à jour, supprimer) pour le modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="93927-328">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="93927-329">Le contexte de base de données est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) et il spécifie les entités à inclure dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="93927-329">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="93927-330">Créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="93927-330">Create a *Data* folder.</span></span>

<span data-ttu-id="93927-331">Ajoutez un fichier *Data/MvcMovieContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="93927-331">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="93927-332">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="93927-332">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="93927-333">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-333">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="93927-334">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="93927-334">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="93927-335">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="93927-335">Register the database context</span></span>

<span data-ttu-id="93927-336">ASP.NET Core comprend [l’injection de dépendances (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="93927-336">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="93927-337">Les services (tels que le contexte de base de données EF Core) doivent être inscrits auprès de l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="93927-337">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="93927-338">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="93927-338">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="93927-339">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="93927-339">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="93927-340">Dans cette section, vous allez inscrire le contexte de base de données auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="93927-340">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="93927-341">En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="93927-341">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="93927-342">Ajoutez le code en surbrillance suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="93927-342">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-343">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-343">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-344">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="93927-345">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="93927-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="93927-346">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="93927-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="93927-347">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="93927-347">Add a database connection string</span></span>

<span data-ttu-id="93927-348">Ajoutez une chaîne de connexion au *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="93927-348">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-349">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-349">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-13)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-350">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-350">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-13)]

---

<span data-ttu-id="93927-351">Générez le projet en tant que vérification des erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="93927-351">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="93927-352">Générer automatiquement des pages de film</span><span class="sxs-lookup"><span data-stu-id="93927-352">Scaffold movie pages</span></span>

<span data-ttu-id="93927-353">Utilisez l’outil de génération de modèles automatique pour créer, lire, mettre à jour et supprimer des pages du modèle de film.</span><span class="sxs-lookup"><span data-stu-id="93927-353">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93927-355">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Contrôleurs*, puis choisissez **Ajouter > Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="93927-355">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_controller21.png)

<span data-ttu-id="93927-357">Dans la boîte de dialogue **Ajouter un modèle automatique**, sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="93927-357">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="93927-359">Renseignez la boîte de dialogue **Ajouter un contrôleur** :</span><span class="sxs-lookup"><span data-stu-id="93927-359">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="93927-360">**Classe de modèle :** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="93927-360">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="93927-361">**Classe de contexte de données :** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="93927-361">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Ajouter un contexte de données](adding-model/_static/dc3.png)

* <span data-ttu-id="93927-363">**Affichages :** conservez la valeur par défaut de chaque option activée.</span><span class="sxs-lookup"><span data-stu-id="93927-363">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="93927-364">**Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="93927-364">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="93927-365">Sélectionnez **Ajouter**</span><span class="sxs-lookup"><span data-stu-id="93927-365">Select **Add**</span></span>

<span data-ttu-id="93927-366">Visual Studio crée :</span><span class="sxs-lookup"><span data-stu-id="93927-366">Visual Studio creates:</span></span>

* <span data-ttu-id="93927-367">Un contrôleur de films (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="93927-367">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="93927-368">Razor afficher des fichiers pour les pages Create, Delete, Details, Edit et index (*views/movies/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="93927-368">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="93927-369">La création automatique de ces fichiers est appelée *génération de modèles automatique*.</span><span class="sxs-lookup"><span data-stu-id="93927-369">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="93927-370">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93927-370">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="93927-371">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="93927-371">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="93927-372">Sur Linux, exportez le chemin de l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="93927-372">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="93927-373">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="93927-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="93927-374">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-374">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="93927-375">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="93927-375">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="93927-376">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="93927-376">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="93927-377">Vous ne pouvez pas encore utiliser les pages générées automatiquement, car la base de données n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="93927-377">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="93927-378">Si vous exécutez l’application et cliquez sur le lien de l' **application de film** , vous recevez un message d’erreur Impossible d' *ouvrir la base de données* ou une *table de ce type :* message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="93927-378">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="93927-379">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="93927-379">Initial migration</span></span>

<span data-ttu-id="93927-380">Utilisez la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-380">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="93927-381">La fonctionnalité Migrations est un ensemble d’outils qui vous permettent de créer et de mettre à jour une base de données pour qu’elle corresponde à votre modèle de données.</span><span class="sxs-lookup"><span data-stu-id="93927-381">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-382">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-382">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93927-383">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="93927-383">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="93927-384">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="93927-384">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="93927-385">`Add-Migration InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="93927-385">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="93927-386">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="93927-386">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="93927-387">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="93927-387">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="93927-388">Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-388">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="93927-389">Le schéma de base de données est basé sur le modèle spécifié dans la classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="93927-389">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="93927-390">`Update-Database`: Met à jour la base de données avec la dernière migration, créée par la commande précédente.</span><span class="sxs-lookup"><span data-stu-id="93927-390">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="93927-391">La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs*, ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-391">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="93927-392">La commande « database update » génère l’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="93927-392">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="93927-393">Aucun type n’a été spécifié pour la colonne décimale 'Price' sur le type d’entité 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="93927-393">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="93927-394">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="93927-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="93927-395">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant ’HasColumnType()’.</span><span class="sxs-lookup"><span data-stu-id="93927-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="93927-396">Vous pouvez ignorer cet avertissement, il sera corrigé dans un prochain tutoriel.</span><span class="sxs-lookup"><span data-stu-id="93927-396">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-397">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-397">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="93927-398">Exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="93927-398">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="93927-399">`ef migrations add InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="93927-399">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="93927-400">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="93927-400">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="93927-401">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="93927-401">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="93927-402">Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-402">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="93927-403">Le schéma de base de donénes est basé sur le modèle spécifié dans la classe `MvcMovieContext` (dans *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="93927-403">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="93927-404">`ef database update`: Met à jour la base de données avec la dernière migration, créée par la commande précédente.</span><span class="sxs-lookup"><span data-stu-id="93927-404">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="93927-405">La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs*, ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-405">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="93927-406">La classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="93927-406">The InitialCreate class</span></span>

<span data-ttu-id="93927-407">Examinez le fichier de migration *Migrations/{horodatage}_InitialCreate.cs* :</span><span class="sxs-lookup"><span data-stu-id="93927-407">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="93927-408">La méthode `Up` crée la table Movie et configure `Id` comme la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="93927-408">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="93927-409">La méthode `Down` rétablit les modifications de schéma provoquées par la migration `Up`.</span><span class="sxs-lookup"><span data-stu-id="93927-409">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="93927-410">Test de l'application</span><span class="sxs-lookup"><span data-stu-id="93927-410">Test the app</span></span>

* <span data-ttu-id="93927-411">Exécutez l’application et cliquez sur le lien **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="93927-411">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="93927-412">Si vous obtenez une exception similaire à celle-ci :</span><span class="sxs-lookup"><span data-stu-id="93927-412">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-413">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-413">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-414">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-414">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="93927-415">Il est probable que vous n’ayez pas effectué l’[étape de migration](#migration).</span><span class="sxs-lookup"><span data-stu-id="93927-415">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="93927-416">Testez la page **Create**.</span><span class="sxs-lookup"><span data-stu-id="93927-416">Test the **Create** page.</span></span> <span data-ttu-id="93927-417">Entrez et envoyez des données.</span><span class="sxs-lookup"><span data-stu-id="93927-417">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="93927-418">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="93927-418">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="93927-419">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="93927-419">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="93927-420">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="93927-420">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="93927-421">Testez les pages **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="93927-421">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="93927-422">Injection de dépendances dans le contrôleur</span><span class="sxs-lookup"><span data-stu-id="93927-422">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93927-424">Ouvrez le fichier *Controllers/MoviesController.cs* et examinez le constructeur :</span><span class="sxs-lookup"><span data-stu-id="93927-424">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="93927-425">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="93927-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="93927-426">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="93927-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-427">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-427">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="93927-428">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="93927-428">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="93927-429">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="93927-429">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="93927-430">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="93927-430">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="93927-431">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="93927-431">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="93927-432">Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage.</span><span class="sxs-lookup"><span data-stu-id="93927-432">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="93927-433">`IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="93927-433">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="93927-434">Modèles fortement typés et mot clé @model</span><span class="sxs-lookup"><span data-stu-id="93927-434">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="93927-435">Plus tôt dans ce didacticiel, vous avez vu comment un contrôleur peut passer des données ou des objets à une vue en utilisant le dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="93927-435">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="93927-436">Le dictionnaire `ViewData` est un objet dynamique qui fournit un moyen pratique d’effectuer une liaison tardive pour passer des informations à une vue.</span><span class="sxs-lookup"><span data-stu-id="93927-436">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="93927-437">Le modèle MVC fournit également la possibilité de passer des objets de modèle fortement typés à une vue.</span><span class="sxs-lookup"><span data-stu-id="93927-437">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="93927-438">Cette approche fortement typée permet de vérifier votre code au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="93927-438">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="93927-439">Le mécanisme de génération de modèles automatique a utilisé cette approche (c’est-à-dire passer un modèle fortement typé) avec la classe `MoviesController` et des vues.</span><span class="sxs-lookup"><span data-stu-id="93927-439">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="93927-440">Examinez la méthode `Details` générée dans le fichier *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="93927-440">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="93927-441">Le paramètre `id` est généralement passé en tant que données de routage.</span><span class="sxs-lookup"><span data-stu-id="93927-441">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="93927-442">Par exemple, `https://localhost:5001/movies/details/1` définit :</span><span class="sxs-lookup"><span data-stu-id="93927-442">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="93927-443">Le contrôleur sur le contrôleur `movies` (le premier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="93927-443">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="93927-444">L’action sur `details` (le deuxième segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="93927-444">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="93927-445">L’ID sur 1 (le dernier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="93927-445">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="93927-446">Vous pouvez aussi passer `id` avec une requête de chaîne, comme suit :</span><span class="sxs-lookup"><span data-stu-id="93927-446">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="93927-447">Le `id` paramètre est défini en tant que [type Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) au cas où une valeur d’ID n’est pas fournie.</span><span class="sxs-lookup"><span data-stu-id="93927-447">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="93927-448">Une [expression lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) est passée à `FirstOrDefaultAsync` pour sélectionner les entités de film qui correspondent aux données de routage ou à la valeur de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="93927-448">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="93927-449">Si un film est trouvé, une instance du modèle `Movie` est passée à la vue `Details` :</span><span class="sxs-lookup"><span data-stu-id="93927-449">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="93927-450">Examinez le contenu du fichier *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="93927-450">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="93927-451">L’instruction `@model` située en haut du fichier de la vue spécifie le type d’objet attendu par la vue.</span><span class="sxs-lookup"><span data-stu-id="93927-451">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="93927-452">Lorsque le contrôleur de film était créé, l’instruction `@model` suivante était incluse :</span><span class="sxs-lookup"><span data-stu-id="93927-452">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="93927-453">Cette directive `@model` autorise l’accès au film que le contrôleur a passé à la vue.</span><span class="sxs-lookup"><span data-stu-id="93927-453">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="93927-454">L’objet `Model` est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="93927-454">The `Model` object is strongly typed.</span></span> <span data-ttu-id="93927-455">Par exemple, dans la vue *Details.cshtml*, le code passe chaque champ du film aux Helpers HTML `DisplayNameFor` et `DisplayFor` avec l’objet `Model` fortement typé.</span><span class="sxs-lookup"><span data-stu-id="93927-455">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="93927-456">Les méthodes et les vues `Create` et `Edit` passent aussi un objet du modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="93927-456">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="93927-457">Examinez la vue *Index.cshtml* et la méthode `Index` dans le contrôleur Movies.</span><span class="sxs-lookup"><span data-stu-id="93927-457">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="93927-458">Notez comment le code crée un objet `List` quand il appelle la méthode `View`.</span><span class="sxs-lookup"><span data-stu-id="93927-458">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="93927-459">Le code passe cette liste `Movies` de la méthode d’action `Index` à la vue :</span><span class="sxs-lookup"><span data-stu-id="93927-459">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="93927-460">Lors de la création du contrôleur du film, la génération de modèles automatique a inclus l’instruction `@model` suivante en haut du fichier *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="93927-460">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="93927-461">La directive `@model` vous permet d’accéder à la liste des films que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="93927-461">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="93927-462">Par exemple, dans la vue *Index.cshtml*, le code boucle dans les films avec une instruction `foreach` sur l’objet `Model` fortement typé :</span><span class="sxs-lookup"><span data-stu-id="93927-462">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="93927-463">Comme l’objet `Model` est fortement typé (en tant qu’objet `IEnumerable<Movie>`), chaque élément de la boucle est typé en tant que `Movie`.</span><span class="sxs-lookup"><span data-stu-id="93927-463">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="93927-464">Entre autres avantages, cela signifie que votre code est vérifié au moment de la compilation :</span><span class="sxs-lookup"><span data-stu-id="93927-464">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93927-465">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="93927-465">Additional resources</span></span>

* [<span data-ttu-id="93927-466">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="93927-466">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="93927-467">Globalisation et localisation</span><span class="sxs-lookup"><span data-stu-id="93927-467">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="93927-468">[Ajout d’une vue précédente](adding-view.md) 
>  [Utilisation de SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="93927-468">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="93927-469">Ajouter une classe de modèle de données</span><span class="sxs-lookup"><span data-stu-id="93927-469">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-470">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93927-471">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="93927-471">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="93927-472">Nommez la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="93927-472">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-473">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="93927-474">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="93927-474">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="93927-475">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="93927-475">Scaffold the movie model</span></span>

<span data-ttu-id="93927-476">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="93927-476">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="93927-477">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="93927-477">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-478">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-478">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93927-479">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Contrôleurs*, puis choisissez **Ajouter > Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="93927-479">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_controller21.png)

<span data-ttu-id="93927-481">Dans la boîte de dialogue **Ajouter un modèle automatique**, sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="93927-481">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="93927-483">Renseignez la boîte de dialogue **Ajouter un contrôleur** :</span><span class="sxs-lookup"><span data-stu-id="93927-483">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="93927-484">**Classe de modèle :** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="93927-484">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="93927-485">**Classe de contexte de données :** sélectionnez l’icône **+** et ajoutez le **MvcMovie.Models.MvcMovieContext** par défaut.</span><span class="sxs-lookup"><span data-stu-id="93927-485">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Ajouter un contexte de données](adding-model/_static/dc.png)

* <span data-ttu-id="93927-487">**Affichages :** conservez la valeur par défaut de chaque option activée.</span><span class="sxs-lookup"><span data-stu-id="93927-487">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="93927-488">**Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="93927-488">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="93927-489">Sélectionnez **Ajouter**</span><span class="sxs-lookup"><span data-stu-id="93927-489">Select **Add**</span></span>

![Boîte de dialogue Ajouter un contrôleur](adding-model/_static/add_controller2.png)

<span data-ttu-id="93927-491">Visual Studio crée :</span><span class="sxs-lookup"><span data-stu-id="93927-491">Visual Studio creates:</span></span>

* <span data-ttu-id="93927-492">Une [classe de contexte de base de données](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="93927-492">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="93927-493">Un contrôleur de films (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="93927-493">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="93927-494">Razor afficher des fichiers pour les pages Create, Delete, Details, Edit et index (*views/movies/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="93927-494">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="93927-495">La création automatique du contexte de base de données et de méthodes d’action et de vues [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (créer, lire, mettre à jour et supprimer) porte le nom de *génération de modèles automatique*.</span><span class="sxs-lookup"><span data-stu-id="93927-495">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="93927-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93927-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="93927-497">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="93927-497">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="93927-498">Installez l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="93927-498">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="93927-499">Sur Linux, exportez le chemin de l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="93927-499">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="93927-500">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="93927-500">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="93927-501">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-501">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="93927-502">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="93927-502">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="93927-503">Installez l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="93927-503">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="93927-504">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="93927-504">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="93927-505">Si vous exécutez l’application et que vous cliquez sur le lien **Mvc Movie**, vous recevez une erreur semblable à la suivante :</span><span class="sxs-lookup"><span data-stu-id="93927-505">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-506">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-507">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-507">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="93927-508">Vous devez créer la base de données, et vous utilisez pour cela la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core.</span><span class="sxs-lookup"><span data-stu-id="93927-508">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="93927-509">Les migrations permettent de créer une base de données qui correspond à votre modèle de données, et de mettre à jour le schéma de base de données quand votre modèle de données change.</span><span class="sxs-lookup"><span data-stu-id="93927-509">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="93927-510">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="93927-510">Initial migration</span></span>

<span data-ttu-id="93927-511">Dans cette section, vous devez effectuer les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="93927-511">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="93927-512">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="93927-512">Add an initial migration.</span></span>
* <span data-ttu-id="93927-513">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="93927-513">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-514">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-514">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="93927-515">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="93927-515">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu Console du Gestionnaire de package](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="93927-517">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="93927-517">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="93927-518">La commande `Add-Migration` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="93927-518">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="93927-519">Le schéma de base de données est basé sur le modèle spécifié dans la classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="93927-519">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="93927-520">L’argument `Initial` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="93927-520">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="93927-521">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est utilisé.</span><span class="sxs-lookup"><span data-stu-id="93927-521">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="93927-522">Pour plus d’informations, consultez <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="93927-522">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="93927-523">La `Update-Database` commande exécute la `Up` méthode dans le fichier *migrations/{Time-horodatage} _InitialCreate. cs* , qui crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-523">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-524">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-524">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="93927-525">La commande `ef migrations add InitialCreate` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="93927-525">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="93927-526">Le schéma de base de donénes est basé sur le modèle spécifié dans la classe `MvcMovieContext` (dans *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="93927-526">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="93927-527">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="93927-527">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="93927-528">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="93927-528">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="93927-529">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="93927-529">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="93927-530">ASP.NET Core comprend [l’injection de dépendances (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="93927-530">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="93927-531">Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="93927-531">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="93927-532">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="93927-532">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="93927-533">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="93927-533">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93927-534">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93927-534">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93927-535">L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="93927-535">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="93927-536">Examinez la méthode `Startup.ConfigureServices` suivante.</span><span class="sxs-lookup"><span data-stu-id="93927-536">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="93927-537">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="93927-537">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="93927-538">`MvcMovieContext` coordonne les fonctionnalités d’EF Core (Create, Read, Update, Delete, etc.) pour le modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="93927-538">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="93927-539">Le contexte de données (`MvcMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="93927-539">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="93927-540">Il spécifie les entités qui sont incluses dans le modèle de données :</span><span class="sxs-lookup"><span data-stu-id="93927-540">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="93927-541">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="93927-541">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="93927-542">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="93927-542">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="93927-543">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="93927-543">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="93927-544">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="93927-544">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="93927-545">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="93927-545">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93927-546">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93927-546">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="93927-547">Vous avez créé un contexte de base de données et vous l’avez inscrit dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="93927-547">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="93927-548">Test de l'application</span><span class="sxs-lookup"><span data-stu-id="93927-548">Test the app</span></span>

* <span data-ttu-id="93927-549">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="93927-549">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="93927-550">Si vous obtenez une exception de base de données similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="93927-550">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="93927-551">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="93927-551">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="93927-552">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="93927-552">Test the **Create** link.</span></span> <span data-ttu-id="93927-553">Entrez et envoyez des données.</span><span class="sxs-lookup"><span data-stu-id="93927-553">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="93927-554">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="93927-554">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="93927-555">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="93927-555">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="93927-556">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="93927-556">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="93927-557">Testez les liens **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="93927-557">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="93927-558">Examiner la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="93927-558">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="93927-559">Le code précédent mis en surbrillance montre le contexte de la base de données des films qui est ajouté au conteneur [d’injection de dépendance](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="93927-559">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="93927-560">`services.AddDbContext<MvcMovieContext>(options =>` spécifie la base de données à utiliser et la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="93927-560">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="93927-561">`=>` est un [opérateur lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="93927-561">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="93927-562">Ouvrez le fichier *Controllers/MoviesController.cs* et examinez le constructeur :</span><span class="sxs-lookup"><span data-stu-id="93927-562">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="93927-563">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="93927-563">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="93927-564">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="93927-564">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="93927-565">Modèles fortement typés et mot clé @model</span><span class="sxs-lookup"><span data-stu-id="93927-565">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="93927-566">Plus tôt dans ce didacticiel, vous avez vu comment un contrôleur peut passer des données ou des objets à une vue en utilisant le dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="93927-566">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="93927-567">Le dictionnaire `ViewData` est un objet dynamique qui fournit un moyen pratique d’effectuer une liaison tardive pour passer des informations à une vue.</span><span class="sxs-lookup"><span data-stu-id="93927-567">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="93927-568">Le modèle MVC fournit également la possibilité de passer des objets de modèle fortement typés à une vue.</span><span class="sxs-lookup"><span data-stu-id="93927-568">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="93927-569">Cette approche fortement typée permet une meilleure vérification de votre code au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="93927-569">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="93927-570">Le mécanisme de génération de modèles automatique a utilisé cette approche (c’est-à-dire passer un modèle fortement typé) avec la classe `MoviesController` et les vues quand il a créé les méthodes et les vues.</span><span class="sxs-lookup"><span data-stu-id="93927-570">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="93927-571">Examinez la méthode `Details` générée dans le fichier *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="93927-571">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="93927-572">Le paramètre `id` est généralement passé en tant que données de routage.</span><span class="sxs-lookup"><span data-stu-id="93927-572">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="93927-573">Par exemple, `https://localhost:5001/movies/details/1` définit :</span><span class="sxs-lookup"><span data-stu-id="93927-573">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="93927-574">Le contrôleur sur le contrôleur `movies` (le premier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="93927-574">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="93927-575">L’action sur `details` (le deuxième segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="93927-575">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="93927-576">L’ID sur 1 (le dernier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="93927-576">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="93927-577">Vous pouvez aussi passer `id` avec une requête de chaîne, comme suit :</span><span class="sxs-lookup"><span data-stu-id="93927-577">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="93927-578">Le `id` paramètre est défini en tant que [type Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) au cas où une valeur d’ID n’est pas fournie.</span><span class="sxs-lookup"><span data-stu-id="93927-578">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="93927-579">Une [expression lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) est passée à `FirstOrDefaultAsync` pour sélectionner les entités de film qui correspondent aux données de routage ou à la valeur de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="93927-579">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="93927-580">Si un film est trouvé, une instance du modèle `Movie` est passée à la vue `Details` :</span><span class="sxs-lookup"><span data-stu-id="93927-580">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="93927-581">Examinez le contenu du fichier *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="93927-581">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="93927-582">En incluant une instruction `@model` en haut du fichier de la vue, vous pouvez spécifier le type d’objet attendu par la vue.</span><span class="sxs-lookup"><span data-stu-id="93927-582">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="93927-583">Quand vous avez créé le contrôleur pour les films, l’instruction `@model` suivante a été incluse automatiquement en haut du fichier *Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="93927-583">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="93927-584">Cette directive `@model` vous permet d’accéder au film que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="93927-584">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="93927-585">Par exemple, dans la vue *Details.cshtml*, le code passe chaque champ du film aux Helpers HTML `DisplayNameFor` et `DisplayFor` avec l’objet `Model` fortement typé.</span><span class="sxs-lookup"><span data-stu-id="93927-585">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="93927-586">Les méthodes et les vues `Create` et `Edit` passent aussi un objet du modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="93927-586">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="93927-587">Examinez la vue *Index.cshtml* et la méthode `Index` dans le contrôleur Movies.</span><span class="sxs-lookup"><span data-stu-id="93927-587">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="93927-588">Notez comment le code crée un objet `List` quand il appelle la méthode `View`.</span><span class="sxs-lookup"><span data-stu-id="93927-588">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="93927-589">Le code passe cette liste `Movies` de la méthode d’action `Index` à la vue :</span><span class="sxs-lookup"><span data-stu-id="93927-589">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="93927-590">Quand vous avez créé le contrôleur pour les films, la génération de modèles automatique a inclus automatiquement l’instruction `@model` suivante en haut du fichier *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="93927-590">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="93927-591">La directive `@model` vous permet d’accéder à la liste des films que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="93927-591">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="93927-592">Par exemple, dans la vue *Index.cshtml*, le code boucle dans les films avec une instruction `foreach` sur l’objet `Model` fortement typé :</span><span class="sxs-lookup"><span data-stu-id="93927-592">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="93927-593">Comme l’objet `Model` est fortement typé (en tant qu’objet `IEnumerable<Movie>`), chaque élément de la boucle est typé en tant que `Movie`.</span><span class="sxs-lookup"><span data-stu-id="93927-593">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="93927-594">Entre autres avantages, cela signifie que votre code est vérifié au moment de la compilation :</span><span class="sxs-lookup"><span data-stu-id="93927-594">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93927-595">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="93927-595">Additional resources</span></span>

* [<span data-ttu-id="93927-596">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="93927-596">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="93927-597">Globalisation et localisation</span><span class="sxs-lookup"><span data-stu-id="93927-597">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="93927-598">[Ajout d’une vue précédente](adding-view.md) 
>  [Utilisation d’une base de données](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="93927-598">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
