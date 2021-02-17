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
ms.openlocfilehash: 92bfda330399b43871b3ae0e6b609726f7ad4a91
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564054"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="f6201-104">Partie 2, ajouter un modèle à une Razor application pages dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f6201-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="f6201-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f6201-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="f6201-106">Dans cette section, des classes sont ajoutées pour la gestion des films dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="f6201-107">Les classes de modèle de l’application utilisent [Entity Framework Core (EF Core)](/ef/core) pour travailler avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="f6201-108">EF Core est un mappeur objet-relationnel (O/RM) qui simplifie l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="f6201-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="f6201-109">Vous écrivez d’abord les classes de modèle, et EF Core crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="f6201-110">Les classes de modèle sont appelées classes POCO (à partir de «**P** Lain-**O** LD **C** LR **O** bjets »), car elles n’ont pas de dépendance sur EF Core.</span><span class="sxs-lookup"><span data-stu-id="f6201-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="f6201-111">Elles définissent les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="f6201-112">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f6201-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="f6201-113">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="f6201-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f6201-115">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet *Razor PagesMovie* > **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f6201-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="f6201-116">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="f6201-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="f6201-117">Cliquez avec le bouton droit sur le dossier *modèles* .</span><span class="sxs-lookup"><span data-stu-id="f6201-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="f6201-118">Sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="f6201-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="f6201-119">Nommez la classe *Movie*.</span><span class="sxs-lookup"><span data-stu-id="f6201-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="f6201-120">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="f6201-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f6201-121">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="f6201-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="f6201-122">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="f6201-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f6201-123">`[DataType(DataType.Date)]`: L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="f6201-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f6201-124">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="f6201-124">With this attribute:</span></span>

  * <span data-ttu-id="f6201-125">L’utilisateur n’est pas obligé d’entrer des informations d’heure dans le champ date.</span><span class="sxs-lookup"><span data-stu-id="f6201-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f6201-126">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="f6201-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6201-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6201-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f6201-128">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="f6201-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="f6201-129">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="f6201-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="f6201-130">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="f6201-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f6201-131">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="f6201-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="f6201-132">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="f6201-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f6201-133">`[DataType(DataType.Date)]`: L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="f6201-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f6201-134">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="f6201-134">With this attribute:</span></span>

  * <span data-ttu-id="f6201-135">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="f6201-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f6201-136">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="f6201-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="f6201-137">Ajouter des packages NuGet des outils EF</span><span class="sxs-lookup"><span data-stu-id="f6201-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="f6201-138">Ajouter une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="f6201-138">Add a database context class</span></span>

1. <span data-ttu-id="f6201-139">Dans le projet *Razor PagesMovie* , créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="f6201-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="f6201-140">Dans le dossier *Data* , ajoutez un fichier nommé *Razor PagesMovieContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f6201-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="f6201-141">Le code précédent crée une propriété `DbSet` pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="f6201-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="f6201-142">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="f6201-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="f6201-143">Le code n’est pas compilé tant que les dépendances n’ont pas été ajoutées dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="f6201-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="f6201-144">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="f6201-144">Add a database connection string</span></span>

<span data-ttu-id="f6201-145">Ajoutez une chaîne de connexion au *appsettings.json* fichier comme indiqué dans le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="f6201-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="f6201-146">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="f6201-146">Register the database context</span></span>

1. <span data-ttu-id="f6201-147">En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="f6201-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="f6201-148">Inscrivez le contexte de base de données avec le conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f6201-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6201-149">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f6201-150">Dans la **fenêtre outil de solution**, cliquez avec le contrôle sur le projet *Razor PagesMovie* , puis sélectionnez **Ajouter** > **un nouveau dossier...**. Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="f6201-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="f6201-151">Cliquez avec le contrôle sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier...**.</span><span class="sxs-lookup"><span data-stu-id="f6201-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="f6201-152">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="f6201-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="f6201-153">Dans le volet gauche, sélectionnez **Général**.</span><span class="sxs-lookup"><span data-stu-id="f6201-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="f6201-154">Dans le volet central, sélectionnez **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="f6201-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="f6201-155">Nommez la classe **Movie**, puis sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="f6201-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="f6201-156">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="f6201-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f6201-157">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="f6201-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="f6201-158">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="f6201-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f6201-159">`[DataType(DataType.Date)]`: L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="f6201-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f6201-160">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="f6201-160">With this attribute:</span></span>

  * <span data-ttu-id="f6201-161">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="f6201-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f6201-162">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="f6201-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="f6201-163">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="f6201-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="f6201-164">Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="f6201-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="f6201-165">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="f6201-165">Scaffold the movie model</span></span>

<span data-ttu-id="f6201-166">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="f6201-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="f6201-167">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="f6201-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f6201-169">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="f6201-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="f6201-170">Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f6201-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="f6201-171">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="f6201-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="f6201-172">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="f6201-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/5/sca.png)

1. <span data-ttu-id="f6201-174">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f6201-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

1. <span data-ttu-id="f6201-176">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="f6201-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="f6201-177">Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="f6201-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="f6201-178">Dans la ligne **Classe du contexte de données**, sélectionnez le signe **+** (plus).</span><span class="sxs-lookup"><span data-stu-id="f6201-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="f6201-179">Dans la boîte de dialogue **Ajouter un contexte de données** , le nom de la classe `RazorPagesMovie.Data.RazorPagesMovieContext` est généré.</span><span class="sxs-lookup"><span data-stu-id="f6201-179">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="f6201-180">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f6201-180">Select **Add**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/3/arp.png)

<span data-ttu-id="f6201-182">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="f6201-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6201-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6201-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="f6201-184">Ouvrez une interface de commande dans le répertoire du projet, qui contient les fichiers *Program.cs*, *Startup.cs* et *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="f6201-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="f6201-185">**Pour Windows**: exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f6201-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="f6201-186">**Pour macOS et Linux**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f6201-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="f6201-187">Le tableau suivant détaille les options du générateur de code ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6201-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="f6201-188">Option</span><span class="sxs-lookup"><span data-stu-id="f6201-188">Option</span></span>               | <span data-ttu-id="f6201-189">Description</span><span class="sxs-lookup"><span data-stu-id="f6201-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="f6201-190">Nom du modèle.</span><span class="sxs-lookup"><span data-stu-id="f6201-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="f6201-191">Classe `DbContext` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="f6201-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="f6201-192">Utiliser la disposition par défaut.</span><span class="sxs-lookup"><span data-stu-id="f6201-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="f6201-193">Chemin du dossier de sortie relatif pour créer les vues.</span><span class="sxs-lookup"><span data-stu-id="f6201-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="f6201-194">Ajoute `_ValidationScriptsPartial` aux pages Modifier et Créer.</span><span class="sxs-lookup"><span data-stu-id="f6201-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="f6201-195">Utilisez l' `-h` option pour obtenir de l’aide sur la `aspnet-codegenerator razorpage` commande :</span><span class="sxs-lookup"><span data-stu-id="f6201-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="f6201-196">Pour plus d’informations, consultez [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="f6201-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="f6201-197">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="f6201-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="f6201-198">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="f6201-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="f6201-199">Le code suivant montre comment injecter <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans `Startup` .</span><span class="sxs-lookup"><span data-stu-id="f6201-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="f6201-200">`IWebHostEnvironment` est injecté afin que l’application puisse utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="f6201-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6201-201">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f6201-202">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="f6201-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="f6201-203">Cliquez avec le contrôle sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f6201-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="f6201-204">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="f6201-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="f6201-205">Cliquez avec le contrôle sur le dossier *pages/movies* > **Ajouter** une > **nouvelle génération de modèles automatique...**.</span><span class="sxs-lookup"><span data-stu-id="f6201-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

1. <span data-ttu-id="f6201-207">Dans la boîte de dialogue **nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f6201-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="f6201-209">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="f6201-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="f6201-210">Dans la **classe DbContext à utiliser :** Row, nommez la classe `RazorPagesMovie.Data.RazorPagesMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="f6201-210">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="f6201-211">Sélectionnez **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="f6201-211">Select **Finish**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/5/arpMac.png)

<span data-ttu-id="f6201-213">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="f6201-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="f6201-214">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="f6201-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="f6201-215">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="f6201-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="f6201-216">Le code suivant montre comment injecter <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans `Startup` .</span><span class="sxs-lookup"><span data-stu-id="f6201-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="f6201-217">`IWebHostEnvironment` est injecté afin que l’application puisse utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="f6201-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="f6201-218">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="f6201-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6201-220">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="f6201-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="f6201-221">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="f6201-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="f6201-222">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="f6201-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="f6201-223">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="f6201-223">Updated</span></span>

* <span data-ttu-id="f6201-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="f6201-224">*Startup.cs*</span></span>

<span data-ttu-id="f6201-225">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="f6201-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6201-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6201-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f6201-227">Le processus de génération de modèles automatique crée les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="f6201-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="f6201-228">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="f6201-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="f6201-229">Les fichiers créés sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="f6201-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6201-230">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f6201-231">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="f6201-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="f6201-232">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="f6201-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="f6201-233">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="f6201-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="f6201-234">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="f6201-234">Updated</span></span>

* <span data-ttu-id="f6201-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="f6201-235">*Startup.cs*</span></span>

<span data-ttu-id="f6201-236">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="f6201-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="f6201-237">Créer le schéma de base de données initial à l’aide de la fonctionnalité de migration d’EF</span><span class="sxs-lookup"><span data-stu-id="f6201-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="f6201-238">La fonctionnalité migrations de Entity Framework Core offre un moyen d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="f6201-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="f6201-239">Créez le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="f6201-239">Create the initial database schema.</span></span>
* <span data-ttu-id="f6201-240">Mettez à jour de façon incrémentielle le schéma de base de données pour le maintenir synchronisé avec le modèle de données de l’application.</span><span class="sxs-lookup"><span data-stu-id="f6201-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="f6201-241">Les données existantes de la base de données sont conservées.</span><span class="sxs-lookup"><span data-stu-id="f6201-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6201-243">Dans cette section, la fenêtre **console du gestionnaire de package** (PMC) permet d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="f6201-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="f6201-244">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="f6201-244">Add an initial migration.</span></span>
* <span data-ttu-id="f6201-245">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="f6201-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="f6201-246">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="f6201-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menu Console du Gestionnaire de package](model/_static/5/pmc.png)

1. <span data-ttu-id="f6201-248">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f6201-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6201-249">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="f6201-250">Exécutez les commandes CLI .NET suivantes :</span><span class="sxs-lookup"><span data-stu-id="f6201-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="f6201-251">Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ».</span><span class="sxs-lookup"><span data-stu-id="f6201-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="f6201-252">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="f6201-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="f6201-253">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »</span><span class="sxs-lookup"><span data-stu-id="f6201-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="f6201-254">Ignorez l’avertissement, car il sera traité dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="f6201-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="f6201-255">La commande `migrations` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="f6201-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="f6201-256">Le schéma est basé sur le modèle spécifié dans `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="f6201-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="f6201-257">L’argument `InitialCreate` est utilisé pour nommer les migrations.</span><span class="sxs-lookup"><span data-stu-id="f6201-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="f6201-258">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="f6201-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="f6201-259">La `update` commande exécute la `Up` méthode dans des migrations qui n’ont pas été appliquées.</span><span class="sxs-lookup"><span data-stu-id="f6201-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="f6201-260">Dans ce cas, `update` exécute la `Up` méthode dans le fichier *migrations/ \<time-stamp> _InitialCreate. cs* , qui crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="f6201-262">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="f6201-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="f6201-263">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f6201-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f6201-264">Les services, tels que le contexte de base de données EF Core, sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="f6201-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="f6201-265">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="f6201-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f6201-266">Le code de constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="f6201-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f6201-267">L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit auprès du conteneur d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="f6201-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="f6201-268">Examinez la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f6201-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f6201-269">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="f6201-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="f6201-270">Les `RazorPagesMovieContext` coordonnées EF Core fonctionnalité, telles que la création, la lecture, la mise à jour et la suppression, pour le `Movie` modèle.</span><span class="sxs-lookup"><span data-stu-id="f6201-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="f6201-271">Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="f6201-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="f6201-272">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="f6201-273">Le code précédent crée une [propriété \<Movie> DbSet](xref:Microsoft.EntityFrameworkCore.DbSet%601) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="f6201-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="f6201-274">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="f6201-275">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="f6201-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f6201-276">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="f6201-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="f6201-277">Pour le développement local, le [système de configuration](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="f6201-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6201-278">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f6201-279">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="f6201-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="f6201-280">Test de l'application</span><span class="sxs-lookup"><span data-stu-id="f6201-280">Test the app</span></span>

1. <span data-ttu-id="f6201-281">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="f6201-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="f6201-282">Si vous recevez l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="f6201-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="f6201-283">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="f6201-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="f6201-284">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f6201-284">Test the **Create** link.</span></span>

   ![Create page](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="f6201-286">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="f6201-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f6201-287">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="f6201-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="f6201-288">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="f6201-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="f6201-289">Testez les liens **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="f6201-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="f6201-290">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="f6201-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f6201-291">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f6201-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f6201-292">[Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="f6201-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="f6201-293">Dans cette section, des classes sont ajoutées pour la gestion des films.</span><span class="sxs-lookup"><span data-stu-id="f6201-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="f6201-294">Les classes de modèle de l’application utilisent [Entity Framework Core (EF Core)](/ef/core) pour travailler avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="f6201-295">EF Core est un mappeur objet-relationnel (O/RM) qui simplifie l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="f6201-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="f6201-296">Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core.</span><span class="sxs-lookup"><span data-stu-id="f6201-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="f6201-297">Elles définissent les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="f6201-298">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f6201-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="f6201-299">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="f6201-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6201-301">Cliquez avec le bouton droit sur le projet **Razor PagesMovie** > **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f6201-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="f6201-302">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="f6201-302">Name the folder *Models*.</span></span>

<span data-ttu-id="f6201-303">Cliquez avec le bouton droit sur le dossier *modèles* .</span><span class="sxs-lookup"><span data-stu-id="f6201-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="f6201-304">Sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="f6201-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="f6201-305">Nommez la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="f6201-305">Name the class **Movie**.</span></span>

<span data-ttu-id="f6201-306">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="f6201-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f6201-307">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="f6201-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="f6201-308">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="f6201-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f6201-309">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="f6201-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f6201-310">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="f6201-310">With this attribute:</span></span>

  * <span data-ttu-id="f6201-311">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="f6201-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f6201-312">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="f6201-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="f6201-313">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="f6201-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6201-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6201-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6201-315">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="f6201-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="f6201-316">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="f6201-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="f6201-317">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="f6201-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f6201-318">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="f6201-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="f6201-319">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="f6201-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f6201-320">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="f6201-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f6201-321">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="f6201-321">With this attribute:</span></span>

  * <span data-ttu-id="f6201-322">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="f6201-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f6201-323">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="f6201-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="f6201-324">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="f6201-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="f6201-325">Ajouter des packages NuGet des outils EF</span><span class="sxs-lookup"><span data-stu-id="f6201-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="f6201-326">Ajouter une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="f6201-326">Add a database context class</span></span>

* <span data-ttu-id="f6201-327">Dans le projet *Razor PagesMovie* , créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="f6201-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="f6201-328">Ajoutez la classe `RazorPagesMovieContext` suivante au dossier *Data* :</span><span class="sxs-lookup"><span data-stu-id="f6201-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="f6201-329">Le code précédent crée une propriété `DbSet` pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="f6201-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="f6201-330">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="f6201-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="f6201-331">Le code n’est pas compilé tant que les dépendances n’ont pas été ajoutées dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="f6201-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="f6201-332">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="f6201-332">Add a database connection string</span></span>

<span data-ttu-id="f6201-333">Ajoutez une chaîne de connexion au *appsettings.json* fichier comme indiqué dans le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="f6201-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="f6201-334">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="f6201-334">Register the database context</span></span>

<span data-ttu-id="f6201-335">En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="f6201-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="f6201-336">Inscrivez le contexte de base de données auprès du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f6201-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6201-337">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f6201-338">Dans la **fenêtre outil de solution**, cliquez avec le contrôle sur le projet **Razor PagesMovie** , puis sélectionnez **Ajouter** > **un nouveau dossier...**. Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="f6201-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="f6201-339">Cliquez avec le bouton droit sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier...**.</span><span class="sxs-lookup"><span data-stu-id="f6201-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="f6201-340">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="f6201-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="f6201-341">Dans le volet gauche, sélectionnez **Général**.</span><span class="sxs-lookup"><span data-stu-id="f6201-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="f6201-342">Dans le volet central, sélectionnez **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="f6201-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="f6201-343">Nommez la classe **Movie**, puis sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="f6201-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="f6201-344">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="f6201-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f6201-345">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="f6201-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="f6201-346">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="f6201-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f6201-347">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="f6201-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f6201-348">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="f6201-348">With this attribute:</span></span>

  * <span data-ttu-id="f6201-349">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="f6201-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f6201-350">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="f6201-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="f6201-351">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="f6201-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="f6201-352">Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="f6201-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="f6201-353">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="f6201-353">Scaffold the movie model</span></span>

<span data-ttu-id="f6201-354">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="f6201-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="f6201-355">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="f6201-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6201-357">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="f6201-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="f6201-358">Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f6201-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="f6201-359">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="f6201-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="f6201-360">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="f6201-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/sca.png)

<span data-ttu-id="f6201-362">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f6201-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

<span data-ttu-id="f6201-364">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="f6201-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="f6201-365">Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="f6201-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="f6201-366">Dans la ligne de la **classe de contexte de données** , sélectionnez le **+** signe (plus), puis modifiez le nom généré à partir de Razor PagesMovie.**Modèles**. Razor PagesMovieContext à Razor PagesMovie.**Données**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="f6201-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="f6201-367">[Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise.</span><span class="sxs-lookup"><span data-stu-id="f6201-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="f6201-368">Elle crée la classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="f6201-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="f6201-369">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f6201-369">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/3/arp.png)

<span data-ttu-id="f6201-371">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="f6201-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6201-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6201-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="f6201-373">Ouvrez une fenêtre de commande dans le répertoire du projet, qui contient les fichiers *Program.cs*, *Startup.cs* et *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="f6201-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="f6201-374">**Pour Windows**: exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f6201-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="f6201-375">**Pour macOS et Linux**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f6201-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="f6201-376">Le tableau suivant détaille les options du générateur de code ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="f6201-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="f6201-377">Option</span><span class="sxs-lookup"><span data-stu-id="f6201-377">Option</span></span>               | <span data-ttu-id="f6201-378">Description</span><span class="sxs-lookup"><span data-stu-id="f6201-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="f6201-379">Nom du modèle.</span><span class="sxs-lookup"><span data-stu-id="f6201-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="f6201-380">Classe `DbContext` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="f6201-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="f6201-381">Utiliser la disposition par défaut.</span><span class="sxs-lookup"><span data-stu-id="f6201-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="f6201-382">Chemin du dossier de sortie relatif pour créer les vues.</span><span class="sxs-lookup"><span data-stu-id="f6201-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="f6201-383">Ajoute `_ValidationScriptsPartial` aux pages Modifier et Créer.</span><span class="sxs-lookup"><span data-stu-id="f6201-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="f6201-384">Utilisez l' `-h` option pour obtenir de l’aide sur la `aspnet-codegenerator razorpage` commande :</span><span class="sxs-lookup"><span data-stu-id="f6201-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="f6201-385">Pour plus d’informations, consultez [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="f6201-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="f6201-386">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="f6201-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="f6201-387">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="f6201-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="f6201-388">Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage.</span><span class="sxs-lookup"><span data-stu-id="f6201-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="f6201-389">`IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="f6201-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6201-390">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f6201-391">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="f6201-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="f6201-392">Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f6201-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="f6201-393">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="f6201-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="f6201-394">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** une > **nouvelle génération de modèles automatique...**.</span><span class="sxs-lookup"><span data-stu-id="f6201-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

<span data-ttu-id="f6201-396">Dans la boîte de dialogue **nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f6201-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="f6201-398">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="f6201-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="f6201-399">Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="f6201-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="f6201-400">Dans la ligne de la **classe de contexte de données** , tapez le nom de la nouvelle classe, Razor PagesMovie.**Données**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="f6201-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="f6201-401">[Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise.</span><span class="sxs-lookup"><span data-stu-id="f6201-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="f6201-402">Elle crée la classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="f6201-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="f6201-403">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f6201-403">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

<span data-ttu-id="f6201-405">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="f6201-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="f6201-406">Ajouter des outils EF</span><span class="sxs-lookup"><span data-stu-id="f6201-406">Add EF tools</span></span>

<span data-ttu-id="f6201-407">Exécutez la commande CLI .NET Core suivante :</span><span class="sxs-lookup"><span data-stu-id="f6201-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="f6201-408">La commande précédente ajoute les outils de Entity Framework Core pour le CLI .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6201-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="f6201-409">Pour plus d’informations, consultez [référence des outils de Entity Framework Core-CLI .net Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="f6201-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="f6201-410">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="f6201-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="f6201-411">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="f6201-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="f6201-412">Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage.</span><span class="sxs-lookup"><span data-stu-id="f6201-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="f6201-413">`IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="f6201-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="f6201-414">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="f6201-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6201-416">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="f6201-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="f6201-417">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="f6201-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="f6201-418">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="f6201-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="f6201-419">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="f6201-419">Updated</span></span>

* <span data-ttu-id="f6201-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="f6201-420">*Startup.cs*</span></span>

<span data-ttu-id="f6201-421">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="f6201-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6201-422">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f6201-423">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="f6201-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="f6201-424">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="f6201-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="f6201-425">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="f6201-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="f6201-426">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="f6201-426">Updated</span></span>

* <span data-ttu-id="f6201-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="f6201-427">*Startup.cs*</span></span>

<span data-ttu-id="f6201-428">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="f6201-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6201-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6201-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f6201-430">Le processus de génération de modèles automatique crée les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="f6201-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="f6201-431">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="f6201-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="f6201-432">Les fichiers créés sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="f6201-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="f6201-433">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="f6201-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6201-435">Dans cette section, la console du gestionnaire de package est utilisée pour :</span><span class="sxs-lookup"><span data-stu-id="f6201-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="f6201-436">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="f6201-436">Add an initial migration.</span></span>
* <span data-ttu-id="f6201-437">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="f6201-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="f6201-438">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="f6201-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="f6201-440">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f6201-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6201-441">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="f6201-442">Exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="f6201-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="f6201-443">Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ».</span><span class="sxs-lookup"><span data-stu-id="f6201-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="f6201-444">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="f6201-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="f6201-445">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »</span><span class="sxs-lookup"><span data-stu-id="f6201-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="f6201-446">Ignorez l’avertissement, car il sera traité dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="f6201-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="f6201-447">La commande migrations génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="f6201-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="f6201-448">Le schéma est basé sur le modèle spécifié dans `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="f6201-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="f6201-449">L’argument `InitialCreate` est utilisé pour nommer les migrations.</span><span class="sxs-lookup"><span data-stu-id="f6201-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="f6201-450">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="f6201-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="f6201-451">La `update` commande exécute la `Up` méthode dans des migrations qui n’ont pas été appliquées.</span><span class="sxs-lookup"><span data-stu-id="f6201-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="f6201-452">Dans ce cas, `update` exécute la `Up` méthode dans le fichier  *migrations/ \<time-stamp> _InitialCreate. cs* , qui crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="f6201-454">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="f6201-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="f6201-455">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f6201-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f6201-456">Les services, tels que le contexte de contexte de la base de données EF Core, sont inscrits avec l’injection de dépendance pendant le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="f6201-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="f6201-457">Ces services sont fournis par les composants qui requièrent ces services, tels que les Razor pages, via des paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="f6201-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="f6201-458">Le code du constructeur qui obtient une instance de contexte de contexte de base de données est indiqué plus loin dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="f6201-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f6201-459">L’outil de génération de modèles automatique a créé automatiquement un contexte de contexte de base de données et l’a inscrit auprès du conteneur d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="f6201-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="f6201-460">Examinez la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f6201-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f6201-461">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="f6201-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="f6201-462">Les `RazorPagesMovieContext` coordonnées EF Core fonctionnalité, telles que la création, la lecture, la mise à jour et la suppression, pour le `Movie` modèle.</span><span class="sxs-lookup"><span data-stu-id="f6201-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="f6201-463">Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="f6201-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="f6201-464">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="f6201-465">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="f6201-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="f6201-466">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="f6201-467">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="f6201-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f6201-468">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="f6201-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="f6201-469">Pour le développement local, le [système de configuration](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="f6201-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6201-470">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f6201-471">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="f6201-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="f6201-472">Test de l'application</span><span class="sxs-lookup"><span data-stu-id="f6201-472">Test the app</span></span>

* <span data-ttu-id="f6201-473">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="f6201-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="f6201-474">Si vous obtenez cette erreur :</span><span class="sxs-lookup"><span data-stu-id="f6201-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="f6201-475">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="f6201-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="f6201-476">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f6201-476">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="f6201-478">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="f6201-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f6201-479">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="f6201-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="f6201-480">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="f6201-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="f6201-481">Testez les liens **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="f6201-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="f6201-482">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="f6201-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f6201-483">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f6201-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f6201-484">[Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="f6201-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f6201-485">Dans cette section, des classes sont ajoutées pour la gestion des films dans une [base de données SQLite](https://www.sqlite.org/index.html)multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="f6201-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="f6201-486">Les applications créées à partir d’un modèle de ASP.NET Core utilisent une base de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="f6201-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="f6201-487">Les classes de modèle de l’application sont utilisées avec [Entity Framework Core (EF Core)](/ef/core) ([fournisseur de base de données SQLite EF Core](/ef/core/providers/sqlite)) pour fonctionner avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="f6201-488">EF Core est un framework de mappage relationnel d’objets qui simplifie l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="f6201-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="f6201-489">Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core.</span><span class="sxs-lookup"><span data-stu-id="f6201-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="f6201-490">Elles définissent les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="f6201-491">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f6201-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="f6201-492">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="f6201-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6201-494">Cliquez avec le bouton droit sur le projet **Razor PagesMovie** > **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f6201-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="f6201-495">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="f6201-495">Name the folder *Models*.</span></span>

<span data-ttu-id="f6201-496">Cliquez avec le bouton droit sur le dossier *modèles* .</span><span class="sxs-lookup"><span data-stu-id="f6201-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="f6201-497">Sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="f6201-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="f6201-498">Nommez la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="f6201-498">Name the class **Movie**.</span></span>

<span data-ttu-id="f6201-499">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="f6201-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f6201-500">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="f6201-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="f6201-501">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="f6201-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f6201-502">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="f6201-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f6201-503">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="f6201-503">With this attribute:</span></span>

  * <span data-ttu-id="f6201-504">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="f6201-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f6201-505">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="f6201-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="f6201-506">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="f6201-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6201-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6201-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6201-508">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="f6201-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="f6201-509">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="f6201-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="f6201-510">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="f6201-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f6201-511">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="f6201-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="f6201-512">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="f6201-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f6201-513">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="f6201-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f6201-514">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="f6201-514">With this attribute:</span></span>

  * <span data-ttu-id="f6201-515">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="f6201-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f6201-516">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="f6201-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="f6201-517">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="f6201-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="f6201-518">Ajouter des packages NuGet des outils EF</span><span class="sxs-lookup"><span data-stu-id="f6201-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="f6201-519">Ajouter une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="f6201-519">Add a database context class</span></span>

<span data-ttu-id="f6201-520">Dans le Razor projet PagesMovie, créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="f6201-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="f6201-521">Ajoutez la classe `RazorPagesMovieContext` suivante au dossier *Data* :</span><span class="sxs-lookup"><span data-stu-id="f6201-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="f6201-522">Le code précédent crée une propriété `DbSet` pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="f6201-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="f6201-523">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="f6201-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="f6201-524">Le code n’est pas compilé tant que les dépendances n’ont pas été ajoutées dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="f6201-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="f6201-525">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="f6201-525">Add a database connection string</span></span>

<span data-ttu-id="f6201-526">Ajoutez une chaîne de connexion au *appsettings.json* fichier comme indiqué dans le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="f6201-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="f6201-527">Ajouter les packages NuGet exigés</span><span class="sxs-lookup"><span data-stu-id="f6201-527">Add required NuGet packages</span></span>

<span data-ttu-id="f6201-528">Exécutez la commande CLI .NET Core suivante pour ajouter SQLite et CodeGeneration. Design au projet :</span><span class="sxs-lookup"><span data-stu-id="f6201-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="f6201-529">Le package `Microsoft.VisualStudio.Web.CodeGeneration.Design` est nécessaire à la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="f6201-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="f6201-530">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="f6201-530">Register the database context</span></span>

<span data-ttu-id="f6201-531">En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="f6201-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="f6201-532">Inscrivez le contexte de base de données auprès du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f6201-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="f6201-533">Générez le projet en tant que vérification des erreurs.</span><span class="sxs-lookup"><span data-stu-id="f6201-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6201-534">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f6201-535">Dans la **fenêtre outil** de la solution, cliquez avec le contrôle sur le projet *Razor PagesMovie* , puis sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f6201-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="f6201-536">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="f6201-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="f6201-537">Cliquez avec le contrôle sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier**.</span><span class="sxs-lookup"><span data-stu-id="f6201-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="f6201-538">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="f6201-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="f6201-539">Dans le volet gauche, sélectionnez **Général**.</span><span class="sxs-lookup"><span data-stu-id="f6201-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="f6201-540">Dans le volet central, sélectionnez **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="f6201-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="f6201-541">Nommez la classe **Movie**, puis sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="f6201-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="f6201-542">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="f6201-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f6201-543">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="f6201-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="f6201-544">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="f6201-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f6201-545">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="f6201-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f6201-546">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="f6201-546">With this attribute:</span></span>

  * <span data-ttu-id="f6201-547">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="f6201-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f6201-548">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="f6201-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="f6201-549">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="f6201-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="f6201-550">Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="f6201-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="f6201-551">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="f6201-551">Scaffold the movie model</span></span>

<span data-ttu-id="f6201-552">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="f6201-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="f6201-553">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="f6201-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6201-555">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="f6201-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="f6201-556">Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f6201-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="f6201-557">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="f6201-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="f6201-558">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="f6201-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/sca.png)

<span data-ttu-id="f6201-560">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f6201-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

<span data-ttu-id="f6201-562">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="f6201-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="f6201-563">Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="f6201-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="f6201-564">Dans la ligne de la **classe de contexte de données** , sélectionnez le **+** signe (plus) et acceptez le nom généré **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="f6201-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="f6201-565">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f6201-565">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/arp.png)

<span data-ttu-id="f6201-567">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="f6201-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6201-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6201-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="f6201-569">Ouvrez une fenêtre de commande dans le répertoire du projet, qui contient les fichiers *Program.cs*, *Startup.cs* et *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="f6201-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="f6201-570">**Pour Windows**: exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f6201-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="f6201-571">**Pour macOS et Linux**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f6201-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="f6201-572">Le tableau suivant détaille les options du générateur de code ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="f6201-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="f6201-573">Option</span><span class="sxs-lookup"><span data-stu-id="f6201-573">Option</span></span>               | <span data-ttu-id="f6201-574">Description</span><span class="sxs-lookup"><span data-stu-id="f6201-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="f6201-575">Nom du modèle.</span><span class="sxs-lookup"><span data-stu-id="f6201-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="f6201-576">Classe `DbContext` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="f6201-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="f6201-577">Utiliser la disposition par défaut.</span><span class="sxs-lookup"><span data-stu-id="f6201-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="f6201-578">Chemin du dossier de sortie relatif pour créer les vues.</span><span class="sxs-lookup"><span data-stu-id="f6201-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="f6201-579">Ajoute `_ValidationScriptsPartial` aux pages Modifier et Créer.</span><span class="sxs-lookup"><span data-stu-id="f6201-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="f6201-580">Utilisez l' `-h` option pour obtenir de l’aide sur la `aspnet-codegenerator razorpage` commande :</span><span class="sxs-lookup"><span data-stu-id="f6201-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="f6201-581">Pour plus d’informations, consultez [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="f6201-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6201-582">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f6201-583">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="f6201-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="f6201-584">Cliquez avec le contrôle sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f6201-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="f6201-585">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="f6201-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="f6201-586">Cliquez avec le contrôle sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="f6201-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

<span data-ttu-id="f6201-588">Dans la boîte de dialogue **Ajouter une nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f6201-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="f6201-590">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="f6201-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="f6201-591">Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie**.</span><span class="sxs-lookup"><span data-stu-id="f6201-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="f6201-592">Dans la ligne de la **classe de contexte de données** , tapez Select the **Razor PagesMovieContext** This crée une nouvelle classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="f6201-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="f6201-593">Dans ce cas, il s’agit de **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="f6201-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="f6201-594">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f6201-594">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

<span data-ttu-id="f6201-596">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="f6201-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="f6201-597">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="f6201-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="f6201-598">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="f6201-598">Files created</span></span>

* <span data-ttu-id="f6201-599">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="f6201-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="f6201-600">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="f6201-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="f6201-601">Fichier mis à jour</span><span class="sxs-lookup"><span data-stu-id="f6201-601">File updated</span></span>

* <span data-ttu-id="f6201-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="f6201-602">*Startup.cs*</span></span>

<span data-ttu-id="f6201-603">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="f6201-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="f6201-604">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="f6201-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6201-606">Dans cette section, la console du gestionnaire de package est utilisée pour :</span><span class="sxs-lookup"><span data-stu-id="f6201-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="f6201-607">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="f6201-607">Add an initial migration.</span></span>
* <span data-ttu-id="f6201-608">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="f6201-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="f6201-609">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="f6201-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="f6201-611">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f6201-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="f6201-612">La commande `Add-Migration` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="f6201-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="f6201-613">Le schéma est basé sur le modèle spécifié dans le `DbContext` , dans le fichier *Razor PagesMovieContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="f6201-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="f6201-614">L' `InitialCreate` argument est utilisé pour nommer la migration.</span><span class="sxs-lookup"><span data-stu-id="f6201-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="f6201-615">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est utilisé.</span><span class="sxs-lookup"><span data-stu-id="f6201-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="f6201-616">Pour plus d’informations, consultez <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="f6201-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="f6201-617">La `Update-Database` commande exécute la `Up` méthode dans le fichier *migrations/ \<time-stamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="f6201-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="f6201-618">La méthode `Up` crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6201-619">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="f6201-620">Exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="f6201-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="f6201-621">Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ».</span><span class="sxs-lookup"><span data-stu-id="f6201-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="f6201-622">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="f6201-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="f6201-623">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »</span><span class="sxs-lookup"><span data-stu-id="f6201-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="f6201-624">Ignorez l’avertissement, car il sera traité dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="f6201-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6201-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6201-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="f6201-626">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="f6201-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="f6201-627">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f6201-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f6201-628">Les services (tels que le contexte de contexte de la base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="f6201-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="f6201-629">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="f6201-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f6201-630">Le code du constructeur qui obtient une instance de contexte de contextB du contexte de base de données est indiqué plus loin dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="f6201-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f6201-631">L’outil de génération de modèles automatique a créé automatiquement un contexte de contexte de base de données et l’a inscrit auprès du conteneur d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="f6201-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="f6201-632">Examinez la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f6201-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f6201-633">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="f6201-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="f6201-634">Les `RazorPagesMovieContext` coordonnées EF Core fonctionnalité, telles que la création, la lecture, la mise à jour et la suppression, pour le `Movie` modèle.</span><span class="sxs-lookup"><span data-stu-id="f6201-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="f6201-635">Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="f6201-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="f6201-636">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="f6201-637">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="f6201-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="f6201-638">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="f6201-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="f6201-639">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="f6201-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f6201-640">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="f6201-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="f6201-641">Pour le développement local, le [système de configuration](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="f6201-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6201-642">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f6201-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f6201-643">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="f6201-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="f6201-644">Test de l'application</span><span class="sxs-lookup"><span data-stu-id="f6201-644">Test the app</span></span>

* <span data-ttu-id="f6201-645">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="f6201-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="f6201-646">Si vous obtenez cette erreur :</span><span class="sxs-lookup"><span data-stu-id="f6201-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="f6201-647">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="f6201-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="f6201-648">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f6201-648">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="f6201-650">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="f6201-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f6201-651">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="f6201-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="f6201-652">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="f6201-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="f6201-653">Testez les liens **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="f6201-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="f6201-654">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="f6201-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f6201-655">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f6201-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f6201-656">[Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="f6201-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
