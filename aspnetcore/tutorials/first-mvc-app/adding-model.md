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
ms.openlocfilehash: 16cef6cc9e772f494515942072c2aaf58913ce91
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688447"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="a13ff-103">Partie 4 : ajouter un modèle à une application ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="a13ff-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="a13ff-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Ryan Nowak](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="a13ff-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="a13ff-105">Dans cette section, vous allez ajouter des classes pour la gestion des films dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="a13ff-106">Ces classes constituent la partie « **M** odèle » de l’application **M** VC.</span><span class="sxs-lookup"><span data-stu-id="a13ff-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="a13ff-107">Vous utilisez ces classes avec [Entity Framework Core](/ef/core) (EF Core) pour travailler avec une base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="a13ff-108">EF Core est un framework de mappage relationnel d’objets qui simplifie le code d’accès aux données à écrire.</span><span class="sxs-lookup"><span data-stu-id="a13ff-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="a13ff-109">Les classes de modèle que vous créez portent le nom de classes OCT (« **O** bjet **C** LR **T** raditionnel »)**,** car elles n’ont pas de dépendances envers EF Core.</span><span class="sxs-lookup"><span data-stu-id="a13ff-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="a13ff-110">Elles définissent simplement les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="a13ff-111">Dans ce didacticiel, vous écrivez d’abord les classes du modèle, puis EF Core crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="a13ff-112">Ajouter une classe de modèle de données</span><span class="sxs-lookup"><span data-stu-id="a13ff-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a13ff-114">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="a13ff-115">Nommez le fichier *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a13ff-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a13ff-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a13ff-117">Ajoutez un fichier nommé *Movie.cs* au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-118">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a13ff-119">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** une classe  >  **New Class**  >  **vide** Class.</span><span class="sxs-lookup"><span data-stu-id="a13ff-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="a13ff-120">Nommez le fichier *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="a13ff-121">Mettez le fichier *Movie.cs* à jour avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="a13ff-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="a13ff-122">La classe `Movie` contient un champ `Id`, qui est nécessaire à la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="a13ff-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="a13ff-123">L' <xref:System.ComponentModel.DataAnnotations.DataType> attribut sur `ReleaseDate` spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="a13ff-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="a13ff-124">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="a13ff-124">With this attribute:</span></span>

* <span data-ttu-id="a13ff-125">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="a13ff-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="a13ff-126">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="a13ff-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="a13ff-127">Les [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="a13ff-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="a13ff-128">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="a13ff-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a13ff-130">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="a13ff-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu Console du Gestionnaire de package](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="a13ff-132">Dans la console du gestionnaire de package, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a13ff-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="a13ff-133">La commande précédente ajoute le fournisseur EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a13ff-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="a13ff-134">Le package du fournisseur installe le package EF Core en tant que dépendance.</span><span class="sxs-lookup"><span data-stu-id="a13ff-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="a13ff-135">Plus loin dans ce tutoriel, d’autres packages sont installés automatiquement lors de l’étape de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="a13ff-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a13ff-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a13ff-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-137">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a13ff-138">Dans le menu **projet** , sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="a13ff-139">Dans le champ de **recherche** en haut à droite, entrez `Microsoft.EntityFrameworkCore.SQLite` et appuyez sur la touche **retour** pour effectuer la recherche.</span><span class="sxs-lookup"><span data-stu-id="a13ff-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="a13ff-140">Sélectionnez le package NuGet correspondant, puis cliquez sur le bouton **Ajouter un package** .</span><span class="sxs-lookup"><span data-stu-id="a13ff-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Ajouter Entity Framework Core package NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="a13ff-142">La boîte de dialogue **Sélectionner les projets** s’affiche, avec le `MvcMovie` projet sélectionné.</span><span class="sxs-lookup"><span data-stu-id="a13ff-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="a13ff-143">Appuyez sur le bouton **OK** .</span><span class="sxs-lookup"><span data-stu-id="a13ff-143">Press the **Ok** button.</span></span>

<span data-ttu-id="a13ff-144">Une boîte de dialogue d' **acceptation de licence** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="a13ff-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="a13ff-145">Passez en revue les licences comme vous le souhaitez, puis cliquez sur le bouton **accepter** .</span><span class="sxs-lookup"><span data-stu-id="a13ff-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="a13ff-146">Répétez les étapes ci-dessus pour installer les packages NuGet suivants :</span><span class="sxs-lookup"><span data-stu-id="a13ff-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="a13ff-147">Exécutez la commande CLI .NET suivante :</span><span class="sxs-lookup"><span data-stu-id="a13ff-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="a13ff-148">La commande précédente ajoute l' [outil de génération de modèles automatique ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="a13ff-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="a13ff-149">Créer une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="a13ff-149">Create a database context class</span></span>

<span data-ttu-id="a13ff-150">Une classe de contexte de base de données est nécessaire pour coordonner les fonctionnalités d’EF Core (créer, lire, mettre à jour, supprimer) pour le modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="a13ff-151">Le contexte de base de données est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) et il spécifie les entités à inclure dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="a13ff-152">Créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-152">Create a *Data* folder.</span></span>

<span data-ttu-id="a13ff-153">Ajoutez un fichier *Data/MvcMovieContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a13ff-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="a13ff-154">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="a13ff-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="a13ff-155">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="a13ff-156">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="a13ff-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="a13ff-157">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="a13ff-157">Register the database context</span></span>

<span data-ttu-id="a13ff-158">ASP.NET Core comprend [l’injection de dépendances (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a13ff-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a13ff-159">Les services (tels que le contexte de base de données EF Core) doivent être inscrits auprès de l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="a13ff-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="a13ff-160">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="a13ff-161">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="a13ff-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="a13ff-162">Dans cette section, vous allez inscrire le contexte de base de données auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="a13ff-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="a13ff-163">En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="a13ff-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="a13ff-164">Ajoutez le code en surbrillance suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a13ff-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-166">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="a13ff-167">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="a13ff-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="a13ff-168">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="a13ff-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="a13ff-169">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="a13ff-169">Add a database connection string</span></span>

<span data-ttu-id="a13ff-170">Ajoutez une chaîne de connexion au *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="a13ff-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-11)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-172">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-11)]

---

<span data-ttu-id="a13ff-173">Générez le projet en tant que vérification des erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="a13ff-174">Générer automatiquement des pages de film</span><span class="sxs-lookup"><span data-stu-id="a13ff-174">Scaffold movie pages</span></span>

<span data-ttu-id="a13ff-175">Utilisez l’outil de génération de modèles automatique pour créer, lire, mettre à jour et supprimer des pages du modèle de film.</span><span class="sxs-lookup"><span data-stu-id="a13ff-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a13ff-177">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Contrôleurs*, puis choisissez **Ajouter > Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_controller21.png)

<span data-ttu-id="a13ff-179">Dans la boîte de dialogue **Ajouter un modèle automatique**, sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="a13ff-181">Renseignez la boîte de dialogue **Ajouter un contrôleur** :</span><span class="sxs-lookup"><span data-stu-id="a13ff-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="a13ff-182">**Classe de modèle :** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="a13ff-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="a13ff-183">**Classe de contexte de données :** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="a13ff-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Ajouter un contexte de données](adding-model/_static/dc5.png)

* <span data-ttu-id="a13ff-185">**Affichages :** conservez la valeur par défaut de chaque option activée.</span><span class="sxs-lookup"><span data-stu-id="a13ff-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="a13ff-186">**Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="a13ff-187">Sélectionnez **Ajouter**</span><span class="sxs-lookup"><span data-stu-id="a13ff-187">Select **Add**</span></span>

<span data-ttu-id="a13ff-188">Visual Studio crée :</span><span class="sxs-lookup"><span data-stu-id="a13ff-188">Visual Studio creates:</span></span>

* <span data-ttu-id="a13ff-189">Un contrôleur de films (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="a13ff-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="a13ff-190">Razor afficher des fichiers pour les pages Create, Delete, Details, Edit et index (*views/movies/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="a13ff-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="a13ff-191">La création automatique de ces fichiers est appelée *génération de modèles automatique*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="a13ff-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a13ff-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="a13ff-193">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="a13ff-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="a13ff-194">Sur Linux, exportez le chemin de l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="a13ff-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="a13ff-195">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a13ff-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-196">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a13ff-197">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="a13ff-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="a13ff-198">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a13ff-198">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="a13ff-199">Vous ne pouvez pas encore utiliser les pages générées automatiquement, car la base de données n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="a13ff-199">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="a13ff-200">Si vous exécutez l’application et cliquez sur le lien de l' **application de film** , vous recevez un message d’erreur Impossible d' *ouvrir la base de données* ou une *table de ce type :* message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-200">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="a13ff-201">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="a13ff-201">Initial migration</span></span>

<span data-ttu-id="a13ff-202">Utilisez la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-202">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="a13ff-203">La fonctionnalité Migrations est un ensemble d’outils qui vous permettent de créer et de mettre à jour une base de données pour qu’elle corresponde à votre modèle de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-203">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-204">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a13ff-205">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="a13ff-205">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="a13ff-206">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a13ff-206">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="a13ff-207">`Add-Migration InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="a13ff-207">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="a13ff-208">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="a13ff-208">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="a13ff-209">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="a13ff-209">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="a13ff-210">Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-210">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="a13ff-211">Le schéma de base de données est basé sur le modèle spécifié dans la classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-211">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="a13ff-212">`Update-Database`: Met à jour la base de données avec la dernière migration, créée par la commande précédente.</span><span class="sxs-lookup"><span data-stu-id="a13ff-212">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="a13ff-213">La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs*, ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-213">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="a13ff-214">La commande « database update » génère l’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="a13ff-214">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="a13ff-215">Aucun type n’a été spécifié pour la colonne décimale 'Price' sur le type d’entité 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="a13ff-215">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="a13ff-216">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="a13ff-216">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="a13ff-217">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant ’HasColumnType()’.</span><span class="sxs-lookup"><span data-stu-id="a13ff-217">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="a13ff-218">Vous pouvez ignorer cet avertissement, il sera corrigé dans un prochain tutoriel.</span><span class="sxs-lookup"><span data-stu-id="a13ff-218">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-219">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-219">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="a13ff-220">Exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="a13ff-220">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="a13ff-221">`ef migrations add InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="a13ff-221">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="a13ff-222">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="a13ff-222">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="a13ff-223">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="a13ff-223">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="a13ff-224">Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-224">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="a13ff-225">Le schéma de base de donénes est basé sur le modèle spécifié dans la classe `MvcMovieContext` (dans *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="a13ff-225">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="a13ff-226">`ef database update`: Met à jour la base de données avec la dernière migration, créée par la commande précédente.</span><span class="sxs-lookup"><span data-stu-id="a13ff-226">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="a13ff-227">La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs*, ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-227">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="a13ff-228">La classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="a13ff-228">The InitialCreate class</span></span>

<span data-ttu-id="a13ff-229">Examinez le fichier de migration *Migrations/{horodatage}_InitialCreate.cs* :</span><span class="sxs-lookup"><span data-stu-id="a13ff-229">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="a13ff-230">La méthode `Up` crée la table Movie et configure `Id` comme la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="a13ff-230">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="a13ff-231">La méthode `Down` rétablit les modifications de schéma provoquées par la migration `Up`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-231">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="a13ff-232">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="a13ff-232">Test the app</span></span>

* <span data-ttu-id="a13ff-233">Exécutez l’application et cliquez sur le lien **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-233">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="a13ff-234">Si vous obtenez une exception similaire à celle-ci :</span><span class="sxs-lookup"><span data-stu-id="a13ff-234">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-235">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-236">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="a13ff-237">Il est probable que vous n’ayez pas effectué l’[étape de migration](#migration).</span><span class="sxs-lookup"><span data-stu-id="a13ff-237">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="a13ff-238">Testez la page **Create**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-238">Test the **Create** page.</span></span> <span data-ttu-id="a13ff-239">Entrez et envoyez des données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-239">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="a13ff-240">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-240">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a13ff-241">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="a13ff-241">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="a13ff-242">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="a13ff-242">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="a13ff-243">Testez les pages **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-243">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="a13ff-244">Injection de dépendances dans le contrôleur</span><span class="sxs-lookup"><span data-stu-id="a13ff-244">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a13ff-246">Ouvrez le fichier *Controllers/MoviesController.cs* et examinez le constructeur :</span><span class="sxs-lookup"><span data-stu-id="a13ff-246">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="a13ff-247">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-247">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="a13ff-248">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-248">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-249">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="a13ff-250">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-250">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="a13ff-251">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-251">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="a13ff-252">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="a13ff-252">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="a13ff-253">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="a13ff-253">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="a13ff-254">Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage.</span><span class="sxs-lookup"><span data-stu-id="a13ff-254">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="a13ff-255">`IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="a13ff-255">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="a13ff-256">Modèles fortement typés et mot clé @model</span><span class="sxs-lookup"><span data-stu-id="a13ff-256">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="a13ff-257">Plus tôt dans ce didacticiel, vous avez vu comment un contrôleur peut passer des données ou des objets à une vue en utilisant le dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-257">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="a13ff-258">Le dictionnaire `ViewData` est un objet dynamique qui fournit un moyen pratique d’effectuer une liaison tardive pour passer des informations à une vue.</span><span class="sxs-lookup"><span data-stu-id="a13ff-258">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="a13ff-259">Le modèle MVC fournit également la possibilité de passer des objets de modèle fortement typés à une vue.</span><span class="sxs-lookup"><span data-stu-id="a13ff-259">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="a13ff-260">Cette approche fortement typée permet de vérifier votre code au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="a13ff-260">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="a13ff-261">Le mécanisme de génération de modèles automatique a utilisé cette approche (c’est-à-dire passer un modèle fortement typé) avec la classe `MoviesController` et des vues.</span><span class="sxs-lookup"><span data-stu-id="a13ff-261">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="a13ff-262">Examinez la méthode `Details` générée dans le fichier *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="a13ff-262">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="a13ff-263">Le paramètre `id` est généralement passé en tant que données de routage.</span><span class="sxs-lookup"><span data-stu-id="a13ff-263">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="a13ff-264">Par exemple, `https://localhost:5001/movies/details/1` définit :</span><span class="sxs-lookup"><span data-stu-id="a13ff-264">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="a13ff-265">Le contrôleur sur le contrôleur `movies` (le premier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="a13ff-265">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="a13ff-266">L’action sur `details` (le deuxième segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="a13ff-266">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="a13ff-267">L’ID sur 1 (le dernier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="a13ff-267">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="a13ff-268">Vous pouvez aussi passer `id` avec une requête de chaîne, comme suit :</span><span class="sxs-lookup"><span data-stu-id="a13ff-268">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="a13ff-269">Le `id` paramètre est défini en tant que [type Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) au cas où une valeur d’ID n’est pas fournie.</span><span class="sxs-lookup"><span data-stu-id="a13ff-269">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="a13ff-270">Une [expression lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) est passée à `FirstOrDefaultAsync` pour sélectionner les entités de film qui correspondent aux données de routage ou à la valeur de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="a13ff-270">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="a13ff-271">Si un film est trouvé, une instance du modèle `Movie` est passée à la vue `Details` :</span><span class="sxs-lookup"><span data-stu-id="a13ff-271">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="a13ff-272">Examinez le contenu du fichier *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a13ff-272">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="a13ff-273">L’instruction `@model` située en haut du fichier de la vue spécifie le type d’objet attendu par la vue.</span><span class="sxs-lookup"><span data-stu-id="a13ff-273">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="a13ff-274">Lorsque le contrôleur de film était créé, l’instruction `@model` suivante était incluse :</span><span class="sxs-lookup"><span data-stu-id="a13ff-274">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="a13ff-275">Cette directive `@model` autorise l’accès au film que le contrôleur a passé à la vue.</span><span class="sxs-lookup"><span data-stu-id="a13ff-275">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="a13ff-276">L’objet `Model` est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="a13ff-276">The `Model` object is strongly typed.</span></span> <span data-ttu-id="a13ff-277">Par exemple, dans la vue *Details.cshtml*, le code passe chaque champ du film aux Helpers HTML `DisplayNameFor` et `DisplayFor` avec l’objet `Model` fortement typé.</span><span class="sxs-lookup"><span data-stu-id="a13ff-277">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="a13ff-278">Les méthodes et les vues `Create` et `Edit` passent aussi un objet du modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-278">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="a13ff-279">Examinez la vue *Index.cshtml* et la méthode `Index` dans le contrôleur Movies.</span><span class="sxs-lookup"><span data-stu-id="a13ff-279">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="a13ff-280">Notez comment le code crée un objet `List` quand il appelle la méthode `View`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-280">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="a13ff-281">Le code passe cette liste `Movies` de la méthode d’action `Index` à la vue :</span><span class="sxs-lookup"><span data-stu-id="a13ff-281">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="a13ff-282">Lors de la création du contrôleur du film, la génération de modèles automatique a inclus l’instruction `@model` suivante en haut du fichier *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a13ff-282">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="a13ff-283">La directive `@model` vous permet d’accéder à la liste des films que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="a13ff-283">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="a13ff-284">Par exemple, dans la vue *Index.cshtml*, le code boucle dans les films avec une instruction `foreach` sur l’objet `Model` fortement typé :</span><span class="sxs-lookup"><span data-stu-id="a13ff-284">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="a13ff-285">Comme l’objet `Model` est fortement typé (en tant qu’objet `IEnumerable<Movie>`), chaque élément de la boucle est typé en tant que `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-285">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="a13ff-286">Entre autres avantages, cela signifie que votre code est vérifié au moment de la compilation :</span><span class="sxs-lookup"><span data-stu-id="a13ff-286">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a13ff-287">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a13ff-287">Additional resources</span></span>

* [<span data-ttu-id="a13ff-288">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="a13ff-288">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="a13ff-289">Globalisation et localisation</span><span class="sxs-lookup"><span data-stu-id="a13ff-289">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="a13ff-290">[Ajout d’une vue précédente](adding-view.md) 
>  [Utilisation de SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="a13ff-290">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="a13ff-291">Ajouter une classe de modèle de données</span><span class="sxs-lookup"><span data-stu-id="a13ff-291">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-292">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-292">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a13ff-293">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-293">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="a13ff-294">Nommez le fichier *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-294">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a13ff-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a13ff-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a13ff-296">Ajoutez un fichier nommé *Movie.cs* au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-296">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-297">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-297">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a13ff-298">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** une classe  >  **New Class**  >  **vide** Class.</span><span class="sxs-lookup"><span data-stu-id="a13ff-298">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="a13ff-299">Nommez le fichier *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-299">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="a13ff-300">Mettez le fichier *Movie.cs* à jour avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="a13ff-300">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="a13ff-301">La classe `Movie` contient un champ `Id`, qui est nécessaire à la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="a13ff-301">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="a13ff-302">L' <xref:System.ComponentModel.DataAnnotations.DataType> attribut sur `ReleaseDate` spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="a13ff-302">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="a13ff-303">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="a13ff-303">With this attribute:</span></span>

* <span data-ttu-id="a13ff-304">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="a13ff-304">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="a13ff-305">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="a13ff-305">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="a13ff-306">Les [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="a13ff-306">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="a13ff-307">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="a13ff-307">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-308">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-308">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a13ff-309">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="a13ff-309">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu Console du Gestionnaire de package](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="a13ff-311">Dans la console du gestionnaire de package, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a13ff-311">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="a13ff-312">La commande précédente ajoute le fournisseur EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a13ff-312">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="a13ff-313">Le package du fournisseur installe le package EF Core en tant que dépendance.</span><span class="sxs-lookup"><span data-stu-id="a13ff-313">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="a13ff-314">Plus loin dans ce tutoriel, d’autres packages sont installés automatiquement lors de l’étape de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="a13ff-314">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a13ff-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a13ff-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-316">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-316">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a13ff-317">Dans le menu **projet** , sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-317">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="a13ff-318">Dans le champ de **recherche** en haut à droite, entrez `Microsoft.EntityFrameworkCore.SQLite` et appuyez sur la touche **retour** pour effectuer la recherche.</span><span class="sxs-lookup"><span data-stu-id="a13ff-318">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="a13ff-319">Sélectionnez le package NuGet correspondant, puis cliquez sur le bouton **Ajouter un package** .</span><span class="sxs-lookup"><span data-stu-id="a13ff-319">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Ajouter Entity Framework Core package NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="a13ff-321">La boîte de dialogue **Sélectionner les projets** s’affiche, avec le `MvcMovie` projet sélectionné.</span><span class="sxs-lookup"><span data-stu-id="a13ff-321">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="a13ff-322">Appuyez sur le bouton **OK** .</span><span class="sxs-lookup"><span data-stu-id="a13ff-322">Press the **Ok** button.</span></span>

<span data-ttu-id="a13ff-323">Une boîte de dialogue d' **acceptation de licence** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="a13ff-323">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="a13ff-324">Passez en revue les licences comme vous le souhaitez, puis cliquez sur le bouton **accepter** .</span><span class="sxs-lookup"><span data-stu-id="a13ff-324">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="a13ff-325">Répétez les étapes ci-dessus pour installer les packages NuGet suivants :</span><span class="sxs-lookup"><span data-stu-id="a13ff-325">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="a13ff-326">Créer une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="a13ff-326">Create a database context class</span></span>

<span data-ttu-id="a13ff-327">Une classe de contexte de base de données est nécessaire pour coordonner les fonctionnalités d’EF Core (créer, lire, mettre à jour, supprimer) pour le modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-327">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="a13ff-328">Le contexte de base de données est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) et il spécifie les entités à inclure dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-328">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="a13ff-329">Créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-329">Create a *Data* folder.</span></span>

<span data-ttu-id="a13ff-330">Ajoutez un fichier *Data/MvcMovieContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a13ff-330">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="a13ff-331">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="a13ff-331">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="a13ff-332">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-332">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="a13ff-333">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="a13ff-333">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="a13ff-334">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="a13ff-334">Register the database context</span></span>

<span data-ttu-id="a13ff-335">ASP.NET Core comprend [l’injection de dépendances (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a13ff-335">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a13ff-336">Les services (tels que le contexte de base de données EF Core) doivent être inscrits auprès de l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="a13ff-336">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="a13ff-337">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-337">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="a13ff-338">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="a13ff-338">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="a13ff-339">Dans cette section, vous allez inscrire le contexte de base de données auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="a13ff-339">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="a13ff-340">En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="a13ff-340">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="a13ff-341">Ajoutez le code en surbrillance suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a13ff-341">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-342">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-342">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-343">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-343">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="a13ff-344">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="a13ff-344">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="a13ff-345">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="a13ff-345">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="a13ff-346">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="a13ff-346">Add a database connection string</span></span>

<span data-ttu-id="a13ff-347">Ajoutez une chaîne de connexion au *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="a13ff-347">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-348">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-348">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-349">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-349">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="a13ff-350">Générez le projet en tant que vérification des erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-350">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="a13ff-351">Générer automatiquement des pages de film</span><span class="sxs-lookup"><span data-stu-id="a13ff-351">Scaffold movie pages</span></span>

<span data-ttu-id="a13ff-352">Utilisez l’outil de génération de modèles automatique pour créer, lire, mettre à jour et supprimer des pages du modèle de film.</span><span class="sxs-lookup"><span data-stu-id="a13ff-352">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-353">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-353">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a13ff-354">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Contrôleurs*, puis choisissez **Ajouter > Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-354">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_controller21.png)

<span data-ttu-id="a13ff-356">Dans la boîte de dialogue **Ajouter un modèle automatique**, sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-356">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="a13ff-358">Renseignez la boîte de dialogue **Ajouter un contrôleur** :</span><span class="sxs-lookup"><span data-stu-id="a13ff-358">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="a13ff-359">**Classe de modèle :** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="a13ff-359">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="a13ff-360">**Classe de contexte de données :** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="a13ff-360">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Ajouter un contexte de données](adding-model/_static/dc3.png)

* <span data-ttu-id="a13ff-362">**Affichages :** conservez la valeur par défaut de chaque option activée.</span><span class="sxs-lookup"><span data-stu-id="a13ff-362">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="a13ff-363">**Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-363">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="a13ff-364">Sélectionnez **Ajouter**</span><span class="sxs-lookup"><span data-stu-id="a13ff-364">Select **Add**</span></span>

<span data-ttu-id="a13ff-365">Visual Studio crée :</span><span class="sxs-lookup"><span data-stu-id="a13ff-365">Visual Studio creates:</span></span>

* <span data-ttu-id="a13ff-366">Un contrôleur de films (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="a13ff-366">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="a13ff-367">Razor afficher des fichiers pour les pages Create, Delete, Details, Edit et index (*views/movies/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="a13ff-367">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="a13ff-368">La création automatique de ces fichiers est appelée *génération de modèles automatique*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-368">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="a13ff-369">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a13ff-369">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="a13ff-370">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="a13ff-370">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="a13ff-371">Sur Linux, exportez le chemin de l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="a13ff-371">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="a13ff-372">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a13ff-372">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-373">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-373">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a13ff-374">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="a13ff-374">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="a13ff-375">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a13ff-375">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="a13ff-376">Vous ne pouvez pas encore utiliser les pages générées automatiquement, car la base de données n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="a13ff-376">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="a13ff-377">Si vous exécutez l’application et cliquez sur le lien de l' **application de film** , vous recevez un message d’erreur Impossible d' *ouvrir la base de données* ou une *table de ce type :* message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-377">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="a13ff-378">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="a13ff-378">Initial migration</span></span>

<span data-ttu-id="a13ff-379">Utilisez la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-379">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="a13ff-380">La fonctionnalité Migrations est un ensemble d’outils qui vous permettent de créer et de mettre à jour une base de données pour qu’elle corresponde à votre modèle de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-380">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-381">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a13ff-382">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="a13ff-382">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="a13ff-383">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a13ff-383">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="a13ff-384">`Add-Migration InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="a13ff-384">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="a13ff-385">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="a13ff-385">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="a13ff-386">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="a13ff-386">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="a13ff-387">Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-387">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="a13ff-388">Le schéma de base de données est basé sur le modèle spécifié dans la classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-388">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="a13ff-389">`Update-Database`: Met à jour la base de données avec la dernière migration, créée par la commande précédente.</span><span class="sxs-lookup"><span data-stu-id="a13ff-389">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="a13ff-390">La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs*, ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-390">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="a13ff-391">La commande « database update » génère l’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="a13ff-391">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="a13ff-392">Aucun type n’a été spécifié pour la colonne décimale 'Price' sur le type d’entité 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="a13ff-392">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="a13ff-393">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="a13ff-393">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="a13ff-394">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant ’HasColumnType()’.</span><span class="sxs-lookup"><span data-stu-id="a13ff-394">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="a13ff-395">Vous pouvez ignorer cet avertissement, il sera corrigé dans un prochain tutoriel.</span><span class="sxs-lookup"><span data-stu-id="a13ff-395">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-396">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-396">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="a13ff-397">Exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="a13ff-397">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="a13ff-398">`ef migrations add InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="a13ff-398">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="a13ff-399">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="a13ff-399">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="a13ff-400">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="a13ff-400">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="a13ff-401">Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-401">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="a13ff-402">Le schéma de base de donénes est basé sur le modèle spécifié dans la classe `MvcMovieContext` (dans *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="a13ff-402">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="a13ff-403">`ef database update`: Met à jour la base de données avec la dernière migration, créée par la commande précédente.</span><span class="sxs-lookup"><span data-stu-id="a13ff-403">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="a13ff-404">La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs*, ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-404">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="a13ff-405">La classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="a13ff-405">The InitialCreate class</span></span>

<span data-ttu-id="a13ff-406">Examinez le fichier de migration *Migrations/{horodatage}_InitialCreate.cs* :</span><span class="sxs-lookup"><span data-stu-id="a13ff-406">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="a13ff-407">La méthode `Up` crée la table Movie et configure `Id` comme la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="a13ff-407">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="a13ff-408">La méthode `Down` rétablit les modifications de schéma provoquées par la migration `Up`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-408">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="a13ff-409">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="a13ff-409">Test the app</span></span>

* <span data-ttu-id="a13ff-410">Exécutez l’application et cliquez sur le lien **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-410">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="a13ff-411">Si vous obtenez une exception similaire à celle-ci :</span><span class="sxs-lookup"><span data-stu-id="a13ff-411">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-412">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-412">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-413">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-413">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="a13ff-414">Il est probable que vous n’ayez pas effectué l’[étape de migration](#migration).</span><span class="sxs-lookup"><span data-stu-id="a13ff-414">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="a13ff-415">Testez la page **Create**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-415">Test the **Create** page.</span></span> <span data-ttu-id="a13ff-416">Entrez et envoyez des données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-416">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="a13ff-417">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-417">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a13ff-418">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="a13ff-418">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="a13ff-419">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="a13ff-419">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="a13ff-420">Testez les pages **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-420">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="a13ff-421">Injection de dépendances dans le contrôleur</span><span class="sxs-lookup"><span data-stu-id="a13ff-421">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-422">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a13ff-423">Ouvrez le fichier *Controllers/MoviesController.cs* et examinez le constructeur :</span><span class="sxs-lookup"><span data-stu-id="a13ff-423">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="a13ff-424">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-424">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="a13ff-425">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-425">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-426">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-426">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="a13ff-427">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-427">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="a13ff-428">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-428">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="a13ff-429">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="a13ff-429">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="a13ff-430">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="a13ff-430">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="a13ff-431">Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage.</span><span class="sxs-lookup"><span data-stu-id="a13ff-431">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="a13ff-432">`IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="a13ff-432">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="a13ff-433">Modèles fortement typés et mot clé @model</span><span class="sxs-lookup"><span data-stu-id="a13ff-433">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="a13ff-434">Plus tôt dans ce didacticiel, vous avez vu comment un contrôleur peut passer des données ou des objets à une vue en utilisant le dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-434">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="a13ff-435">Le dictionnaire `ViewData` est un objet dynamique qui fournit un moyen pratique d’effectuer une liaison tardive pour passer des informations à une vue.</span><span class="sxs-lookup"><span data-stu-id="a13ff-435">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="a13ff-436">Le modèle MVC fournit également la possibilité de passer des objets de modèle fortement typés à une vue.</span><span class="sxs-lookup"><span data-stu-id="a13ff-436">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="a13ff-437">Cette approche fortement typée permet de vérifier votre code au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="a13ff-437">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="a13ff-438">Le mécanisme de génération de modèles automatique a utilisé cette approche (c’est-à-dire passer un modèle fortement typé) avec la classe `MoviesController` et des vues.</span><span class="sxs-lookup"><span data-stu-id="a13ff-438">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="a13ff-439">Examinez la méthode `Details` générée dans le fichier *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="a13ff-439">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="a13ff-440">Le paramètre `id` est généralement passé en tant que données de routage.</span><span class="sxs-lookup"><span data-stu-id="a13ff-440">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="a13ff-441">Par exemple, `https://localhost:5001/movies/details/1` définit :</span><span class="sxs-lookup"><span data-stu-id="a13ff-441">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="a13ff-442">Le contrôleur sur le contrôleur `movies` (le premier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="a13ff-442">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="a13ff-443">L’action sur `details` (le deuxième segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="a13ff-443">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="a13ff-444">L’ID sur 1 (le dernier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="a13ff-444">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="a13ff-445">Vous pouvez aussi passer `id` avec une requête de chaîne, comme suit :</span><span class="sxs-lookup"><span data-stu-id="a13ff-445">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="a13ff-446">Le `id` paramètre est défini en tant que [type Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) au cas où une valeur d’ID n’est pas fournie.</span><span class="sxs-lookup"><span data-stu-id="a13ff-446">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="a13ff-447">Une [expression lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) est passée à `FirstOrDefaultAsync` pour sélectionner les entités de film qui correspondent aux données de routage ou à la valeur de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="a13ff-447">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="a13ff-448">Si un film est trouvé, une instance du modèle `Movie` est passée à la vue `Details` :</span><span class="sxs-lookup"><span data-stu-id="a13ff-448">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="a13ff-449">Examinez le contenu du fichier *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a13ff-449">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="a13ff-450">L’instruction `@model` située en haut du fichier de la vue spécifie le type d’objet attendu par la vue.</span><span class="sxs-lookup"><span data-stu-id="a13ff-450">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="a13ff-451">Lorsque le contrôleur de film était créé, l’instruction `@model` suivante était incluse :</span><span class="sxs-lookup"><span data-stu-id="a13ff-451">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="a13ff-452">Cette directive `@model` autorise l’accès au film que le contrôleur a passé à la vue.</span><span class="sxs-lookup"><span data-stu-id="a13ff-452">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="a13ff-453">L’objet `Model` est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="a13ff-453">The `Model` object is strongly typed.</span></span> <span data-ttu-id="a13ff-454">Par exemple, dans la vue *Details.cshtml*, le code passe chaque champ du film aux Helpers HTML `DisplayNameFor` et `DisplayFor` avec l’objet `Model` fortement typé.</span><span class="sxs-lookup"><span data-stu-id="a13ff-454">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="a13ff-455">Les méthodes et les vues `Create` et `Edit` passent aussi un objet du modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-455">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="a13ff-456">Examinez la vue *Index.cshtml* et la méthode `Index` dans le contrôleur Movies.</span><span class="sxs-lookup"><span data-stu-id="a13ff-456">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="a13ff-457">Notez comment le code crée un objet `List` quand il appelle la méthode `View`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-457">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="a13ff-458">Le code passe cette liste `Movies` de la méthode d’action `Index` à la vue :</span><span class="sxs-lookup"><span data-stu-id="a13ff-458">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="a13ff-459">Lors de la création du contrôleur du film, la génération de modèles automatique a inclus l’instruction `@model` suivante en haut du fichier *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a13ff-459">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="a13ff-460">La directive `@model` vous permet d’accéder à la liste des films que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="a13ff-460">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="a13ff-461">Par exemple, dans la vue *Index.cshtml*, le code boucle dans les films avec une instruction `foreach` sur l’objet `Model` fortement typé :</span><span class="sxs-lookup"><span data-stu-id="a13ff-461">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="a13ff-462">Comme l’objet `Model` est fortement typé (en tant qu’objet `IEnumerable<Movie>`), chaque élément de la boucle est typé en tant que `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-462">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="a13ff-463">Entre autres avantages, cela signifie que votre code est vérifié au moment de la compilation :</span><span class="sxs-lookup"><span data-stu-id="a13ff-463">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a13ff-464">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a13ff-464">Additional resources</span></span>

* [<span data-ttu-id="a13ff-465">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="a13ff-465">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="a13ff-466">Globalisation et localisation</span><span class="sxs-lookup"><span data-stu-id="a13ff-466">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="a13ff-467">[Ajout d’une vue précédente](adding-view.md) 
>  [Utilisation de SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="a13ff-467">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="a13ff-468">Ajouter une classe de modèle de données</span><span class="sxs-lookup"><span data-stu-id="a13ff-468">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a13ff-470">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-470">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="a13ff-471">Nommez la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-471">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-472">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-472">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="a13ff-473">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-473">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="a13ff-474">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="a13ff-474">Scaffold the movie model</span></span>

<span data-ttu-id="a13ff-475">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="a13ff-475">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="a13ff-476">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="a13ff-476">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-477">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-477">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a13ff-478">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Contrôleurs*, puis choisissez **Ajouter > Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-478">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_controller21.png)

<span data-ttu-id="a13ff-480">Dans la boîte de dialogue **Ajouter un modèle automatique**, sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-480">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="a13ff-482">Renseignez la boîte de dialogue **Ajouter un contrôleur** :</span><span class="sxs-lookup"><span data-stu-id="a13ff-482">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="a13ff-483">**Classe de modèle :** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="a13ff-483">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="a13ff-484">**Classe de contexte de données :** sélectionnez l’icône **+** et ajoutez le **MvcMovie.Models.MvcMovieContext** par défaut.</span><span class="sxs-lookup"><span data-stu-id="a13ff-484">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Ajouter un contexte de données](adding-model/_static/dc.png)

* <span data-ttu-id="a13ff-486">**Affichages :** conservez la valeur par défaut de chaque option activée.</span><span class="sxs-lookup"><span data-stu-id="a13ff-486">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="a13ff-487">**Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-487">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="a13ff-488">Sélectionnez **Ajouter**</span><span class="sxs-lookup"><span data-stu-id="a13ff-488">Select **Add**</span></span>

![Boîte de dialogue Ajouter un contrôleur](adding-model/_static/add_controller2.png)

<span data-ttu-id="a13ff-490">Visual Studio crée :</span><span class="sxs-lookup"><span data-stu-id="a13ff-490">Visual Studio creates:</span></span>

* <span data-ttu-id="a13ff-491">Une [classe de contexte de base de données](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="a13ff-491">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="a13ff-492">Un contrôleur de films (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="a13ff-492">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="a13ff-493">Razor afficher des fichiers pour les pages Create, Delete, Details, Edit et index (*views/movies/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="a13ff-493">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="a13ff-494">La création automatique du contexte de base de données et de méthodes d’action et de vues [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (créer, lire, mettre à jour et supprimer) porte le nom de *génération de modèles automatique*.</span><span class="sxs-lookup"><span data-stu-id="a13ff-494">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a13ff-495">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a13ff-495">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="a13ff-496">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="a13ff-496">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="a13ff-497">Installez l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="a13ff-497">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="a13ff-498">Sur Linux, exportez le chemin de l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="a13ff-498">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="a13ff-499">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a13ff-499">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-500">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-500">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a13ff-501">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="a13ff-501">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="a13ff-502">Installez l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="a13ff-502">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="a13ff-503">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a13ff-503">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="a13ff-504">Si vous exécutez l’application et que vous cliquez sur le lien **Mvc Movie**, vous recevez une erreur semblable à la suivante :</span><span class="sxs-lookup"><span data-stu-id="a13ff-504">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-505">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-506">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-506">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="a13ff-507">Vous devez créer la base de données, et vous utilisez pour cela la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core.</span><span class="sxs-lookup"><span data-stu-id="a13ff-507">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="a13ff-508">Les migrations permettent de créer une base de données qui correspond à votre modèle de données, et de mettre à jour le schéma de base de données quand votre modèle de données change.</span><span class="sxs-lookup"><span data-stu-id="a13ff-508">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="a13ff-509">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="a13ff-509">Initial migration</span></span>

<span data-ttu-id="a13ff-510">Dans cette section, vous devez effectuer les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="a13ff-510">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="a13ff-511">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="a13ff-511">Add an initial migration.</span></span>
* <span data-ttu-id="a13ff-512">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="a13ff-512">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-513">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-513">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a13ff-514">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="a13ff-514">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu Console du Gestionnaire de package](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="a13ff-516">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a13ff-516">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="a13ff-517">La commande `Add-Migration` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="a13ff-517">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="a13ff-518">Le schéma de base de données est basé sur le modèle spécifié dans la classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-518">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="a13ff-519">L’argument `Initial` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="a13ff-519">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="a13ff-520">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est utilisé.</span><span class="sxs-lookup"><span data-stu-id="a13ff-520">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="a13ff-521">Pour plus d'informations, consultez <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="a13ff-521">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="a13ff-522">La `Update-Database` commande exécute la `Up` méthode dans le fichier *migrations/{Time-horodatage} _InitialCreate. cs* , qui crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-522">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-523">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-523">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="a13ff-524">La commande `ef migrations add InitialCreate` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="a13ff-524">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="a13ff-525">Le schéma de base de donénes est basé sur le modèle spécifié dans la classe `MvcMovieContext` (dans *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="a13ff-525">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="a13ff-526">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="a13ff-526">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="a13ff-527">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="a13ff-527">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="a13ff-528">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="a13ff-528">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="a13ff-529">ASP.NET Core comprend [l’injection de dépendances (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a13ff-529">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a13ff-530">Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="a13ff-530">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="a13ff-531">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-531">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="a13ff-532">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="a13ff-532">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a13ff-533">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a13ff-533">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a13ff-534">L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="a13ff-534">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="a13ff-535">Examinez la méthode `Startup.ConfigureServices` suivante.</span><span class="sxs-lookup"><span data-stu-id="a13ff-535">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a13ff-536">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="a13ff-536">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="a13ff-537">`MvcMovieContext` coordonne les fonctionnalités d’EF Core (Create, Read, Update, Delete, etc.) pour le modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-537">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="a13ff-538">Le contexte de données (`MvcMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="a13ff-538">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="a13ff-539">Il spécifie les entités qui sont incluses dans le modèle de données :</span><span class="sxs-lookup"><span data-stu-id="a13ff-539">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="a13ff-540">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="a13ff-540">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="a13ff-541">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-541">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="a13ff-542">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="a13ff-542">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="a13ff-543">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="a13ff-543">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="a13ff-544">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="a13ff-544">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a13ff-545">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a13ff-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="a13ff-546">Vous avez créé un contexte de base de données et vous l’avez inscrit dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="a13ff-546">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="a13ff-547">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="a13ff-547">Test the app</span></span>

* <span data-ttu-id="a13ff-548">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="a13ff-548">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="a13ff-549">Si vous obtenez une exception de base de données similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="a13ff-549">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="a13ff-550">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="a13ff-550">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="a13ff-551">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-551">Test the **Create** link.</span></span> <span data-ttu-id="a13ff-552">Entrez et envoyez des données.</span><span class="sxs-lookup"><span data-stu-id="a13ff-552">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="a13ff-553">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-553">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a13ff-554">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="a13ff-554">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="a13ff-555">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="a13ff-555">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="a13ff-556">Testez les liens **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="a13ff-556">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="a13ff-557">Examiner la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="a13ff-557">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="a13ff-558">Le code précédent mis en surbrillance montre le contexte de la base de données des films qui est ajouté au conteneur [d’injection de dépendance](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="a13ff-558">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="a13ff-559">`services.AddDbContext<MvcMovieContext>(options =>` spécifie la base de données à utiliser et la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="a13ff-559">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="a13ff-560">`=>` est un [opérateur lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="a13ff-560">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="a13ff-561">Ouvrez le fichier *Controllers/MoviesController.cs* et examinez le constructeur :</span><span class="sxs-lookup"><span data-stu-id="a13ff-561">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="a13ff-562">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-562">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="a13ff-563">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a13ff-563">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="a13ff-564">Modèles fortement typés et mot clé @model</span><span class="sxs-lookup"><span data-stu-id="a13ff-564">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="a13ff-565">Plus tôt dans ce didacticiel, vous avez vu comment un contrôleur peut passer des données ou des objets à une vue en utilisant le dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-565">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="a13ff-566">Le dictionnaire `ViewData` est un objet dynamique qui fournit un moyen pratique d’effectuer une liaison tardive pour passer des informations à une vue.</span><span class="sxs-lookup"><span data-stu-id="a13ff-566">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="a13ff-567">Le modèle MVC fournit également la possibilité de passer des objets de modèle fortement typés à une vue.</span><span class="sxs-lookup"><span data-stu-id="a13ff-567">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="a13ff-568">Cette approche fortement typée permet une meilleure vérification de votre code au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="a13ff-568">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="a13ff-569">Le mécanisme de génération de modèles automatique a utilisé cette approche (c’est-à-dire passer un modèle fortement typé) avec la classe `MoviesController` et les vues quand il a créé les méthodes et les vues.</span><span class="sxs-lookup"><span data-stu-id="a13ff-569">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="a13ff-570">Examinez la méthode `Details` générée dans le fichier *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="a13ff-570">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="a13ff-571">Le paramètre `id` est généralement passé en tant que données de routage.</span><span class="sxs-lookup"><span data-stu-id="a13ff-571">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="a13ff-572">Par exemple, `https://localhost:5001/movies/details/1` définit :</span><span class="sxs-lookup"><span data-stu-id="a13ff-572">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="a13ff-573">Le contrôleur sur le contrôleur `movies` (le premier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="a13ff-573">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="a13ff-574">L’action sur `details` (le deuxième segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="a13ff-574">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="a13ff-575">L’ID sur 1 (le dernier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="a13ff-575">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="a13ff-576">Vous pouvez aussi passer `id` avec une requête de chaîne, comme suit :</span><span class="sxs-lookup"><span data-stu-id="a13ff-576">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="a13ff-577">Le `id` paramètre est défini en tant que [type Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) au cas où une valeur d’ID n’est pas fournie.</span><span class="sxs-lookup"><span data-stu-id="a13ff-577">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="a13ff-578">Une [expression lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) est passée à `FirstOrDefaultAsync` pour sélectionner les entités de film qui correspondent aux données de routage ou à la valeur de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="a13ff-578">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="a13ff-579">Si un film est trouvé, une instance du modèle `Movie` est passée à la vue `Details` :</span><span class="sxs-lookup"><span data-stu-id="a13ff-579">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="a13ff-580">Examinez le contenu du fichier *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a13ff-580">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="a13ff-581">En incluant une instruction `@model` en haut du fichier de la vue, vous pouvez spécifier le type d’objet attendu par la vue.</span><span class="sxs-lookup"><span data-stu-id="a13ff-581">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="a13ff-582">Quand vous avez créé le contrôleur pour les films, l’instruction `@model` suivante a été incluse automatiquement en haut du fichier *Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a13ff-582">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="a13ff-583">Cette directive `@model` vous permet d’accéder au film que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="a13ff-583">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="a13ff-584">Par exemple, dans la vue *Details.cshtml*, le code passe chaque champ du film aux Helpers HTML `DisplayNameFor` et `DisplayFor` avec l’objet `Model` fortement typé.</span><span class="sxs-lookup"><span data-stu-id="a13ff-584">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="a13ff-585">Les méthodes et les vues `Create` et `Edit` passent aussi un objet du modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-585">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="a13ff-586">Examinez la vue *Index.cshtml* et la méthode `Index` dans le contrôleur Movies.</span><span class="sxs-lookup"><span data-stu-id="a13ff-586">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="a13ff-587">Notez comment le code crée un objet `List` quand il appelle la méthode `View`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-587">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="a13ff-588">Le code passe cette liste `Movies` de la méthode d’action `Index` à la vue :</span><span class="sxs-lookup"><span data-stu-id="a13ff-588">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="a13ff-589">Quand vous avez créé le contrôleur pour les films, la génération de modèles automatique a inclus automatiquement l’instruction `@model` suivante en haut du fichier *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a13ff-589">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="a13ff-590">La directive `@model` vous permet d’accéder à la liste des films que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="a13ff-590">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="a13ff-591">Par exemple, dans la vue *Index.cshtml*, le code boucle dans les films avec une instruction `foreach` sur l’objet `Model` fortement typé :</span><span class="sxs-lookup"><span data-stu-id="a13ff-591">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="a13ff-592">Comme l’objet `Model` est fortement typé (en tant qu’objet `IEnumerable<Movie>`), chaque élément de la boucle est typé en tant que `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a13ff-592">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="a13ff-593">Entre autres avantages, cela signifie que votre code est vérifié au moment de la compilation :</span><span class="sxs-lookup"><span data-stu-id="a13ff-593">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a13ff-594">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a13ff-594">Additional resources</span></span>

* [<span data-ttu-id="a13ff-595">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="a13ff-595">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="a13ff-596">Globalisation et localisation</span><span class="sxs-lookup"><span data-stu-id="a13ff-596">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="a13ff-597">[Ajout d’une vue précédente](adding-view.md) 
>  [Utilisation d’une base de données](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="a13ff-597">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
