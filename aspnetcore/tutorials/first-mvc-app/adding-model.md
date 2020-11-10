---
title: 'Partie 4 : ajouter un modèle à une application ASP.NET Core MVC'
author: rick-anderson
description: Partie 4 de la série de didacticiels sur ASP.NET Core MVC.
ms.author: riande
ms.date: 01/13/2020
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: fa1d79bed56f17afe69697a7e24ec200e6a0ab22
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422732"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="67e26-103">Partie 4 : ajouter un modèle à une application ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="67e26-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="67e26-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Ryan Nowak](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="67e26-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="67e26-105">Dans cette section, vous allez ajouter des classes pour la gestion des films dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="67e26-106">Ces classes constituent la partie « **M** odèle » de l’application **M** VC.</span><span class="sxs-lookup"><span data-stu-id="67e26-106">These classes will be the " **M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="67e26-107">Vous utilisez ces classes avec [Entity Framework Core](/ef/core) (EF Core) pour travailler avec une base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="67e26-108">EF Core est un framework de mappage relationnel d’objets qui simplifie le code d’accès aux données à écrire.</span><span class="sxs-lookup"><span data-stu-id="67e26-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="67e26-109">Les classes de modèle que vous créez portent le nom de classes OCT (« **O** bjet **C** LR **T** raditionnel ») **,** car elles n’ont pas de dépendances envers EF Core.</span><span class="sxs-lookup"><span data-stu-id="67e26-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="67e26-110">Elles définissent simplement les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="67e26-111">Dans ce didacticiel, vous écrivez d’abord les classes du modèle, puis EF Core crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="67e26-112">Ajouter une classe de modèle de données</span><span class="sxs-lookup"><span data-stu-id="67e26-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67e26-114">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="67e26-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="67e26-115">Nommez le fichier *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="67e26-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="67e26-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67e26-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="67e26-117">Ajoutez un fichier nommé *Movie.cs* au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="67e26-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="67e26-118">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="67e26-119">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** une classe  >  **New Class**  >  **vide** Class.</span><span class="sxs-lookup"><span data-stu-id="67e26-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="67e26-120">Nommez le fichier *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="67e26-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="67e26-121">Mettez le fichier *Movie.cs* à jour avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="67e26-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="67e26-122">La classe `Movie` contient un champ `Id`, qui est nécessaire à la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="67e26-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="67e26-123">L' <xref:System.ComponentModel.DataAnnotations.DataType> attribut sur `ReleaseDate` spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="67e26-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="67e26-124">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="67e26-124">With this attribute:</span></span>

* <span data-ttu-id="67e26-125">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="67e26-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="67e26-126">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="67e26-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="67e26-127">Les [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="67e26-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="67e26-128">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="67e26-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67e26-130">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="67e26-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu Console du Gestionnaire de package](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="67e26-132">Dans la console du gestionnaire de package, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="67e26-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="67e26-133">La commande précédente ajoute le fournisseur EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="67e26-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="67e26-134">Le package du fournisseur installe le package EF Core en tant que dépendance.</span><span class="sxs-lookup"><span data-stu-id="67e26-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="67e26-135">Plus loin dans ce tutoriel, d’autres packages sont installés automatiquement lors de l’étape de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="67e26-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="67e26-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67e26-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="67e26-137">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="67e26-138">Dans le menu **projet** , sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="67e26-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="67e26-139">Dans le champ de **recherche** en haut à droite, entrez `Microsoft.EntityFrameworkCore.SQLite` et appuyez sur la touche **retour** pour effectuer la recherche.</span><span class="sxs-lookup"><span data-stu-id="67e26-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="67e26-140">Sélectionnez le package NuGet correspondant, puis cliquez sur le bouton **Ajouter un package** .</span><span class="sxs-lookup"><span data-stu-id="67e26-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Ajouter Entity Framework Core package NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="67e26-142">La boîte de dialogue **Sélectionner les projets** s’affiche, avec le `MvcMovie` projet sélectionné.</span><span class="sxs-lookup"><span data-stu-id="67e26-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="67e26-143">Appuyez sur le bouton **OK** .</span><span class="sxs-lookup"><span data-stu-id="67e26-143">Press the **Ok** button.</span></span>

<span data-ttu-id="67e26-144">Une boîte de dialogue d' **acceptation de licence** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="67e26-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="67e26-145">Passez en revue les licences comme vous le souhaitez, puis cliquez sur le bouton **accepter** .</span><span class="sxs-lookup"><span data-stu-id="67e26-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="67e26-146">Répétez les étapes ci-dessus pour installer les packages NuGet suivants :</span><span class="sxs-lookup"><span data-stu-id="67e26-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="67e26-147">Créer une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="67e26-147">Create a database context class</span></span>

<span data-ttu-id="67e26-148">Une classe de contexte de base de données est nécessaire pour coordonner les fonctionnalités d’EF Core (créer, lire, mettre à jour, supprimer) pour le modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="67e26-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="67e26-149">Le contexte de base de données est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) et il spécifie les entités à inclure dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="67e26-150">Créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="67e26-150">Create a *Data* folder.</span></span>

<span data-ttu-id="67e26-151">Ajoutez un fichier *Data/MvcMovieContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="67e26-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="67e26-152">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="67e26-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="67e26-153">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="67e26-154">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="67e26-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="67e26-155">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="67e26-155">Register the database context</span></span>

<span data-ttu-id="67e26-156">ASP.NET Core comprend [l’injection de dépendances (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="67e26-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="67e26-157">Les services (tels que le contexte de base de données EF Core) doivent être inscrits auprès de l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="67e26-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="67e26-158">Ces services sont fournis par les composants qui requièrent ces services (tels que les :::no-loc(Razor)::: pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="67e26-158">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="67e26-159">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="67e26-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="67e26-160">Dans cette section, vous allez inscrire le contexte de base de données auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="67e26-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="67e26-161">En tête du fichier *Startup.cs* , ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="67e26-161">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="67e26-162">Ajoutez le code en surbrillance suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67e26-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-164">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="67e26-165">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="67e26-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="67e26-166">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *:::no-loc(appsettings.json):::* fichier.</span><span class="sxs-lookup"><span data-stu-id="67e26-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="67e26-167">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="67e26-167">Add a database connection string</span></span>

<span data-ttu-id="67e26-168">Ajoutez une chaîne de connexion au *:::no-loc(appsettings.json):::* fichier :</span><span class="sxs-lookup"><span data-stu-id="67e26-168">Add a connection string to the *:::no-loc(appsettings.json):::* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-170">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="67e26-171">Générez le projet en tant que vérification des erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="67e26-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="67e26-172">Générer automatiquement des pages de film</span><span class="sxs-lookup"><span data-stu-id="67e26-172">Scaffold movie pages</span></span>

<span data-ttu-id="67e26-173">Utilisez l’outil de génération de modèles automatique pour créer, lire, mettre à jour et supprimer des pages du modèle de film.</span><span class="sxs-lookup"><span data-stu-id="67e26-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67e26-175">Dans **l’Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Contrôleurs* , puis choisissez **Ajouter > Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="67e26-175">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_controller21.png)

<span data-ttu-id="67e26-177">Dans la boîte de dialogue **Ajouter un modèle automatique** , sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="67e26-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="67e26-179">Renseignez la boîte de dialogue **Ajouter un contrôleur**  :</span><span class="sxs-lookup"><span data-stu-id="67e26-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="67e26-180">**Classe de modèle :** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="67e26-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="67e26-181">**Classe de contexte de données :** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="67e26-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Ajouter un contexte de données](adding-model/_static/dc5.png)

* <span data-ttu-id="67e26-183">**Affichages :** conservez la valeur par défaut de chaque option activée.</span><span class="sxs-lookup"><span data-stu-id="67e26-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="67e26-184">**Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="67e26-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="67e26-185">Sélectionnez **Ajouter**</span><span class="sxs-lookup"><span data-stu-id="67e26-185">Select **Add**</span></span>

<span data-ttu-id="67e26-186">Visual Studio crée :</span><span class="sxs-lookup"><span data-stu-id="67e26-186">Visual Studio creates:</span></span>

* <span data-ttu-id="67e26-187">Un contrôleur de films ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="67e26-187">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="67e26-188">:::no-loc(Razor)::: afficher des fichiers pour les pages Create, Delete, Details, Edit et index ( *views/movies/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="67e26-188">:::no-loc(Razor)::: view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="67e26-189">La création automatique de ces fichiers est appelée *génération de modèles automatique*.</span><span class="sxs-lookup"><span data-stu-id="67e26-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="67e26-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67e26-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="67e26-191">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="67e26-191">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="67e26-192">Sur Linux, exportez le chemin de l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="67e26-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="67e26-193">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="67e26-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="67e26-194">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="67e26-195">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="67e26-195">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="67e26-196">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="67e26-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="67e26-197">Vous ne pouvez pas encore utiliser les pages générées automatiquement, car la base de données n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="67e26-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="67e26-198">Si vous exécutez l’application et cliquez sur le lien de l' **application de film** , vous recevez un message d’erreur Impossible d' *ouvrir la base de données* ou une *table de ce type :* message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="67e26-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="67e26-199">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="67e26-199">Initial migration</span></span>

<span data-ttu-id="67e26-200">Utilisez la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="67e26-201">La fonctionnalité Migrations est un ensemble d’outils qui vous permettent de créer et de mettre à jour une base de données pour qu’elle corresponde à votre modèle de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67e26-203">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="67e26-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="67e26-204">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="67e26-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="67e26-205">`Add-Migration InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="67e26-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="67e26-206">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="67e26-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="67e26-207">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="67e26-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="67e26-208">Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="67e26-209">Le schéma de base de données est basé sur le modèle spécifié dans la classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="67e26-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="67e26-210">`Update-Database`: Met à jour la base de données avec la dernière migration, créée par la commande précédente.</span><span class="sxs-lookup"><span data-stu-id="67e26-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="67e26-211">La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs* , ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="67e26-212">La commande « database update » génère l’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="67e26-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="67e26-213">Aucun type n’a été spécifié pour la colonne décimale 'Price' sur le type d’entité 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="67e26-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="67e26-214">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="67e26-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="67e26-215">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant ’HasColumnType()’.</span><span class="sxs-lookup"><span data-stu-id="67e26-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="67e26-216">Vous pouvez ignorer cet avertissement, il sera corrigé dans un prochain tutoriel.</span><span class="sxs-lookup"><span data-stu-id="67e26-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-217">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="67e26-218">Exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="67e26-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="67e26-219">`ef migrations add InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="67e26-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="67e26-220">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="67e26-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="67e26-221">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="67e26-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="67e26-222">Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="67e26-223">Le schéma de base de donénes est basé sur le modèle spécifié dans la classe `MvcMovieContext` (dans *Data/MvcMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="67e26-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="67e26-224">`ef database update`: Met à jour la base de données avec la dernière migration, créée par la commande précédente.</span><span class="sxs-lookup"><span data-stu-id="67e26-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="67e26-225">La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs* , ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="67e26-226">La classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="67e26-226">The InitialCreate class</span></span>

<span data-ttu-id="67e26-227">Examinez le fichier de migration *Migrations/{horodatage}_InitialCreate.cs*  :</span><span class="sxs-lookup"><span data-stu-id="67e26-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="67e26-228">La méthode `Up` crée la table Movie et configure `Id` comme la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="67e26-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="67e26-229">La méthode `Down` rétablit les modifications de schéma provoquées par la migration `Up`.</span><span class="sxs-lookup"><span data-stu-id="67e26-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="67e26-230">Tester l'application</span><span class="sxs-lookup"><span data-stu-id="67e26-230">Test the app</span></span>

* <span data-ttu-id="67e26-231">Exécutez l’application et cliquez sur le lien **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="67e26-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="67e26-232">Si vous obtenez une exception similaire à celle-ci :</span><span class="sxs-lookup"><span data-stu-id="67e26-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-234">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="67e26-235">Il est probable que vous n’ayez pas effectué l’[étape de migration](#migration).</span><span class="sxs-lookup"><span data-stu-id="67e26-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="67e26-236">Testez la page **Create**.</span><span class="sxs-lookup"><span data-stu-id="67e26-236">Test the **Create** page.</span></span> <span data-ttu-id="67e26-237">Entrez et envoyez des données.</span><span class="sxs-lookup"><span data-stu-id="67e26-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="67e26-238">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="67e26-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="67e26-239">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="67e26-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="67e26-240">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="67e26-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="67e26-241">Testez les pages **Edit** , **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="67e26-241">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="67e26-242">Injection de dépendances dans le contrôleur</span><span class="sxs-lookup"><span data-stu-id="67e26-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67e26-244">Ouvrez le fichier *Controllers/MoviesController.cs* et examinez le constructeur :</span><span class="sxs-lookup"><span data-stu-id="67e26-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="67e26-245">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="67e26-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="67e26-246">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="67e26-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-247">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="67e26-248">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="67e26-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="67e26-249">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="67e26-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="67e26-250">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="67e26-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="67e26-251">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="67e26-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="67e26-252">Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage.</span><span class="sxs-lookup"><span data-stu-id="67e26-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="67e26-253">`IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="67e26-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="67e26-254">Modèles fortement typés et mot clé @model</span><span class="sxs-lookup"><span data-stu-id="67e26-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="67e26-255">Plus tôt dans ce didacticiel, vous avez vu comment un contrôleur peut passer des données ou des objets à une vue en utilisant le dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="67e26-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="67e26-256">Le dictionnaire `ViewData` est un objet dynamique qui fournit un moyen pratique d’effectuer une liaison tardive pour passer des informations à une vue.</span><span class="sxs-lookup"><span data-stu-id="67e26-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="67e26-257">Le modèle MVC fournit également la possibilité de passer des objets de modèle fortement typés à une vue.</span><span class="sxs-lookup"><span data-stu-id="67e26-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="67e26-258">Cette approche fortement typée permet de vérifier votre code au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="67e26-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="67e26-259">Le mécanisme de génération de modèles automatique a utilisé cette approche (c’est-à-dire passer un modèle fortement typé) avec la classe `MoviesController` et des vues.</span><span class="sxs-lookup"><span data-stu-id="67e26-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="67e26-260">Examinez la méthode `Details` générée dans le fichier *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="67e26-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="67e26-261">Le paramètre `id` est généralement passé en tant que données de routage.</span><span class="sxs-lookup"><span data-stu-id="67e26-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="67e26-262">Par exemple, `https://localhost:5001/movies/details/1` définit :</span><span class="sxs-lookup"><span data-stu-id="67e26-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="67e26-263">Le contrôleur sur le contrôleur `movies` (le premier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="67e26-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="67e26-264">L’action sur `details` (le deuxième segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="67e26-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="67e26-265">L’ID sur 1 (le dernier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="67e26-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="67e26-266">Vous pouvez aussi passer `id` avec une requête de chaîne, comme suit :</span><span class="sxs-lookup"><span data-stu-id="67e26-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="67e26-267">Le `id` paramètre est défini en tant que [type Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) au cas où une valeur d’ID n’est pas fournie.</span><span class="sxs-lookup"><span data-stu-id="67e26-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="67e26-268">Une [expression lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) est passée à `FirstOrDefaultAsync` pour sélectionner les entités de film qui correspondent aux données de routage ou à la valeur de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="67e26-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="67e26-269">Si un film est trouvé, une instance du modèle `Movie` est passée à la vue `Details` :</span><span class="sxs-lookup"><span data-stu-id="67e26-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="67e26-270">Examinez le contenu du fichier *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="67e26-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="67e26-271">L’instruction `@model` située en haut du fichier de la vue spécifie le type d’objet attendu par la vue.</span><span class="sxs-lookup"><span data-stu-id="67e26-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="67e26-272">Lorsque le contrôleur de film était créé, l’instruction `@model` suivante était incluse :</span><span class="sxs-lookup"><span data-stu-id="67e26-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="67e26-273">Cette directive `@model` autorise l’accès au film que le contrôleur a passé à la vue.</span><span class="sxs-lookup"><span data-stu-id="67e26-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="67e26-274">L’objet `Model` est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="67e26-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="67e26-275">Par exemple, dans la vue *Details.cshtml* , le code passe chaque champ du film aux Helpers HTML `DisplayNameFor` et `DisplayFor` avec l’objet `Model` fortement typé.</span><span class="sxs-lookup"><span data-stu-id="67e26-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="67e26-276">Les méthodes et les vues `Create` et `Edit` passent aussi un objet du modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="67e26-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="67e26-277">Examinez la vue *Index.cshtml* et la méthode `Index` dans le contrôleur Movies.</span><span class="sxs-lookup"><span data-stu-id="67e26-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="67e26-278">Notez comment le code crée un objet `List` quand il appelle la méthode `View`.</span><span class="sxs-lookup"><span data-stu-id="67e26-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="67e26-279">Le code passe cette liste `Movies` de la méthode d’action `Index` à la vue :</span><span class="sxs-lookup"><span data-stu-id="67e26-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="67e26-280">Lors de la création du contrôleur du film, la génération de modèles automatique a inclus l’instruction `@model` suivante en haut du fichier *Index.cshtml*  :</span><span class="sxs-lookup"><span data-stu-id="67e26-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="67e26-281">La directive `@model` vous permet d’accéder à la liste des films que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="67e26-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="67e26-282">Par exemple, dans la vue *Index.cshtml* , le code boucle dans les films avec une instruction `foreach` sur l’objet `Model` fortement typé :</span><span class="sxs-lookup"><span data-stu-id="67e26-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="67e26-283">Comme l’objet `Model` est fortement typé (en tant qu’objet `IEnumerable<Movie>`), chaque élément de la boucle est typé en tant que `Movie`.</span><span class="sxs-lookup"><span data-stu-id="67e26-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="67e26-284">Entre autres avantages, cela signifie que votre code est vérifié au moment de la compilation :</span><span class="sxs-lookup"><span data-stu-id="67e26-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="67e26-285">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="67e26-285">Additional resources</span></span>

* [<span data-ttu-id="67e26-286">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="67e26-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="67e26-287">Globalisation et localisation</span><span class="sxs-lookup"><span data-stu-id="67e26-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="67e26-288">[Ajout d’une vue précédente](adding-view.md) 
>  [Utilisation de SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="67e26-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="67e26-289">Ajouter une classe de modèle de données</span><span class="sxs-lookup"><span data-stu-id="67e26-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67e26-291">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="67e26-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="67e26-292">Nommez le fichier *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="67e26-292">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="67e26-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67e26-293">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="67e26-294">Ajoutez un fichier nommé *Movie.cs* au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="67e26-294">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="67e26-295">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="67e26-296">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** une classe  >  **New Class**  >  **vide** Class.</span><span class="sxs-lookup"><span data-stu-id="67e26-296">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="67e26-297">Nommez le fichier *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="67e26-297">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="67e26-298">Mettez le fichier *Movie.cs* à jour avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="67e26-298">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="67e26-299">La classe `Movie` contient un champ `Id`, qui est nécessaire à la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="67e26-299">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="67e26-300">L' <xref:System.ComponentModel.DataAnnotations.DataType> attribut sur `ReleaseDate` spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="67e26-300">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="67e26-301">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="67e26-301">With this attribute:</span></span>

* <span data-ttu-id="67e26-302">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="67e26-302">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="67e26-303">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="67e26-303">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="67e26-304">Les [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="67e26-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="67e26-305">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="67e26-305">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-306">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67e26-307">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="67e26-307">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu Console du Gestionnaire de package](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="67e26-309">Dans la console du gestionnaire de package, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="67e26-309">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="67e26-310">La commande précédente ajoute le fournisseur EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="67e26-310">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="67e26-311">Le package du fournisseur installe le package EF Core en tant que dépendance.</span><span class="sxs-lookup"><span data-stu-id="67e26-311">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="67e26-312">Plus loin dans ce tutoriel, d’autres packages sont installés automatiquement lors de l’étape de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="67e26-312">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="67e26-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67e26-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="67e26-314">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-314">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="67e26-315">Dans le menu **projet** , sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="67e26-315">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="67e26-316">Dans le champ de **recherche** en haut à droite, entrez `Microsoft.EntityFrameworkCore.SQLite` et appuyez sur la touche **retour** pour effectuer la recherche.</span><span class="sxs-lookup"><span data-stu-id="67e26-316">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="67e26-317">Sélectionnez le package NuGet correspondant, puis cliquez sur le bouton **Ajouter un package** .</span><span class="sxs-lookup"><span data-stu-id="67e26-317">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Ajouter Entity Framework Core package NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="67e26-319">La boîte de dialogue **Sélectionner les projets** s’affiche, avec le `MvcMovie` projet sélectionné.</span><span class="sxs-lookup"><span data-stu-id="67e26-319">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="67e26-320">Appuyez sur le bouton **OK** .</span><span class="sxs-lookup"><span data-stu-id="67e26-320">Press the **Ok** button.</span></span>

<span data-ttu-id="67e26-321">Une boîte de dialogue d' **acceptation de licence** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="67e26-321">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="67e26-322">Passez en revue les licences comme vous le souhaitez, puis cliquez sur le bouton **accepter** .</span><span class="sxs-lookup"><span data-stu-id="67e26-322">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="67e26-323">Répétez les étapes ci-dessus pour installer les packages NuGet suivants :</span><span class="sxs-lookup"><span data-stu-id="67e26-323">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="67e26-324">Créer une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="67e26-324">Create a database context class</span></span>

<span data-ttu-id="67e26-325">Une classe de contexte de base de données est nécessaire pour coordonner les fonctionnalités d’EF Core (créer, lire, mettre à jour, supprimer) pour le modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="67e26-325">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="67e26-326">Le contexte de base de données est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) et il spécifie les entités à inclure dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-326">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="67e26-327">Créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="67e26-327">Create a *Data* folder.</span></span>

<span data-ttu-id="67e26-328">Ajoutez un fichier *Data/MvcMovieContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="67e26-328">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="67e26-329">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="67e26-329">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="67e26-330">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-330">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="67e26-331">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="67e26-331">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="67e26-332">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="67e26-332">Register the database context</span></span>

<span data-ttu-id="67e26-333">ASP.NET Core comprend [l’injection de dépendances (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="67e26-333">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="67e26-334">Les services (tels que le contexte de base de données EF Core) doivent être inscrits auprès de l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="67e26-334">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="67e26-335">Ces services sont fournis par les composants qui requièrent ces services (tels que les :::no-loc(Razor)::: pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="67e26-335">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="67e26-336">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="67e26-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="67e26-337">Dans cette section, vous allez inscrire le contexte de base de données auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="67e26-337">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="67e26-338">En tête du fichier *Startup.cs* , ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="67e26-338">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="67e26-339">Ajoutez le code en surbrillance suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67e26-339">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-340">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-340">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-341">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-341">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="67e26-342">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="67e26-342">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="67e26-343">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *:::no-loc(appsettings.json):::* fichier.</span><span class="sxs-lookup"><span data-stu-id="67e26-343">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="67e26-344">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="67e26-344">Add a database connection string</span></span>

<span data-ttu-id="67e26-345">Ajoutez une chaîne de connexion au *:::no-loc(appsettings.json):::* fichier :</span><span class="sxs-lookup"><span data-stu-id="67e26-345">Add a connection string to the *:::no-loc(appsettings.json):::* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-346">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-346">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-347">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-347">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="67e26-348">Générez le projet en tant que vérification des erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="67e26-348">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="67e26-349">Générer automatiquement des pages de film</span><span class="sxs-lookup"><span data-stu-id="67e26-349">Scaffold movie pages</span></span>

<span data-ttu-id="67e26-350">Utilisez l’outil de génération de modèles automatique pour créer, lire, mettre à jour et supprimer des pages du modèle de film.</span><span class="sxs-lookup"><span data-stu-id="67e26-350">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-351">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-351">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67e26-352">Dans **l’Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Contrôleurs* , puis choisissez **Ajouter > Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="67e26-352">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_controller21.png)

<span data-ttu-id="67e26-354">Dans la boîte de dialogue **Ajouter un modèle automatique** , sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="67e26-354">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="67e26-356">Renseignez la boîte de dialogue **Ajouter un contrôleur**  :</span><span class="sxs-lookup"><span data-stu-id="67e26-356">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="67e26-357">**Classe de modèle :** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="67e26-357">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="67e26-358">**Classe de contexte de données :** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="67e26-358">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Ajouter un contexte de données](adding-model/_static/dc3.png)

* <span data-ttu-id="67e26-360">**Affichages :** conservez la valeur par défaut de chaque option activée.</span><span class="sxs-lookup"><span data-stu-id="67e26-360">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="67e26-361">**Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="67e26-361">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="67e26-362">Sélectionnez **Ajouter**</span><span class="sxs-lookup"><span data-stu-id="67e26-362">Select **Add**</span></span>

<span data-ttu-id="67e26-363">Visual Studio crée :</span><span class="sxs-lookup"><span data-stu-id="67e26-363">Visual Studio creates:</span></span>

* <span data-ttu-id="67e26-364">Un contrôleur de films ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="67e26-364">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="67e26-365">:::no-loc(Razor)::: afficher des fichiers pour les pages Create, Delete, Details, Edit et index ( *views/movies/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="67e26-365">:::no-loc(Razor)::: view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="67e26-366">La création automatique de ces fichiers est appelée *génération de modèles automatique*.</span><span class="sxs-lookup"><span data-stu-id="67e26-366">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="67e26-367">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67e26-367">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="67e26-368">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="67e26-368">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="67e26-369">Sur Linux, exportez le chemin de l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="67e26-369">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="67e26-370">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="67e26-370">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="67e26-371">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-371">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="67e26-372">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="67e26-372">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="67e26-373">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="67e26-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="67e26-374">Vous ne pouvez pas encore utiliser les pages générées automatiquement, car la base de données n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="67e26-374">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="67e26-375">Si vous exécutez l’application et cliquez sur le lien de l' **application de film** , vous recevez un message d’erreur Impossible d' *ouvrir la base de données* ou une *table de ce type :* message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="67e26-375">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="67e26-376">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="67e26-376">Initial migration</span></span>

<span data-ttu-id="67e26-377">Utilisez la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-377">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="67e26-378">La fonctionnalité Migrations est un ensemble d’outils qui vous permettent de créer et de mettre à jour une base de données pour qu’elle corresponde à votre modèle de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-378">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-379">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67e26-380">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="67e26-380">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="67e26-381">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="67e26-381">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="67e26-382">`Add-Migration InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="67e26-382">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="67e26-383">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="67e26-383">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="67e26-384">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="67e26-384">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="67e26-385">Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-385">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="67e26-386">Le schéma de base de données est basé sur le modèle spécifié dans la classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="67e26-386">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="67e26-387">`Update-Database`: Met à jour la base de données avec la dernière migration, créée par la commande précédente.</span><span class="sxs-lookup"><span data-stu-id="67e26-387">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="67e26-388">La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs* , ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-388">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="67e26-389">La commande « database update » génère l’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="67e26-389">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="67e26-390">Aucun type n’a été spécifié pour la colonne décimale 'Price' sur le type d’entité 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="67e26-390">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="67e26-391">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="67e26-391">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="67e26-392">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant ’HasColumnType()’.</span><span class="sxs-lookup"><span data-stu-id="67e26-392">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="67e26-393">Vous pouvez ignorer cet avertissement, il sera corrigé dans un prochain tutoriel.</span><span class="sxs-lookup"><span data-stu-id="67e26-393">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-394">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-394">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="67e26-395">Exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="67e26-395">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="67e26-396">`ef migrations add InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="67e26-396">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="67e26-397">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="67e26-397">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="67e26-398">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="67e26-398">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="67e26-399">Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-399">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="67e26-400">Le schéma de base de donénes est basé sur le modèle spécifié dans la classe `MvcMovieContext` (dans *Data/MvcMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="67e26-400">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="67e26-401">`ef database update`: Met à jour la base de données avec la dernière migration, créée par la commande précédente.</span><span class="sxs-lookup"><span data-stu-id="67e26-401">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="67e26-402">La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs* , ce qui entraîne la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-402">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="67e26-403">La classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="67e26-403">The InitialCreate class</span></span>

<span data-ttu-id="67e26-404">Examinez le fichier de migration *Migrations/{horodatage}_InitialCreate.cs*  :</span><span class="sxs-lookup"><span data-stu-id="67e26-404">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="67e26-405">La méthode `Up` crée la table Movie et configure `Id` comme la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="67e26-405">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="67e26-406">La méthode `Down` rétablit les modifications de schéma provoquées par la migration `Up`.</span><span class="sxs-lookup"><span data-stu-id="67e26-406">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="67e26-407">Tester l'application</span><span class="sxs-lookup"><span data-stu-id="67e26-407">Test the app</span></span>

* <span data-ttu-id="67e26-408">Exécutez l’application et cliquez sur le lien **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="67e26-408">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="67e26-409">Si vous obtenez une exception similaire à celle-ci :</span><span class="sxs-lookup"><span data-stu-id="67e26-409">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-410">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-410">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-411">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-411">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="67e26-412">Il est probable que vous n’ayez pas effectué l’[étape de migration](#migration).</span><span class="sxs-lookup"><span data-stu-id="67e26-412">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="67e26-413">Testez la page **Create**.</span><span class="sxs-lookup"><span data-stu-id="67e26-413">Test the **Create** page.</span></span> <span data-ttu-id="67e26-414">Entrez et envoyez des données.</span><span class="sxs-lookup"><span data-stu-id="67e26-414">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="67e26-415">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="67e26-415">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="67e26-416">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="67e26-416">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="67e26-417">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="67e26-417">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="67e26-418">Testez les pages **Edit** , **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="67e26-418">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="67e26-419">Injection de dépendances dans le contrôleur</span><span class="sxs-lookup"><span data-stu-id="67e26-419">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-420">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67e26-421">Ouvrez le fichier *Controllers/MoviesController.cs* et examinez le constructeur :</span><span class="sxs-lookup"><span data-stu-id="67e26-421">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="67e26-422">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="67e26-422">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="67e26-423">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="67e26-423">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-424">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-424">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="67e26-425">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="67e26-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="67e26-426">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="67e26-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="67e26-427">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="67e26-427">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="67e26-428">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="67e26-428">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="67e26-429">Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage.</span><span class="sxs-lookup"><span data-stu-id="67e26-429">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="67e26-430">`IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="67e26-430">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="67e26-431">Modèles fortement typés et mot clé @model</span><span class="sxs-lookup"><span data-stu-id="67e26-431">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="67e26-432">Plus tôt dans ce didacticiel, vous avez vu comment un contrôleur peut passer des données ou des objets à une vue en utilisant le dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="67e26-432">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="67e26-433">Le dictionnaire `ViewData` est un objet dynamique qui fournit un moyen pratique d’effectuer une liaison tardive pour passer des informations à une vue.</span><span class="sxs-lookup"><span data-stu-id="67e26-433">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="67e26-434">Le modèle MVC fournit également la possibilité de passer des objets de modèle fortement typés à une vue.</span><span class="sxs-lookup"><span data-stu-id="67e26-434">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="67e26-435">Cette approche fortement typée permet de vérifier votre code au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="67e26-435">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="67e26-436">Le mécanisme de génération de modèles automatique a utilisé cette approche (c’est-à-dire passer un modèle fortement typé) avec la classe `MoviesController` et des vues.</span><span class="sxs-lookup"><span data-stu-id="67e26-436">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="67e26-437">Examinez la méthode `Details` générée dans le fichier *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="67e26-437">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="67e26-438">Le paramètre `id` est généralement passé en tant que données de routage.</span><span class="sxs-lookup"><span data-stu-id="67e26-438">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="67e26-439">Par exemple, `https://localhost:5001/movies/details/1` définit :</span><span class="sxs-lookup"><span data-stu-id="67e26-439">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="67e26-440">Le contrôleur sur le contrôleur `movies` (le premier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="67e26-440">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="67e26-441">L’action sur `details` (le deuxième segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="67e26-441">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="67e26-442">L’ID sur 1 (le dernier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="67e26-442">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="67e26-443">Vous pouvez aussi passer `id` avec une requête de chaîne, comme suit :</span><span class="sxs-lookup"><span data-stu-id="67e26-443">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="67e26-444">Le `id` paramètre est défini en tant que [type Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) au cas où une valeur d’ID n’est pas fournie.</span><span class="sxs-lookup"><span data-stu-id="67e26-444">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="67e26-445">Une [expression lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) est passée à `FirstOrDefaultAsync` pour sélectionner les entités de film qui correspondent aux données de routage ou à la valeur de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="67e26-445">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="67e26-446">Si un film est trouvé, une instance du modèle `Movie` est passée à la vue `Details` :</span><span class="sxs-lookup"><span data-stu-id="67e26-446">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="67e26-447">Examinez le contenu du fichier *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="67e26-447">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="67e26-448">L’instruction `@model` située en haut du fichier de la vue spécifie le type d’objet attendu par la vue.</span><span class="sxs-lookup"><span data-stu-id="67e26-448">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="67e26-449">Lorsque le contrôleur de film était créé, l’instruction `@model` suivante était incluse :</span><span class="sxs-lookup"><span data-stu-id="67e26-449">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="67e26-450">Cette directive `@model` autorise l’accès au film que le contrôleur a passé à la vue.</span><span class="sxs-lookup"><span data-stu-id="67e26-450">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="67e26-451">L’objet `Model` est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="67e26-451">The `Model` object is strongly typed.</span></span> <span data-ttu-id="67e26-452">Par exemple, dans la vue *Details.cshtml* , le code passe chaque champ du film aux Helpers HTML `DisplayNameFor` et `DisplayFor` avec l’objet `Model` fortement typé.</span><span class="sxs-lookup"><span data-stu-id="67e26-452">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="67e26-453">Les méthodes et les vues `Create` et `Edit` passent aussi un objet du modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="67e26-453">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="67e26-454">Examinez la vue *Index.cshtml* et la méthode `Index` dans le contrôleur Movies.</span><span class="sxs-lookup"><span data-stu-id="67e26-454">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="67e26-455">Notez comment le code crée un objet `List` quand il appelle la méthode `View`.</span><span class="sxs-lookup"><span data-stu-id="67e26-455">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="67e26-456">Le code passe cette liste `Movies` de la méthode d’action `Index` à la vue :</span><span class="sxs-lookup"><span data-stu-id="67e26-456">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="67e26-457">Lors de la création du contrôleur du film, la génération de modèles automatique a inclus l’instruction `@model` suivante en haut du fichier *Index.cshtml*  :</span><span class="sxs-lookup"><span data-stu-id="67e26-457">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="67e26-458">La directive `@model` vous permet d’accéder à la liste des films que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="67e26-458">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="67e26-459">Par exemple, dans la vue *Index.cshtml* , le code boucle dans les films avec une instruction `foreach` sur l’objet `Model` fortement typé :</span><span class="sxs-lookup"><span data-stu-id="67e26-459">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="67e26-460">Comme l’objet `Model` est fortement typé (en tant qu’objet `IEnumerable<Movie>`), chaque élément de la boucle est typé en tant que `Movie`.</span><span class="sxs-lookup"><span data-stu-id="67e26-460">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="67e26-461">Entre autres avantages, cela signifie que votre code est vérifié au moment de la compilation :</span><span class="sxs-lookup"><span data-stu-id="67e26-461">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="67e26-462">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="67e26-462">Additional resources</span></span>

* [<span data-ttu-id="67e26-463">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="67e26-463">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="67e26-464">Globalisation et localisation</span><span class="sxs-lookup"><span data-stu-id="67e26-464">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="67e26-465">[Ajout d’une vue précédente](adding-view.md) 
>  [Utilisation de SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="67e26-465">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="67e26-466">Ajouter une classe de modèle de données</span><span class="sxs-lookup"><span data-stu-id="67e26-466">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67e26-468">Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="67e26-468">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="67e26-469">Nommez la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="67e26-469">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-470">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="67e26-471">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="67e26-471">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="67e26-472">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="67e26-472">Scaffold the movie model</span></span>

<span data-ttu-id="67e26-473">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="67e26-473">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="67e26-474">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="67e26-474">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-475">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-475">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67e26-476">Dans **l’Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Contrôleurs* , puis choisissez **Ajouter > Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="67e26-476">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![affichage de l’étape ci-dessus](adding-model/_static/add_controller21.png)

<span data-ttu-id="67e26-478">Dans la boîte de dialogue **Ajouter un modèle automatique** , sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="67e26-478">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="67e26-480">Renseignez la boîte de dialogue **Ajouter un contrôleur**  :</span><span class="sxs-lookup"><span data-stu-id="67e26-480">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="67e26-481">**Classe de modèle :** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="67e26-481">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="67e26-482">**Classe de contexte de données :** sélectionnez l’icône **+** et ajoutez le **MvcMovie.Models.MvcMovieContext** par défaut.</span><span class="sxs-lookup"><span data-stu-id="67e26-482">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Ajouter un contexte de données](adding-model/_static/dc.png)

* <span data-ttu-id="67e26-484">**Affichages :** conservez la valeur par défaut de chaque option activée.</span><span class="sxs-lookup"><span data-stu-id="67e26-484">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="67e26-485">**Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="67e26-485">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="67e26-486">Sélectionnez **Ajouter**</span><span class="sxs-lookup"><span data-stu-id="67e26-486">Select **Add**</span></span>

![Boîte de dialogue Ajouter un contrôleur](adding-model/_static/add_controller2.png)

<span data-ttu-id="67e26-488">Visual Studio crée :</span><span class="sxs-lookup"><span data-stu-id="67e26-488">Visual Studio creates:</span></span>

* <span data-ttu-id="67e26-489">Une [classe de contexte de base de données](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core ( *Data/MvcMovieContext.cs* )</span><span class="sxs-lookup"><span data-stu-id="67e26-489">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext.cs* )</span></span>
* <span data-ttu-id="67e26-490">Un contrôleur de films ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="67e26-490">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="67e26-491">:::no-loc(Razor)::: afficher des fichiers pour les pages Create, Delete, Details, Edit et index ( *views/movies/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="67e26-491">:::no-loc(Razor)::: view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="67e26-492">La création automatique du contexte de base de données et de méthodes d’action et de vues [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (créer, lire, mettre à jour et supprimer) porte le nom de *génération de modèles automatique*.</span><span class="sxs-lookup"><span data-stu-id="67e26-492">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="67e26-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67e26-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="67e26-494">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="67e26-494">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="67e26-495">Installez l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="67e26-495">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="67e26-496">Sur Linux, exportez le chemin de l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="67e26-496">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="67e26-497">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="67e26-497">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="67e26-498">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-498">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="67e26-499">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="67e26-499">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="67e26-500">Installez l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="67e26-500">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="67e26-501">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="67e26-501">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="67e26-502">Si vous exécutez l’application et que vous cliquez sur le lien **Mvc Movie** , vous recevez une erreur semblable à la suivante :</span><span class="sxs-lookup"><span data-stu-id="67e26-502">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-503">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-503">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPool:::no-loc(Identity)::: identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-504">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-504">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="67e26-505">Vous devez créer la base de données, et vous utilisez pour cela la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core.</span><span class="sxs-lookup"><span data-stu-id="67e26-505">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="67e26-506">Les migrations permettent de créer une base de données qui correspond à votre modèle de données, et de mettre à jour le schéma de base de données quand votre modèle de données change.</span><span class="sxs-lookup"><span data-stu-id="67e26-506">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="67e26-507">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="67e26-507">Initial migration</span></span>

<span data-ttu-id="67e26-508">Dans cette section, vous devez effectuer les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="67e26-508">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="67e26-509">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="67e26-509">Add an initial migration.</span></span>
* <span data-ttu-id="67e26-510">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="67e26-510">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-511">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-511">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="67e26-512">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="67e26-512">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu Console du Gestionnaire de package](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="67e26-514">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="67e26-514">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="67e26-515">La commande `Add-Migration` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="67e26-515">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="67e26-516">Le schéma de base de données est basé sur le modèle spécifié dans la classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="67e26-516">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="67e26-517">L’argument `Initial` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="67e26-517">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="67e26-518">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est utilisé.</span><span class="sxs-lookup"><span data-stu-id="67e26-518">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="67e26-519">Pour plus d'informations, consultez <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="67e26-519">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="67e26-520">La `Update-Database` commande exécute la `Up` méthode dans le fichier *migrations/{Time-horodatage} _InitialCreate. cs* , qui crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-520">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-521">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-521">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="67e26-522">La commande `ef migrations add InitialCreate` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="67e26-522">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="67e26-523">Le schéma de base de donénes est basé sur le modèle spécifié dans la classe `MvcMovieContext` (dans *Data/MvcMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="67e26-523">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="67e26-524">L’argument `InitialCreate` est le nom de la migration.</span><span class="sxs-lookup"><span data-stu-id="67e26-524">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="67e26-525">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="67e26-525">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="67e26-526">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="67e26-526">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="67e26-527">ASP.NET Core comprend [l’injection de dépendances (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="67e26-527">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="67e26-528">Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="67e26-528">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="67e26-529">Ces services sont fournis par les composants qui requièrent ces services (tels que les :::no-loc(Razor)::: pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="67e26-529">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="67e26-530">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="67e26-530">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67e26-531">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67e26-531">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67e26-532">L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="67e26-532">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="67e26-533">Examinez la méthode `Startup.ConfigureServices` suivante.</span><span class="sxs-lookup"><span data-stu-id="67e26-533">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="67e26-534">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="67e26-534">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="67e26-535">`MvcMovieContext` coordonne les fonctionnalités d’EF Core (Create, Read, Update, Delete, etc.) pour le modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="67e26-535">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="67e26-536">Le contexte de données (`MvcMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="67e26-536">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="67e26-537">Il spécifie les entités qui sont incluses dans le modèle de données :</span><span class="sxs-lookup"><span data-stu-id="67e26-537">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="67e26-538">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="67e26-538">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="67e26-539">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="67e26-539">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="67e26-540">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="67e26-540">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="67e26-541">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="67e26-541">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="67e26-542">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *:::no-loc(appsettings.json):::* fichier.</span><span class="sxs-lookup"><span data-stu-id="67e26-542">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="67e26-543">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="67e26-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="67e26-544">Vous avez créé un contexte de base de données et vous l’avez inscrit dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="67e26-544">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="67e26-545">Tester l'application</span><span class="sxs-lookup"><span data-stu-id="67e26-545">Test the app</span></span>

* <span data-ttu-id="67e26-546">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="67e26-546">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="67e26-547">Si vous obtenez une exception de base de données similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="67e26-547">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="67e26-548">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="67e26-548">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="67e26-549">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="67e26-549">Test the **Create** link.</span></span> <span data-ttu-id="67e26-550">Entrez et envoyez des données.</span><span class="sxs-lookup"><span data-stu-id="67e26-550">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="67e26-551">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="67e26-551">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="67e26-552">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="67e26-552">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="67e26-553">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="67e26-553">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="67e26-554">Testez les liens **Edit** , **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="67e26-554">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="67e26-555">Examiner la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="67e26-555">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="67e26-556">Le code précédent mis en surbrillance montre le contexte de la base de données des films qui est ajouté au conteneur [d’injection de dépendance](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="67e26-556">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="67e26-557">`services.AddDbContext<MvcMovieContext>(options =>` spécifie la base de données à utiliser et la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="67e26-557">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="67e26-558">`=>` est un [opérateur lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="67e26-558">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="67e26-559">Ouvrez le fichier *Controllers/MoviesController.cs* et examinez le constructeur :</span><span class="sxs-lookup"><span data-stu-id="67e26-559">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="67e26-560">Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="67e26-560">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="67e26-561">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="67e26-561">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="67e26-562">Modèles fortement typés et mot clé @model</span><span class="sxs-lookup"><span data-stu-id="67e26-562">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="67e26-563">Plus tôt dans ce didacticiel, vous avez vu comment un contrôleur peut passer des données ou des objets à une vue en utilisant le dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="67e26-563">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="67e26-564">Le dictionnaire `ViewData` est un objet dynamique qui fournit un moyen pratique d’effectuer une liaison tardive pour passer des informations à une vue.</span><span class="sxs-lookup"><span data-stu-id="67e26-564">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="67e26-565">Le modèle MVC fournit également la possibilité de passer des objets de modèle fortement typés à une vue.</span><span class="sxs-lookup"><span data-stu-id="67e26-565">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="67e26-566">Cette approche fortement typée permet une meilleure vérification de votre code au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="67e26-566">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="67e26-567">Le mécanisme de génération de modèles automatique a utilisé cette approche (c’est-à-dire passer un modèle fortement typé) avec la classe `MoviesController` et les vues quand il a créé les méthodes et les vues.</span><span class="sxs-lookup"><span data-stu-id="67e26-567">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="67e26-568">Examinez la méthode `Details` générée dans le fichier *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="67e26-568">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="67e26-569">Le paramètre `id` est généralement passé en tant que données de routage.</span><span class="sxs-lookup"><span data-stu-id="67e26-569">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="67e26-570">Par exemple, `https://localhost:5001/movies/details/1` définit :</span><span class="sxs-lookup"><span data-stu-id="67e26-570">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="67e26-571">Le contrôleur sur le contrôleur `movies` (le premier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="67e26-571">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="67e26-572">L’action sur `details` (le deuxième segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="67e26-572">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="67e26-573">L’ID sur 1 (le dernier segment de l’URL).</span><span class="sxs-lookup"><span data-stu-id="67e26-573">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="67e26-574">Vous pouvez aussi passer `id` avec une requête de chaîne, comme suit :</span><span class="sxs-lookup"><span data-stu-id="67e26-574">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="67e26-575">Le `id` paramètre est défini en tant que [type Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) au cas où une valeur d’ID n’est pas fournie.</span><span class="sxs-lookup"><span data-stu-id="67e26-575">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="67e26-576">Une [expression lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) est passée à `FirstOrDefaultAsync` pour sélectionner les entités de film qui correspondent aux données de routage ou à la valeur de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="67e26-576">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="67e26-577">Si un film est trouvé, une instance du modèle `Movie` est passée à la vue `Details` :</span><span class="sxs-lookup"><span data-stu-id="67e26-577">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="67e26-578">Examinez le contenu du fichier *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="67e26-578">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="67e26-579">En incluant une instruction `@model` en haut du fichier de la vue, vous pouvez spécifier le type d’objet attendu par la vue.</span><span class="sxs-lookup"><span data-stu-id="67e26-579">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="67e26-580">Quand vous avez créé le contrôleur pour les films, l’instruction `@model` suivante a été incluse automatiquement en haut du fichier *Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="67e26-580">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="67e26-581">Cette directive `@model` vous permet d’accéder au film que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="67e26-581">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="67e26-582">Par exemple, dans la vue *Details.cshtml* , le code passe chaque champ du film aux Helpers HTML `DisplayNameFor` et `DisplayFor` avec l’objet `Model` fortement typé.</span><span class="sxs-lookup"><span data-stu-id="67e26-582">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="67e26-583">Les méthodes et les vues `Create` et `Edit` passent aussi un objet du modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="67e26-583">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="67e26-584">Examinez la vue *Index.cshtml* et la méthode `Index` dans le contrôleur Movies.</span><span class="sxs-lookup"><span data-stu-id="67e26-584">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="67e26-585">Notez comment le code crée un objet `List` quand il appelle la méthode `View`.</span><span class="sxs-lookup"><span data-stu-id="67e26-585">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="67e26-586">Le code passe cette liste `Movies` de la méthode d’action `Index` à la vue :</span><span class="sxs-lookup"><span data-stu-id="67e26-586">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="67e26-587">Quand vous avez créé le contrôleur pour les films, la génération de modèles automatique a inclus automatiquement l’instruction `@model` suivante en haut du fichier *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="67e26-587">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="67e26-588">La directive `@model` vous permet d’accéder à la liste des films que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé.</span><span class="sxs-lookup"><span data-stu-id="67e26-588">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="67e26-589">Par exemple, dans la vue *Index.cshtml* , le code boucle dans les films avec une instruction `foreach` sur l’objet `Model` fortement typé :</span><span class="sxs-lookup"><span data-stu-id="67e26-589">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="67e26-590">Comme l’objet `Model` est fortement typé (en tant qu’objet `IEnumerable<Movie>`), chaque élément de la boucle est typé en tant que `Movie`.</span><span class="sxs-lookup"><span data-stu-id="67e26-590">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="67e26-591">Entre autres avantages, cela signifie que votre code est vérifié au moment de la compilation :</span><span class="sxs-lookup"><span data-stu-id="67e26-591">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="67e26-592">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="67e26-592">Additional resources</span></span>

* [<span data-ttu-id="67e26-593">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="67e26-593">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="67e26-594">Globalisation et localisation</span><span class="sxs-lookup"><span data-stu-id="67e26-594">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="67e26-595">[Ajout d’une vue précédente](adding-view.md) 
>  [Utilisation d’une base de données](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="67e26-595">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
