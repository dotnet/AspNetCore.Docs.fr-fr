---
title: Partie 2, ajouter un modèle
author: rick-anderson
description: Partie 2 de la série de didacticiels sur les Razor pages. Dans cette section, des classes de modèle sont ajoutées.
ms.author: riande
ms.date: 11/11/2020
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7ea28e0ecad410335c37c603c8ec1eb5e6e41d33
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485990"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="9bd5b-104">Partie 2, ajouter un modèle à une Razor application pages dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="9bd5b-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="9bd5b-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9bd5b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="9bd5b-106">Dans cette section, des classes sont ajoutées pour la gestion des films dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="9bd5b-107">Les classes de modèle de l’application utilisent [Entity Framework Core (EF Core)](/ef/core) pour travailler avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="9bd5b-108">EF Core est un mappeur objet-relationnel (O/RM) qui simplifie l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="9bd5b-109">Vous écrivez d’abord les classes de modèle, et EF Core crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="9bd5b-110">Les classes de modèle sont appelées classes POCO (à partir de «**P** Lain-**O** LD **C** LR **O** bjets »), car elles n’ont pas de dépendance sur EF Core.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="9bd5b-111">Elles définissent les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="9bd5b-112">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="9bd5b-113">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="9bd5b-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9bd5b-115">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet *Razor PagesMovie* > **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="9bd5b-116">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="9bd5b-117">Cliquez avec le bouton droit sur le dossier *modèles* .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="9bd5b-118">Sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="9bd5b-119">Nommez la classe *Movie*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="9bd5b-120">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9bd5b-121">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="9bd5b-122">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9bd5b-123">`[DataType(DataType.Date)]`: L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9bd5b-124">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-124">With this attribute:</span></span>

  * <span data-ttu-id="9bd5b-125">L’utilisateur n’est pas obligé d’entrer des informations d’heure dans le champ date.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9bd5b-126">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd5b-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd5b-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9bd5b-128">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="9bd5b-129">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="9bd5b-130">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9bd5b-131">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="9bd5b-132">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9bd5b-133">`[DataType(DataType.Date)]`: L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9bd5b-134">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-134">With this attribute:</span></span>

  * <span data-ttu-id="9bd5b-135">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9bd5b-136">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="9bd5b-137">Ajouter des packages NuGet des outils EF</span><span class="sxs-lookup"><span data-stu-id="9bd5b-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="9bd5b-138">Ajouter une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="9bd5b-138">Add a database context class</span></span>

1. <span data-ttu-id="9bd5b-139">Dans le projet *Razor PagesMovie* , créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="9bd5b-140">Dans le dossier *Data* , ajoutez un fichier nommé *Razor PagesMovieContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="9bd5b-141">Le code précédent crée une propriété `DbSet` pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="9bd5b-142">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="9bd5b-143">Le code n’est pas compilé tant que les dépendances n’ont pas été ajoutées dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="9bd5b-144">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="9bd5b-144">Add a database connection string</span></span>

<span data-ttu-id="9bd5b-145">Ajoutez une chaîne de connexion au *appsettings.json* fichier comme indiqué dans le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9bd5b-146">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="9bd5b-146">Register the database context</span></span>

1. <span data-ttu-id="9bd5b-147">En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="9bd5b-148">Inscrivez le contexte de base de données avec le conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-149">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9bd5b-150">Dans la **fenêtre outil de solution**, cliquez avec le contrôle sur le projet *Razor PagesMovie* , puis sélectionnez **Ajouter** > **un nouveau dossier...**. Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="9bd5b-151">Cliquez avec le contrôle sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier...**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="9bd5b-152">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="9bd5b-153">Dans le volet gauche, sélectionnez **Général**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="9bd5b-154">Dans le volet central, sélectionnez **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="9bd5b-155">Nommez la classe **Movie**, puis sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="9bd5b-156">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9bd5b-157">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="9bd5b-158">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9bd5b-159">`[DataType(DataType.Date)]`: L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9bd5b-160">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-160">With this attribute:</span></span>

  * <span data-ttu-id="9bd5b-161">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9bd5b-162">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="9bd5b-163">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="9bd5b-164">Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="9bd5b-165">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="9bd5b-165">Scaffold the movie model</span></span>

<span data-ttu-id="9bd5b-166">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="9bd5b-167">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9bd5b-169">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="9bd5b-170">Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="9bd5b-171">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="9bd5b-172">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/5/sca.png)

1. <span data-ttu-id="9bd5b-174">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

1. <span data-ttu-id="9bd5b-176">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="9bd5b-177">Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="9bd5b-178">Dans la ligne **Classe du contexte de données**, sélectionnez le signe **+** (plus).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="9bd5b-179">Dans la boîte de dialogue **Ajouter un contexte de données** , nommez la classe *Razor PagesMovie. Data. Razor PagesMovieContext* est généré.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="9bd5b-180">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-180">Select **Add**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/3/arp.png)

<span data-ttu-id="9bd5b-182">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd5b-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd5b-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="9bd5b-184">Ouvrez une interface de commande dans le répertoire du projet, qui contient les fichiers *Program.cs*, *Startup.cs* et *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="9bd5b-185">**Pour Windows**: exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="9bd5b-186">**Pour macOS et Linux**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="9bd5b-187">Le tableau suivant détaille les options du générateur de code ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="9bd5b-188">Option</span><span class="sxs-lookup"><span data-stu-id="9bd5b-188">Option</span></span>               | <span data-ttu-id="9bd5b-189">Description</span><span class="sxs-lookup"><span data-stu-id="9bd5b-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="9bd5b-190">Nom du modèle.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="9bd5b-191">Classe `DbContext` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="9bd5b-192">Utiliser la disposition par défaut.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="9bd5b-193">Chemin du dossier de sortie relatif pour créer les vues.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="9bd5b-194">Ajoute `_ValidationScriptsPartial` aux pages Modifier et Créer.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="9bd5b-195">Utilisez l' `-h` option pour obtenir de l’aide sur la `aspnet-codegenerator razorpage` commande :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="9bd5b-196">Pour plus d’informations, consultez [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9bd5b-197">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="9bd5b-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9bd5b-198">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9bd5b-199">Le code suivant montre comment injecter <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans `Startup` .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="9bd5b-200">`IWebHostEnvironment` est injecté afin que l’application puisse utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-201">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9bd5b-202">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="9bd5b-203">Cliquez avec le contrôle sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="9bd5b-204">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="9bd5b-205">Cliquez avec le contrôle sur le dossier *pages/movies* > **Ajouter** une > **nouvelle génération de modèles automatique...**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

1. <span data-ttu-id="9bd5b-207">Dans la boîte de dialogue **nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="9bd5b-209">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="9bd5b-210">Dans la **classe DbContext à utiliser :** Row, nommez la classe *Razor PagesMovie. Data. Razor PagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="9bd5b-211">Sélectionnez **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-211">Select **Finish**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/5/arpMac.png)

<span data-ttu-id="9bd5b-213">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9bd5b-214">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="9bd5b-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9bd5b-215">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9bd5b-216">Le code suivant montre comment injecter <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans `Startup` .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="9bd5b-217">`IWebHostEnvironment` est injecté afin que l’application puisse utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="9bd5b-218">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="9bd5b-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9bd5b-220">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9bd5b-221">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9bd5b-222">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd5b-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9bd5b-223">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="9bd5b-223">Updated</span></span>

* <span data-ttu-id="9bd5b-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd5b-224">*Startup.cs*</span></span>

<span data-ttu-id="9bd5b-225">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd5b-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd5b-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9bd5b-227">Le processus de génération de modèles automatique crée les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="9bd5b-228">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="9bd5b-229">Les fichiers créés sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-230">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9bd5b-231">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9bd5b-232">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9bd5b-233">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd5b-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9bd5b-234">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="9bd5b-234">Updated</span></span>

* <span data-ttu-id="9bd5b-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd5b-235">*Startup.cs*</span></span>

<span data-ttu-id="9bd5b-236">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="9bd5b-237">Créer le schéma de base de données initial à l’aide de la fonctionnalité de migration d’EF</span><span class="sxs-lookup"><span data-stu-id="9bd5b-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="9bd5b-238">La fonctionnalité migrations de Entity Framework Core offre un moyen d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="9bd5b-239">Créez le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-239">Create the initial database schema.</span></span>
* <span data-ttu-id="9bd5b-240">Mettez à jour de façon incrémentielle le schéma de base de données pour le maintenir synchronisé avec le modèle de données de l’application.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="9bd5b-241">Les données existantes de la base de données sont conservées.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9bd5b-243">Dans cette section, la fenêtre **console du gestionnaire de package** (PMC) permet d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="9bd5b-244">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-244">Add an initial migration.</span></span>
* <span data-ttu-id="9bd5b-245">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="9bd5b-246">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menu Console du Gestionnaire de package](model/_static/5/pmc.png)

1. <span data-ttu-id="9bd5b-248">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-249">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="9bd5b-250">Exécutez les commandes CLI .NET suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="9bd5b-251">Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ».</span><span class="sxs-lookup"><span data-stu-id="9bd5b-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="9bd5b-252">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="9bd5b-253">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »</span><span class="sxs-lookup"><span data-stu-id="9bd5b-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="9bd5b-254">Ignorez l’avertissement, car il sera traité dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="9bd5b-255">La commande `migrations` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="9bd5b-256">Le schéma est basé sur le modèle spécifié dans `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="9bd5b-257">L’argument `InitialCreate` est utilisé pour nommer les migrations.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="9bd5b-258">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="9bd5b-259">La `update` commande exécute la `Up` méthode dans des migrations qui n’ont pas été appliquées.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="9bd5b-260">Dans ce cas, `update` exécute la `Up` méthode dans le fichier *migrations/ \<time-stamp> _InitialCreate. cs* , qui crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="9bd5b-262">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="9bd5b-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="9bd5b-263">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9bd5b-264">Les services, tels que le contexte de base de données EF Core, sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9bd5b-265">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="9bd5b-266">Le code de constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9bd5b-267">L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit auprès du conteneur d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="9bd5b-268">Examinez la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9bd5b-269">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="9bd5b-270">Les `RazorPagesMovieContext` coordonnées EF Core fonctionnalité, telles que la création, la lecture, la mise à jour et la suppression, pour le `Movie` modèle.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="9bd5b-271">Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="9bd5b-272">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9bd5b-273">Le code précédent crée une [propriété \<Movie> DbSet](xref:Microsoft.EntityFrameworkCore.DbSet%601) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="9bd5b-274">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="9bd5b-275">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9bd5b-276">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="9bd5b-277">Pour le développement local, le [système de configuration](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-278">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9bd5b-279">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="9bd5b-280">Test de l'application</span><span class="sxs-lookup"><span data-stu-id="9bd5b-280">Test the app</span></span>

1. <span data-ttu-id="9bd5b-281">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="9bd5b-282">Si vous recevez l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="9bd5b-283">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="9bd5b-284">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-284">Test the **Create** link.</span></span>

   ![Create page](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="9bd5b-286">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9bd5b-287">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="9bd5b-288">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="9bd5b-289">Testez les liens **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="9bd5b-290">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9bd5b-291">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9bd5b-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9bd5b-292">[Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="9bd5b-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="9bd5b-293">Dans cette section, des classes sont ajoutées pour la gestion des films.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="9bd5b-294">Les classes de modèle de l’application utilisent [Entity Framework Core (EF Core)](/ef/core) pour travailler avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="9bd5b-295">EF Core est un mappeur objet-relationnel (O/RM) qui simplifie l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="9bd5b-296">Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="9bd5b-297">Elles définissent les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="9bd5b-298">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="9bd5b-299">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="9bd5b-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9bd5b-301">Cliquez avec le bouton droit sur le projet **Razor PagesMovie** > **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="9bd5b-302">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-302">Name the folder *Models*.</span></span>

<span data-ttu-id="9bd5b-303">Cliquez avec le bouton droit sur le dossier *modèles* .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="9bd5b-304">Sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="9bd5b-305">Nommez la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-305">Name the class **Movie**.</span></span>

<span data-ttu-id="9bd5b-306">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9bd5b-307">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="9bd5b-308">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9bd5b-309">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9bd5b-310">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-310">With this attribute:</span></span>

  * <span data-ttu-id="9bd5b-311">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9bd5b-312">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9bd5b-313">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd5b-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd5b-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9bd5b-315">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="9bd5b-316">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="9bd5b-317">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9bd5b-318">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="9bd5b-319">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9bd5b-320">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9bd5b-321">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-321">With this attribute:</span></span>

  * <span data-ttu-id="9bd5b-322">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9bd5b-323">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9bd5b-324">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="9bd5b-325">Ajouter des packages NuGet des outils EF</span><span class="sxs-lookup"><span data-stu-id="9bd5b-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="9bd5b-326">Ajouter une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="9bd5b-326">Add a database context class</span></span>

* <span data-ttu-id="9bd5b-327">Dans le projet *Razor PagesMovie* , créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="9bd5b-328">Ajoutez la classe `RazorPagesMovieContext` suivante au dossier *Data* :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9bd5b-329">Le code précédent crée une propriété `DbSet` pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="9bd5b-330">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="9bd5b-331">Le code n’est pas compilé tant que les dépendances n’ont pas été ajoutées dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="9bd5b-332">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="9bd5b-332">Add a database connection string</span></span>

<span data-ttu-id="9bd5b-333">Ajoutez une chaîne de connexion au *appsettings.json* fichier comme indiqué dans le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9bd5b-334">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="9bd5b-334">Register the database context</span></span>

<span data-ttu-id="9bd5b-335">En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="9bd5b-336">Inscrivez le contexte de base de données auprès du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-337">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9bd5b-338">Dans la **fenêtre outil de solution**, cliquez avec le contrôle sur le projet **Razor PagesMovie** , puis sélectionnez **Ajouter** > **un nouveau dossier...**. Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="9bd5b-339">Cliquez avec le bouton droit sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier...**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="9bd5b-340">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="9bd5b-341">Dans le volet gauche, sélectionnez **Général**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="9bd5b-342">Dans le volet central, sélectionnez **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="9bd5b-343">Nommez la classe **Movie**, puis sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="9bd5b-344">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9bd5b-345">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="9bd5b-346">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9bd5b-347">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9bd5b-348">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-348">With this attribute:</span></span>

  * <span data-ttu-id="9bd5b-349">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9bd5b-350">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="9bd5b-351">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="9bd5b-352">Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="9bd5b-353">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="9bd5b-353">Scaffold the movie model</span></span>

<span data-ttu-id="9bd5b-354">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="9bd5b-355">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9bd5b-357">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9bd5b-358">Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9bd5b-359">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="9bd5b-360">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/sca.png)

<span data-ttu-id="9bd5b-362">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

<span data-ttu-id="9bd5b-364">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="9bd5b-365">Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="9bd5b-366">Dans la ligne de la **classe de contexte de données** , sélectionnez le **+** signe (plus), puis modifiez le nom généré à partir de Razor PagesMovie.**Modèles**. Razor PagesMovieContext à Razor PagesMovie.**Données**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="9bd5b-367">[Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="9bd5b-368">Elle crée la classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="9bd5b-369">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-369">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/3/arp.png)

<span data-ttu-id="9bd5b-371">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd5b-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd5b-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="9bd5b-373">Ouvrez une fenêtre de commande dans le répertoire du projet, qui contient les fichiers *Program.cs*, *Startup.cs* et *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="9bd5b-374">**Pour Windows**: exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="9bd5b-375">**Pour macOS et Linux**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="9bd5b-376">Le tableau suivant détaille les options du générateur de code ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="9bd5b-377">Option</span><span class="sxs-lookup"><span data-stu-id="9bd5b-377">Option</span></span>               | <span data-ttu-id="9bd5b-378">Description</span><span class="sxs-lookup"><span data-stu-id="9bd5b-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="9bd5b-379">Nom du modèle.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="9bd5b-380">Classe `DbContext` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="9bd5b-381">Utiliser la disposition par défaut.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="9bd5b-382">Chemin du dossier de sortie relatif pour créer les vues.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="9bd5b-383">Ajoute `_ValidationScriptsPartial` aux pages Modifier et Créer.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="9bd5b-384">Utilisez l' `-h` option pour obtenir de l’aide sur la `aspnet-codegenerator razorpage` commande :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="9bd5b-385">Pour plus d’informations, consultez [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9bd5b-386">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="9bd5b-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9bd5b-387">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9bd5b-388">Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="9bd5b-389">`IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-390">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9bd5b-391">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9bd5b-392">Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9bd5b-393">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="9bd5b-394">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** une > **nouvelle génération de modèles automatique...**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

<span data-ttu-id="9bd5b-396">Dans la boîte de dialogue **nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="9bd5b-398">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="9bd5b-399">Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="9bd5b-400">Dans la ligne de la **classe de contexte de données** , tapez le nom de la nouvelle classe, Razor PagesMovie.**Données**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="9bd5b-401">[Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="9bd5b-402">Elle crée la classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="9bd5b-403">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-403">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

<span data-ttu-id="9bd5b-405">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="9bd5b-406">Ajouter des outils EF</span><span class="sxs-lookup"><span data-stu-id="9bd5b-406">Add EF tools</span></span>

<span data-ttu-id="9bd5b-407">Exécutez la commande CLI .NET Core suivante :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="9bd5b-408">La commande précédente ajoute les outils de Entity Framework Core pour le CLI .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="9bd5b-409">Pour plus d’informations, consultez [référence des outils de Entity Framework Core-CLI .net Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9bd5b-410">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="9bd5b-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9bd5b-411">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9bd5b-412">Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="9bd5b-413">`IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="9bd5b-414">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="9bd5b-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9bd5b-416">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9bd5b-417">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9bd5b-418">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd5b-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9bd5b-419">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="9bd5b-419">Updated</span></span>

* <span data-ttu-id="9bd5b-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd5b-420">*Startup.cs*</span></span>

<span data-ttu-id="9bd5b-421">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-422">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9bd5b-423">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9bd5b-424">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9bd5b-425">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd5b-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9bd5b-426">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="9bd5b-426">Updated</span></span>

* <span data-ttu-id="9bd5b-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd5b-427">*Startup.cs*</span></span>

<span data-ttu-id="9bd5b-428">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd5b-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd5b-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9bd5b-430">Le processus de génération de modèles automatique crée les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="9bd5b-431">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="9bd5b-432">Les fichiers créés sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="9bd5b-433">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="9bd5b-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9bd5b-435">Dans cette section, la console du gestionnaire de package est utilisée pour :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="9bd5b-436">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-436">Add an initial migration.</span></span>
* <span data-ttu-id="9bd5b-437">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="9bd5b-438">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="9bd5b-440">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-441">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="9bd5b-442">Exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="9bd5b-443">Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ».</span><span class="sxs-lookup"><span data-stu-id="9bd5b-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="9bd5b-444">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="9bd5b-445">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »</span><span class="sxs-lookup"><span data-stu-id="9bd5b-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="9bd5b-446">Ignorez l’avertissement, car il sera traité dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="9bd5b-447">La commande migrations génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="9bd5b-448">Le schéma est basé sur le modèle spécifié dans `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="9bd5b-449">L’argument `InitialCreate` est utilisé pour nommer les migrations.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="9bd5b-450">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="9bd5b-451">La `update` commande exécute la `Up` méthode dans des migrations qui n’ont pas été appliquées.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="9bd5b-452">Dans ce cas, `update` exécute la `Up` méthode dans le fichier  *migrations/ \<time-stamp> _InitialCreate. cs* , qui crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="9bd5b-454">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="9bd5b-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="9bd5b-455">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9bd5b-456">Les services, tels que le contexte de contexte de la base de données EF Core, sont inscrits avec l’injection de dépendance pendant le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9bd5b-457">Ces services sont fournis par les composants qui requièrent ces services, tels que les Razor pages, via des paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="9bd5b-458">Le code du constructeur qui obtient une instance de contexte de contexte de base de données est indiqué plus loin dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9bd5b-459">L’outil de génération de modèles automatique a créé automatiquement un contexte de contexte de base de données et l’a inscrit auprès du conteneur d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="9bd5b-460">Examinez la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9bd5b-461">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="9bd5b-462">Les `RazorPagesMovieContext` coordonnées EF Core fonctionnalité, telles que la création, la lecture, la mise à jour et la suppression, pour le `Movie` modèle.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="9bd5b-463">Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="9bd5b-464">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9bd5b-465">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="9bd5b-466">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="9bd5b-467">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9bd5b-468">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="9bd5b-469">Pour le développement local, le [système de configuration](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-470">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9bd5b-471">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="9bd5b-472">Test de l'application</span><span class="sxs-lookup"><span data-stu-id="9bd5b-472">Test the app</span></span>

* <span data-ttu-id="9bd5b-473">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="9bd5b-474">Si vous obtenez cette erreur :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="9bd5b-475">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="9bd5b-476">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-476">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="9bd5b-478">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9bd5b-479">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="9bd5b-480">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="9bd5b-481">Testez les liens **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="9bd5b-482">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9bd5b-483">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9bd5b-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9bd5b-484">[Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="9bd5b-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9bd5b-485">Dans cette section, des classes sont ajoutées pour la gestion des films dans une [base de données SQLite](https://www.sqlite.org/index.html)multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="9bd5b-486">Les applications créées à partir d’un modèle de ASP.NET Core utilisent une base de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="9bd5b-487">Les classes de modèle de l’application sont utilisées avec [Entity Framework Core (EF Core)](/ef/core) ([fournisseur de base de données SQLite EF Core](/ef/core/providers/sqlite)) pour fonctionner avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="9bd5b-488">EF Core est un framework de mappage relationnel d’objets qui simplifie l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="9bd5b-489">Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="9bd5b-490">Elles définissent les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="9bd5b-491">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="9bd5b-492">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="9bd5b-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9bd5b-494">Cliquez avec le bouton droit sur le projet **Razor PagesMovie** > **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="9bd5b-495">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-495">Name the folder *Models*.</span></span>

<span data-ttu-id="9bd5b-496">Cliquez avec le bouton droit sur le dossier *modèles* .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="9bd5b-497">Sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="9bd5b-498">Nommez la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-498">Name the class **Movie**.</span></span>

<span data-ttu-id="9bd5b-499">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9bd5b-500">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="9bd5b-501">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9bd5b-502">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9bd5b-503">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-503">With this attribute:</span></span>

  * <span data-ttu-id="9bd5b-504">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9bd5b-505">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9bd5b-506">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd5b-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd5b-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9bd5b-508">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="9bd5b-509">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="9bd5b-510">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9bd5b-511">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="9bd5b-512">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9bd5b-513">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9bd5b-514">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-514">With this attribute:</span></span>

  * <span data-ttu-id="9bd5b-515">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9bd5b-516">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9bd5b-517">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="9bd5b-518">Ajouter des packages NuGet des outils EF</span><span class="sxs-lookup"><span data-stu-id="9bd5b-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="9bd5b-519">Ajouter une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="9bd5b-519">Add a database context class</span></span>

<span data-ttu-id="9bd5b-520">Dans le Razor projet PagesMovie, créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="9bd5b-521">Ajoutez la classe `RazorPagesMovieContext` suivante au dossier *Data* :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9bd5b-522">Le code précédent crée une propriété `DbSet` pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="9bd5b-523">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="9bd5b-524">Le code n’est pas compilé tant que les dépendances n’ont pas été ajoutées dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="9bd5b-525">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="9bd5b-525">Add a database connection string</span></span>

<span data-ttu-id="9bd5b-526">Ajoutez une chaîne de connexion au *appsettings.json* fichier comme indiqué dans le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="9bd5b-527">Ajouter les packages NuGet exigés</span><span class="sxs-lookup"><span data-stu-id="9bd5b-527">Add required NuGet packages</span></span>

<span data-ttu-id="9bd5b-528">Exécutez la commande CLI .NET Core suivante pour ajouter SQLite et CodeGeneration. Design au projet :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="9bd5b-529">Le package `Microsoft.VisualStudio.Web.CodeGeneration.Design` est nécessaire à la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9bd5b-530">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="9bd5b-530">Register the database context</span></span>

<span data-ttu-id="9bd5b-531">En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="9bd5b-532">Inscrivez le contexte de base de données auprès du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="9bd5b-533">Générez le projet en tant que vérification des erreurs.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-534">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9bd5b-535">Dans la **fenêtre outil** de la solution, cliquez avec le contrôle sur le projet *Razor PagesMovie* , puis sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="9bd5b-536">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="9bd5b-537">Cliquez avec le contrôle sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="9bd5b-538">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="9bd5b-539">Dans le volet gauche, sélectionnez **Général**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="9bd5b-540">Dans le volet central, sélectionnez **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="9bd5b-541">Nommez la classe **Movie**, puis sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="9bd5b-542">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9bd5b-543">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="9bd5b-544">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9bd5b-545">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9bd5b-546">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-546">With this attribute:</span></span>

  * <span data-ttu-id="9bd5b-547">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9bd5b-548">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9bd5b-549">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="9bd5b-550">Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="9bd5b-551">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="9bd5b-551">Scaffold the movie model</span></span>

<span data-ttu-id="9bd5b-552">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="9bd5b-553">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9bd5b-555">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9bd5b-556">Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9bd5b-557">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="9bd5b-558">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/sca.png)

<span data-ttu-id="9bd5b-560">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

<span data-ttu-id="9bd5b-562">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="9bd5b-563">Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="9bd5b-564">Dans la ligne de la **classe de contexte de données** , sélectionnez le **+** signe (plus) et acceptez le nom généré **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="9bd5b-565">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-565">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/arp.png)

<span data-ttu-id="9bd5b-567">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9bd5b-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9bd5b-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="9bd5b-569">Ouvrez une fenêtre de commande dans le répertoire du projet, qui contient les fichiers *Program.cs*, *Startup.cs* et *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="9bd5b-570">**Pour Windows**: exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="9bd5b-571">**Pour macOS et Linux**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="9bd5b-572">Le tableau suivant détaille les options du générateur de code ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="9bd5b-573">Option</span><span class="sxs-lookup"><span data-stu-id="9bd5b-573">Option</span></span>               | <span data-ttu-id="9bd5b-574">Description</span><span class="sxs-lookup"><span data-stu-id="9bd5b-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="9bd5b-575">Nom du modèle.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="9bd5b-576">Classe `DbContext` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="9bd5b-577">Utiliser la disposition par défaut.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="9bd5b-578">Chemin du dossier de sortie relatif pour créer les vues.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="9bd5b-579">Ajoute `_ValidationScriptsPartial` aux pages Modifier et Créer.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="9bd5b-580">Utilisez l' `-h` option pour obtenir de l’aide sur la `aspnet-codegenerator razorpage` commande :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="9bd5b-581">Pour plus d’informations, consultez [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-582">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9bd5b-583">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9bd5b-584">Cliquez avec le contrôle sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9bd5b-585">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="9bd5b-586">Cliquez avec le contrôle sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

<span data-ttu-id="9bd5b-588">Dans la boîte de dialogue **Ajouter une nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="9bd5b-590">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="9bd5b-591">Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="9bd5b-592">Dans la ligne de la **classe de contexte de données** , tapez Select the **Razor PagesMovieContext** This crée une nouvelle classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="9bd5b-593">Dans ce cas, il s’agit de **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="9bd5b-594">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-594">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

<span data-ttu-id="9bd5b-596">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="9bd5b-597">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="9bd5b-598">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="9bd5b-598">Files created</span></span>

* <span data-ttu-id="9bd5b-599">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9bd5b-600">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd5b-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="9bd5b-601">Fichier mis à jour</span><span class="sxs-lookup"><span data-stu-id="9bd5b-601">File updated</span></span>

* <span data-ttu-id="9bd5b-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9bd5b-602">*Startup.cs*</span></span>

<span data-ttu-id="9bd5b-603">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="9bd5b-604">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="9bd5b-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9bd5b-606">Dans cette section, la console du gestionnaire de package est utilisée pour :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="9bd5b-607">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-607">Add an initial migration.</span></span>
* <span data-ttu-id="9bd5b-608">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="9bd5b-609">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="9bd5b-611">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="9bd5b-612">La commande `Add-Migration` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="9bd5b-613">Le schéma est basé sur le modèle spécifié dans le `DbContext` , dans le fichier *Razor PagesMovieContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="9bd5b-614">L' `InitialCreate` argument est utilisé pour nommer la migration.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="9bd5b-615">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est utilisé.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="9bd5b-616">Pour plus d’informations, consultez <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="9bd5b-617">La `Update-Database` commande exécute la `Up` méthode dans le fichier *migrations/ \<time-stamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="9bd5b-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="9bd5b-618">La méthode `Up` crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-619">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="9bd5b-620">Exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="9bd5b-621">Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ».</span><span class="sxs-lookup"><span data-stu-id="9bd5b-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="9bd5b-622">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="9bd5b-623">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »</span><span class="sxs-lookup"><span data-stu-id="9bd5b-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="9bd5b-624">Ignorez l’avertissement, car il sera traité dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9bd5b-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bd5b-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="9bd5b-626">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="9bd5b-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="9bd5b-627">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9bd5b-628">Les services (tels que le contexte de contexte de la base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9bd5b-629">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="9bd5b-630">Le code du constructeur qui obtient une instance de contexte de contextB du contexte de base de données est indiqué plus loin dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9bd5b-631">L’outil de génération de modèles automatique a créé automatiquement un contexte de contexte de base de données et l’a inscrit auprès du conteneur d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="9bd5b-632">Examinez la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9bd5b-633">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="9bd5b-634">Les `RazorPagesMovieContext` coordonnées EF Core fonctionnalité, telles que la création, la lecture, la mise à jour et la suppression, pour le `Movie` modèle.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="9bd5b-635">Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="9bd5b-636">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9bd5b-637">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="9bd5b-638">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="9bd5b-639">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9bd5b-640">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="9bd5b-641">Pour le développement local, le [système de configuration](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9bd5b-642">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9bd5b-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9bd5b-643">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="9bd5b-644">Test de l'application</span><span class="sxs-lookup"><span data-stu-id="9bd5b-644">Test the app</span></span>

* <span data-ttu-id="9bd5b-645">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="9bd5b-646">Si vous obtenez cette erreur :</span><span class="sxs-lookup"><span data-stu-id="9bd5b-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="9bd5b-647">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="9bd5b-648">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-648">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="9bd5b-650">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9bd5b-651">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="9bd5b-652">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="9bd5b-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="9bd5b-653">Testez les liens **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="9bd5b-654">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="9bd5b-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9bd5b-655">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9bd5b-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9bd5b-656">[Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="9bd5b-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
