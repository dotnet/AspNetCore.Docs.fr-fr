---
title: Partie 2, ajouter un modèle
author: rick-anderson
description: Partie 2 de la série de didacticiels sur les Razor pages. Dans cette section, des classes de modèle sont ajoutées.
ms.author: riande
ms.date: 03/10/2021
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
ms.openlocfilehash: defbc73d0c1d6aac30360cd7b83cc518a407bf98
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413442"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="0275f-104">Partie 2, ajouter un modèle à une Razor application pages dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="0275f-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="0275f-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0275f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="0275f-106">Dans cette section, des classes sont ajoutées pour la gestion des films dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="0275f-107">Les classes de modèle de l’application utilisent [Entity Framework Core (EF Core)](/ef/core) pour travailler avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="0275f-108">EF Core est un mappeur objet-relationnel (O/RM) qui simplifie l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="0275f-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="0275f-109">Vous écrivez d’abord les classes de modèle, et EF Core crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="0275f-110">Les classes de modèle sont appelées classes POCO (à partir de «**P** Lain-**O** LD **C** LR **O** bjets »), car elles n’ont pas de dépendance sur EF Core.</span><span class="sxs-lookup"><span data-stu-id="0275f-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="0275f-111">Elles définissent les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="0275f-112">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0275f-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="0275f-113">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="0275f-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0275f-115">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet *Razor PagesMovie* > **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="0275f-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="0275f-116">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="0275f-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="0275f-117">Cliquez avec le bouton droit sur le dossier *modèles* .</span><span class="sxs-lookup"><span data-stu-id="0275f-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="0275f-118">Sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="0275f-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="0275f-119">Nommez la classe *Movie*.</span><span class="sxs-lookup"><span data-stu-id="0275f-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="0275f-120">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="0275f-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0275f-121">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="0275f-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="0275f-122">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="0275f-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0275f-123">`[DataType(DataType.Date)]`: L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0275f-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0275f-124">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="0275f-124">With this attribute:</span></span>

  * <span data-ttu-id="0275f-125">L’utilisateur n’est pas obligé d’entrer des informations d’heure dans le champ date.</span><span class="sxs-lookup"><span data-stu-id="0275f-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0275f-126">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="0275f-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0275f-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0275f-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0275f-128">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="0275f-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="0275f-129">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="0275f-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="0275f-130">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="0275f-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0275f-131">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="0275f-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="0275f-132">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="0275f-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0275f-133">`[DataType(DataType.Date)]`: L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0275f-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0275f-134">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="0275f-134">With this attribute:</span></span>

  * <span data-ttu-id="0275f-135">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="0275f-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0275f-136">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="0275f-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="0275f-137">Ajouter des packages NuGet des outils EF</span><span class="sxs-lookup"><span data-stu-id="0275f-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0275f-138">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0275f-139">Dans la **fenêtre outil de solution**, cliquez avec le contrôle sur le projet *Razor PagesMovie* , puis sélectionnez **Ajouter** > **un nouveau dossier...**. Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="0275f-139">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="0275f-140">Cliquez avec le contrôle sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier...**.</span><span class="sxs-lookup"><span data-stu-id="0275f-140">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="0275f-141">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="0275f-141">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="0275f-142">Dans le volet gauche, sélectionnez **Général**.</span><span class="sxs-lookup"><span data-stu-id="0275f-142">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="0275f-143">Dans le volet central, sélectionnez **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="0275f-143">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="0275f-144">Nommez la classe **Movie**, puis sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="0275f-144">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="0275f-145">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="0275f-145">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0275f-146">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="0275f-146">The `Movie` class contains:</span></span>

* <span data-ttu-id="0275f-147">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="0275f-147">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0275f-148">`[DataType(DataType.Date)]`: L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0275f-148">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0275f-149">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="0275f-149">With this attribute:</span></span>

  * <span data-ttu-id="0275f-150">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="0275f-150">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0275f-151">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="0275f-151">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="0275f-152">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="0275f-152">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="0275f-153">Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="0275f-153">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="0275f-154">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="0275f-154">Scaffold the movie model</span></span>

<span data-ttu-id="0275f-155">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="0275f-155">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="0275f-156">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="0275f-156">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-157">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0275f-158">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="0275f-158">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="0275f-159">Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="0275f-159">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="0275f-160">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="0275f-160">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="0275f-161">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="0275f-161">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/5/sca.png)

1. <span data-ttu-id="0275f-163">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="0275f-163">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

1. <span data-ttu-id="0275f-165">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0275f-165">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="0275f-166">Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="0275f-166">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="0275f-167">Dans la ligne **Classe du contexte de données**, sélectionnez le signe **+** (plus).</span><span class="sxs-lookup"><span data-stu-id="0275f-167">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="0275f-168">Dans la boîte de dialogue **Ajouter un contexte de données** , le nom de la classe `RazorPagesMovie.Data.RazorPagesMovieContext` est généré.</span><span class="sxs-lookup"><span data-stu-id="0275f-168">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="0275f-169">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="0275f-169">Select **Add**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/3/arp.png)

<span data-ttu-id="0275f-171">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="0275f-171">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0275f-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0275f-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="0275f-173">Ouvrez une interface de commande dans le répertoire du projet, qui contient les fichiers *Program.cs*, *Startup.cs* et *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0275f-173">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="0275f-174">**Pour Windows**: exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="0275f-174">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="0275f-175">**Pour macOS et Linux**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="0275f-175">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="0275f-176">Le tableau suivant détaille les options du générateur de code ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0275f-176">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="0275f-177">Option</span><span class="sxs-lookup"><span data-stu-id="0275f-177">Option</span></span>               | <span data-ttu-id="0275f-178">Description</span><span class="sxs-lookup"><span data-stu-id="0275f-178">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="0275f-179">Nom du modèle.</span><span class="sxs-lookup"><span data-stu-id="0275f-179">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="0275f-180">Classe `DbContext` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="0275f-180">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="0275f-181">Utiliser la disposition par défaut.</span><span class="sxs-lookup"><span data-stu-id="0275f-181">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="0275f-182">Chemin du dossier de sortie relatif pour créer les vues.</span><span class="sxs-lookup"><span data-stu-id="0275f-182">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="0275f-183">Ajoute `_ValidationScriptsPartial` aux pages Modifier et Créer.</span><span class="sxs-lookup"><span data-stu-id="0275f-183">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="0275f-184">Utilisez l' `-h` option pour obtenir de l’aide sur la `aspnet-codegenerator razorpage` commande :</span><span class="sxs-lookup"><span data-stu-id="0275f-184">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="0275f-185">Pour plus d’informations, consultez [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="0275f-185">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="0275f-186">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="0275f-186">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="0275f-187">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="0275f-187">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="0275f-188">Le code suivant montre comment injecter <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans `Startup` .</span><span class="sxs-lookup"><span data-stu-id="0275f-188">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="0275f-189">`IWebHostEnvironment` est injecté afin que l’application puisse utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="0275f-189">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0275f-190">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0275f-191">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="0275f-191">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="0275f-192">Cliquez avec le contrôle sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="0275f-192">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="0275f-193">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="0275f-193">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="0275f-194">Cliquez avec le contrôle sur le dossier *pages/movies* > **Ajouter** une > **nouvelle génération de modèles automatique...**.</span><span class="sxs-lookup"><span data-stu-id="0275f-194">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

1. <span data-ttu-id="0275f-196">Dans la boîte de dialogue **nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="0275f-196">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="0275f-198">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0275f-198">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="0275f-199">Dans la **classe DbContext à utiliser :** Row, nommez la classe `RazorPagesMovie.Data.RazorPagesMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="0275f-199">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="0275f-200">Sélectionnez **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="0275f-200">Select **Finish**.</span></span>

   ![Image illustrant les instructions précédentes.](model/_static/5/arpMac.png)

<span data-ttu-id="0275f-202">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="0275f-202">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="0275f-203">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="0275f-203">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="0275f-204">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="0275f-204">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="0275f-205">Le code suivant montre comment injecter <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans `Startup` .</span><span class="sxs-lookup"><span data-stu-id="0275f-205">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="0275f-206">`IWebHostEnvironment` est injecté afin que l’application puisse utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="0275f-206">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="0275f-207">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="0275f-207">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-208">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0275f-209">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="0275f-209">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="0275f-210">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="0275f-210">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0275f-211">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0275f-211">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="0275f-212">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="0275f-212">Updated</span></span>

* <span data-ttu-id="0275f-213">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0275f-213">*Startup.cs*</span></span>

<span data-ttu-id="0275f-214">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="0275f-214">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0275f-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0275f-215">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0275f-216">Le processus de génération de modèles automatique crée les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="0275f-216">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="0275f-217">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="0275f-217">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="0275f-218">Les fichiers créés sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="0275f-218">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0275f-219">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0275f-220">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="0275f-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="0275f-221">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="0275f-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0275f-222">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0275f-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="0275f-223">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="0275f-223">Updated</span></span>

* <span data-ttu-id="0275f-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0275f-224">*Startup.cs*</span></span>

<span data-ttu-id="0275f-225">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="0275f-225">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="0275f-226">Créer le schéma de base de données initial à l’aide de la fonctionnalité de migration d’EF</span><span class="sxs-lookup"><span data-stu-id="0275f-226">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="0275f-227">La fonctionnalité migrations de Entity Framework Core offre un moyen d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="0275f-227">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="0275f-228">Créez le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="0275f-228">Create the initial database schema.</span></span>
* <span data-ttu-id="0275f-229">Mettez à jour de façon incrémentielle le schéma de base de données pour le maintenir synchronisé avec le modèle de données de l’application.</span><span class="sxs-lookup"><span data-stu-id="0275f-229">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="0275f-230">Les données existantes de la base de données sont conservées.</span><span class="sxs-lookup"><span data-stu-id="0275f-230">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0275f-232">Dans cette section, la fenêtre **console du gestionnaire de package** (PMC) permet d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="0275f-232">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="0275f-233">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="0275f-233">Add an initial migration.</span></span>
* <span data-ttu-id="0275f-234">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="0275f-234">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="0275f-235">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="0275f-235">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menu Console du Gestionnaire de package](model/_static/5/pmc.png)

1. <span data-ttu-id="0275f-237">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="0275f-237">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0275f-238">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="0275f-239">Exécutez les commandes CLI .NET suivantes :</span><span class="sxs-lookup"><span data-stu-id="0275f-239">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="0275f-240">Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ».</span><span class="sxs-lookup"><span data-stu-id="0275f-240">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="0275f-241">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="0275f-241">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="0275f-242">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »</span><span class="sxs-lookup"><span data-stu-id="0275f-242">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="0275f-243">Ignorez l’avertissement, car il sera traité dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="0275f-243">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="0275f-244">La commande `migrations` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="0275f-244">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="0275f-245">Le schéma est basé sur le modèle spécifié dans `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="0275f-245">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="0275f-246">L’argument `InitialCreate` est utilisé pour nommer les migrations.</span><span class="sxs-lookup"><span data-stu-id="0275f-246">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="0275f-247">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="0275f-247">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="0275f-248">La `update` commande exécute la `Up` méthode dans des migrations qui n’ont pas été appliquées.</span><span class="sxs-lookup"><span data-stu-id="0275f-248">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="0275f-249">Dans ce cas, `update` exécute la `Up` méthode dans le fichier *migrations/ \<time-stamp> _InitialCreate. cs* , qui crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-249">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-250">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0275f-251">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="0275f-251">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0275f-252">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0275f-252">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0275f-253">Les services, tels que le contexte de base de données EF Core, sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="0275f-253">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0275f-254">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="0275f-254">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0275f-255">Le code de constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="0275f-255">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0275f-256">L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit auprès du conteneur d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="0275f-256">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0275f-257">Examinez la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0275f-257">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0275f-258">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="0275f-258">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="0275f-259">Les `RazorPagesMovieContext` coordonnées EF Core fonctionnalité, telles que la création, la lecture, la mise à jour et la suppression, pour le `Movie` modèle.</span><span class="sxs-lookup"><span data-stu-id="0275f-259">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="0275f-260">Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="0275f-260">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="0275f-261">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-261">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0275f-262">Le code précédent crée une [propriété \<Movie> DbSet](xref:Microsoft.EntityFrameworkCore.DbSet%601) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="0275f-262">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="0275f-263">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-263">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="0275f-264">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="0275f-264">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0275f-265">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="0275f-265">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="0275f-266">Pour le développement local, le [système de configuration](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="0275f-266">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0275f-267">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-267">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0275f-268">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="0275f-268">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="0275f-269">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="0275f-269">Test the app</span></span>

1. <span data-ttu-id="0275f-270">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="0275f-270">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="0275f-271">Si vous recevez l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="0275f-271">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="0275f-272">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="0275f-272">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="0275f-273">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="0275f-273">Test the **Create** link.</span></span>

   ![Create page](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="0275f-275">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="0275f-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0275f-276">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="0275f-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="0275f-277">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="0275f-277">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="0275f-278">Testez les liens **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="0275f-278">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="0275f-279">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="0275f-279">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0275f-280">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0275f-280">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0275f-281">[Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="0275f-281">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="0275f-282">Dans cette section, des classes sont ajoutées pour la gestion des films.</span><span class="sxs-lookup"><span data-stu-id="0275f-282">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="0275f-283">Les classes de modèle de l’application utilisent [Entity Framework Core (EF Core)](/ef/core) pour travailler avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-283">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="0275f-284">EF Core est un mappeur objet-relationnel (O/RM) qui simplifie l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="0275f-284">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="0275f-285">Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core.</span><span class="sxs-lookup"><span data-stu-id="0275f-285">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="0275f-286">Elles définissent les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-286">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="0275f-287">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0275f-287">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="0275f-288">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="0275f-288">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-289">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-289">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0275f-290">Cliquez avec le bouton droit sur le projet **Razor PagesMovie** > **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="0275f-290">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="0275f-291">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="0275f-291">Name the folder *Models*.</span></span>

<span data-ttu-id="0275f-292">Cliquez avec le bouton droit sur le dossier *modèles* .</span><span class="sxs-lookup"><span data-stu-id="0275f-292">Right-click the *Models* folder.</span></span> <span data-ttu-id="0275f-293">Sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="0275f-293">Select **Add** > **Class**.</span></span> <span data-ttu-id="0275f-294">Nommez la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="0275f-294">Name the class **Movie**.</span></span>

<span data-ttu-id="0275f-295">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="0275f-295">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0275f-296">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="0275f-296">The `Movie` class contains:</span></span>

* <span data-ttu-id="0275f-297">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="0275f-297">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0275f-298">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0275f-298">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0275f-299">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="0275f-299">With this attribute:</span></span>

  * <span data-ttu-id="0275f-300">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="0275f-300">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0275f-301">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="0275f-301">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0275f-302">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="0275f-302">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0275f-303">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0275f-303">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0275f-304">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="0275f-304">Add a folder named *Models*.</span></span>
* <span data-ttu-id="0275f-305">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="0275f-305">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="0275f-306">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="0275f-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0275f-307">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="0275f-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="0275f-308">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="0275f-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0275f-309">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0275f-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0275f-310">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="0275f-310">With this attribute:</span></span>

  * <span data-ttu-id="0275f-311">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="0275f-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0275f-312">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="0275f-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0275f-313">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="0275f-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="0275f-314">Ajouter des packages NuGet des outils EF</span><span class="sxs-lookup"><span data-stu-id="0275f-314">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="0275f-315">Ajouter une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="0275f-315">Add a database context class</span></span>

* <span data-ttu-id="0275f-316">Dans le projet *Razor PagesMovie* , créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="0275f-316">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="0275f-317">Ajoutez la classe `RazorPagesMovieContext` suivante au dossier *Data* :</span><span class="sxs-lookup"><span data-stu-id="0275f-317">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0275f-318">Le code précédent crée une propriété `DbSet` pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="0275f-318">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="0275f-319">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="0275f-319">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="0275f-320">Le code n’est pas compilé tant que les dépendances n’ont pas été ajoutées dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="0275f-320">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="0275f-321">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="0275f-321">Add a database connection string</span></span>

<span data-ttu-id="0275f-322">Ajoutez une chaîne de connexion au *appsettings.json* fichier comme indiqué dans le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="0275f-322">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="0275f-323">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="0275f-323">Register the database context</span></span>

<span data-ttu-id="0275f-324">En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="0275f-324">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="0275f-325">Inscrivez le contexte de base de données auprès du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0275f-325">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0275f-326">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0275f-327">Dans la **fenêtre outil de solution**, cliquez avec le contrôle sur le projet **Razor PagesMovie** , puis sélectionnez **Ajouter** > **un nouveau dossier...**. Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="0275f-327">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="0275f-328">Cliquez avec le bouton droit sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier...**.</span><span class="sxs-lookup"><span data-stu-id="0275f-328">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="0275f-329">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="0275f-329">In the **New File** dialog:</span></span>

  * <span data-ttu-id="0275f-330">Dans le volet gauche, sélectionnez **Général**.</span><span class="sxs-lookup"><span data-stu-id="0275f-330">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="0275f-331">Dans le volet central, sélectionnez **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="0275f-331">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="0275f-332">Nommez la classe **Movie**, puis sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="0275f-332">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="0275f-333">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="0275f-333">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0275f-334">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="0275f-334">The `Movie` class contains:</span></span>

* <span data-ttu-id="0275f-335">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="0275f-335">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0275f-336">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0275f-336">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0275f-337">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="0275f-337">With this attribute:</span></span>

  * <span data-ttu-id="0275f-338">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="0275f-338">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0275f-339">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="0275f-339">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="0275f-340">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="0275f-340">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="0275f-341">Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="0275f-341">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="0275f-342">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="0275f-342">Scaffold the movie model</span></span>

<span data-ttu-id="0275f-343">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="0275f-343">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="0275f-344">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="0275f-344">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-345">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-345">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0275f-346">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="0275f-346">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="0275f-347">Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="0275f-347">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="0275f-348">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="0275f-348">Name the folder *Movies*.</span></span>

<span data-ttu-id="0275f-349">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="0275f-349">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/sca.png)

<span data-ttu-id="0275f-351">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="0275f-351">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

<span data-ttu-id="0275f-353">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0275f-353">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0275f-354">Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="0275f-354">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="0275f-355">Dans la ligne de la **classe de contexte de données** , sélectionnez le **+** signe (plus), puis modifiez le nom généré à partir de Razor PagesMovie.**Modèles**. Razor PagesMovieContext à Razor PagesMovie.**Données**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="0275f-355">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="0275f-356">[Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise.</span><span class="sxs-lookup"><span data-stu-id="0275f-356">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="0275f-357">Elle crée la classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="0275f-357">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="0275f-358">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="0275f-358">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/3/arp.png)

<span data-ttu-id="0275f-360">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="0275f-360">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0275f-361">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0275f-361">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="0275f-362">Ouvrez une fenêtre de commande dans le répertoire du projet, qui contient les fichiers *Program.cs*, *Startup.cs* et *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0275f-362">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="0275f-363">**Pour Windows**: exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="0275f-363">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="0275f-364">**Pour macOS et Linux**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="0275f-364">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="0275f-365">Le tableau suivant détaille les options du générateur de code ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="0275f-365">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="0275f-366">Option</span><span class="sxs-lookup"><span data-stu-id="0275f-366">Option</span></span>               | <span data-ttu-id="0275f-367">Description</span><span class="sxs-lookup"><span data-stu-id="0275f-367">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="0275f-368">Nom du modèle.</span><span class="sxs-lookup"><span data-stu-id="0275f-368">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="0275f-369">Classe `DbContext` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="0275f-369">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="0275f-370">Utiliser la disposition par défaut.</span><span class="sxs-lookup"><span data-stu-id="0275f-370">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="0275f-371">Chemin du dossier de sortie relatif pour créer les vues.</span><span class="sxs-lookup"><span data-stu-id="0275f-371">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="0275f-372">Ajoute `_ValidationScriptsPartial` aux pages Modifier et Créer.</span><span class="sxs-lookup"><span data-stu-id="0275f-372">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="0275f-373">Utilisez l' `-h` option pour obtenir de l’aide sur la `aspnet-codegenerator razorpage` commande :</span><span class="sxs-lookup"><span data-stu-id="0275f-373">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="0275f-374">Pour plus d’informations, consultez [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="0275f-374">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="0275f-375">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="0275f-375">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="0275f-376">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="0275f-376">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="0275f-377">Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage.</span><span class="sxs-lookup"><span data-stu-id="0275f-377">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="0275f-378">`IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="0275f-378">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0275f-379">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-379">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0275f-380">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="0275f-380">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="0275f-381">Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="0275f-381">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="0275f-382">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="0275f-382">Name the folder *Movies*.</span></span>

<span data-ttu-id="0275f-383">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** une > **nouvelle génération de modèles automatique...**.</span><span class="sxs-lookup"><span data-stu-id="0275f-383">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

<span data-ttu-id="0275f-385">Dans la boîte de dialogue **nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="0275f-385">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="0275f-387">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0275f-387">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0275f-388">Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="0275f-388">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="0275f-389">Dans la ligne de la **classe de contexte de données** , tapez le nom de la nouvelle classe, Razor PagesMovie.**Données**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="0275f-389">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="0275f-390">[Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise.</span><span class="sxs-lookup"><span data-stu-id="0275f-390">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="0275f-391">Elle crée la classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="0275f-391">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="0275f-392">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="0275f-392">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

<span data-ttu-id="0275f-394">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="0275f-394">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="0275f-395">Ajouter des outils EF</span><span class="sxs-lookup"><span data-stu-id="0275f-395">Add EF tools</span></span>

<span data-ttu-id="0275f-396">Exécutez la commande CLI .NET Core suivante :</span><span class="sxs-lookup"><span data-stu-id="0275f-396">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="0275f-397">La commande précédente ajoute les outils de Entity Framework Core pour le CLI .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0275f-397">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="0275f-398">Pour plus d’informations, consultez [référence des outils de Entity Framework Core-CLI .net Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="0275f-398">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="0275f-399">Utiliser SQLite pour le développement, SQL Server pour la production</span><span class="sxs-lookup"><span data-stu-id="0275f-399">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="0275f-400">Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement.</span><span class="sxs-lookup"><span data-stu-id="0275f-400">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="0275f-401">Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage.</span><span class="sxs-lookup"><span data-stu-id="0275f-401">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="0275f-402">`IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.</span><span class="sxs-lookup"><span data-stu-id="0275f-402">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="0275f-403">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="0275f-403">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-404">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0275f-405">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="0275f-405">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="0275f-406">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="0275f-406">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0275f-407">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0275f-407">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="0275f-408">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="0275f-408">Updated</span></span>

* <span data-ttu-id="0275f-409">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0275f-409">*Startup.cs*</span></span>

<span data-ttu-id="0275f-410">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="0275f-410">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0275f-411">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-411">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0275f-412">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="0275f-412">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="0275f-413">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="0275f-413">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0275f-414">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0275f-414">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="0275f-415">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="0275f-415">Updated</span></span>

* <span data-ttu-id="0275f-416">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0275f-416">*Startup.cs*</span></span>

<span data-ttu-id="0275f-417">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="0275f-417">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0275f-418">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0275f-418">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0275f-419">Le processus de génération de modèles automatique crée les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="0275f-419">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="0275f-420">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="0275f-420">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="0275f-421">Les fichiers créés sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="0275f-421">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="0275f-422">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="0275f-422">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0275f-424">Dans cette section, la console du gestionnaire de package est utilisée pour :</span><span class="sxs-lookup"><span data-stu-id="0275f-424">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="0275f-425">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="0275f-425">Add an initial migration.</span></span>
* <span data-ttu-id="0275f-426">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="0275f-426">Update the database with the initial migration.</span></span>

<span data-ttu-id="0275f-427">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="0275f-427">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="0275f-429">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="0275f-429">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0275f-430">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-430">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="0275f-431">Exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="0275f-431">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="0275f-432">Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ».</span><span class="sxs-lookup"><span data-stu-id="0275f-432">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="0275f-433">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="0275f-433">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="0275f-434">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »</span><span class="sxs-lookup"><span data-stu-id="0275f-434">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="0275f-435">Ignorez l’avertissement, car il sera traité dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="0275f-435">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="0275f-436">La commande migrations génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="0275f-436">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="0275f-437">Le schéma est basé sur le modèle spécifié dans `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="0275f-437">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="0275f-438">L’argument `InitialCreate` est utilisé pour nommer les migrations.</span><span class="sxs-lookup"><span data-stu-id="0275f-438">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="0275f-439">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="0275f-439">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="0275f-440">La `update` commande exécute la `Up` méthode dans des migrations qui n’ont pas été appliquées.</span><span class="sxs-lookup"><span data-stu-id="0275f-440">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="0275f-441">Dans ce cas, `update` exécute la `Up` méthode dans le fichier  *migrations/ \<time-stamp> _InitialCreate. cs* , qui crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-441">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-442">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-442">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0275f-443">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="0275f-443">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0275f-444">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0275f-444">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0275f-445">Les services, tels que le contexte de contexte de la base de données EF Core, sont inscrits avec l’injection de dépendance pendant le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="0275f-445">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0275f-446">Ces services sont fournis par les composants qui requièrent ces services, tels que les Razor pages, via des paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="0275f-446">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="0275f-447">Le code du constructeur qui obtient une instance de contexte de contexte de base de données est indiqué plus loin dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="0275f-447">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0275f-448">L’outil de génération de modèles automatique a créé automatiquement un contexte de contexte de base de données et l’a inscrit auprès du conteneur d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="0275f-448">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0275f-449">Examinez la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0275f-449">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0275f-450">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="0275f-450">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="0275f-451">Les `RazorPagesMovieContext` coordonnées EF Core fonctionnalité, telles que la création, la lecture, la mise à jour et la suppression, pour le `Movie` modèle.</span><span class="sxs-lookup"><span data-stu-id="0275f-451">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="0275f-452">Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="0275f-452">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="0275f-453">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-453">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0275f-454">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="0275f-454">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="0275f-455">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-455">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="0275f-456">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="0275f-456">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0275f-457">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="0275f-457">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="0275f-458">Pour le développement local, le [système de configuration](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="0275f-458">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0275f-459">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-459">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0275f-460">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="0275f-460">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="0275f-461">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="0275f-461">Test the app</span></span>

* <span data-ttu-id="0275f-462">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="0275f-462">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="0275f-463">Si vous obtenez cette erreur :</span><span class="sxs-lookup"><span data-stu-id="0275f-463">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="0275f-464">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="0275f-464">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="0275f-465">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="0275f-465">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="0275f-467">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="0275f-467">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0275f-468">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="0275f-468">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="0275f-469">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="0275f-469">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="0275f-470">Testez les liens **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="0275f-470">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="0275f-471">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="0275f-471">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0275f-472">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0275f-472">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0275f-473">[Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="0275f-473">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0275f-474">Dans cette section, des classes sont ajoutées pour la gestion des films dans une [base de données SQLite](https://www.sqlite.org/index.html)multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="0275f-474">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="0275f-475">Les applications créées à partir d’un modèle de ASP.NET Core utilisent une base de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="0275f-475">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="0275f-476">Les classes de modèle de l’application sont utilisées avec [Entity Framework Core (EF Core)](/ef/core) ([fournisseur de base de données SQLite EF Core](/ef/core/providers/sqlite)) pour fonctionner avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-476">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="0275f-477">EF Core est un framework de mappage relationnel d’objets qui simplifie l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="0275f-477">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="0275f-478">Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core.</span><span class="sxs-lookup"><span data-stu-id="0275f-478">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="0275f-479">Elles définissent les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-479">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="0275f-480">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0275f-480">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="0275f-481">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="0275f-481">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-482">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-482">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0275f-483">Cliquez avec le bouton droit sur le projet **Razor PagesMovie** > **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="0275f-483">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="0275f-484">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="0275f-484">Name the folder *Models*.</span></span>

<span data-ttu-id="0275f-485">Cliquez avec le bouton droit sur le dossier *modèles* .</span><span class="sxs-lookup"><span data-stu-id="0275f-485">Right-click the *Models* folder.</span></span> <span data-ttu-id="0275f-486">Sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="0275f-486">Select **Add** > **Class**.</span></span> <span data-ttu-id="0275f-487">Nommez la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="0275f-487">Name the class **Movie**.</span></span>

<span data-ttu-id="0275f-488">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="0275f-488">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0275f-489">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="0275f-489">The `Movie` class contains:</span></span>

* <span data-ttu-id="0275f-490">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="0275f-490">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0275f-491">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0275f-491">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0275f-492">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="0275f-492">With this attribute:</span></span>

  * <span data-ttu-id="0275f-493">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="0275f-493">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0275f-494">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="0275f-494">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0275f-495">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="0275f-495">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0275f-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0275f-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0275f-497">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="0275f-497">Add a folder named *Models*.</span></span>
* <span data-ttu-id="0275f-498">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="0275f-498">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="0275f-499">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="0275f-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0275f-500">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="0275f-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="0275f-501">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="0275f-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0275f-502">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0275f-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0275f-503">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="0275f-503">With this attribute:</span></span>

  * <span data-ttu-id="0275f-504">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="0275f-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0275f-505">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="0275f-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0275f-506">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="0275f-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="0275f-507">Ajouter des packages NuGet des outils EF</span><span class="sxs-lookup"><span data-stu-id="0275f-507">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="0275f-508">Ajouter une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="0275f-508">Add a database context class</span></span>

<span data-ttu-id="0275f-509">Dans le Razor projet PagesMovie, créez un dossier nommé *Data*.</span><span class="sxs-lookup"><span data-stu-id="0275f-509">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="0275f-510">Ajoutez la classe `RazorPagesMovieContext` suivante au dossier *Data* :</span><span class="sxs-lookup"><span data-stu-id="0275f-510">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0275f-511">Le code précédent crée une propriété `DbSet` pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="0275f-511">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="0275f-512">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="0275f-512">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="0275f-513">Le code n’est pas compilé tant que les dépendances n’ont pas été ajoutées dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="0275f-513">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="0275f-514">Ajouter une chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="0275f-514">Add a database connection string</span></span>

<span data-ttu-id="0275f-515">Ajoutez une chaîne de connexion au *appsettings.json* fichier comme indiqué dans le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="0275f-515">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="0275f-516">Ajouter les packages NuGet exigés</span><span class="sxs-lookup"><span data-stu-id="0275f-516">Add required NuGet packages</span></span>

<span data-ttu-id="0275f-517">Exécutez la commande CLI .NET Core suivante pour ajouter SQLite et CodeGeneration. Design au projet :</span><span class="sxs-lookup"><span data-stu-id="0275f-517">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="0275f-518">Le package `Microsoft.VisualStudio.Web.CodeGeneration.Design` est nécessaire à la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="0275f-518">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="0275f-519">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="0275f-519">Register the database context</span></span>

<span data-ttu-id="0275f-520">En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :</span><span class="sxs-lookup"><span data-stu-id="0275f-520">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="0275f-521">Inscrivez le contexte de base de données auprès du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0275f-521">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="0275f-522">Générez le projet en tant que vérification des erreurs.</span><span class="sxs-lookup"><span data-stu-id="0275f-522">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0275f-523">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-523">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0275f-524">Dans la **fenêtre outil** de la solution, cliquez avec le contrôle sur le projet *Razor PagesMovie* , puis sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="0275f-524">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="0275f-525">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="0275f-525">Name the folder *Models*.</span></span>
* <span data-ttu-id="0275f-526">Cliquez avec le contrôle sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier**.</span><span class="sxs-lookup"><span data-stu-id="0275f-526">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="0275f-527">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="0275f-527">In the **New File** dialog:</span></span>

  * <span data-ttu-id="0275f-528">Dans le volet gauche, sélectionnez **Général**.</span><span class="sxs-lookup"><span data-stu-id="0275f-528">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="0275f-529">Dans le volet central, sélectionnez **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="0275f-529">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="0275f-530">Nommez la classe **Movie**, puis sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="0275f-530">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="0275f-531">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="0275f-531">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0275f-532">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="0275f-532">The `Movie` class contains:</span></span>

* <span data-ttu-id="0275f-533">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="0275f-533">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0275f-534">`[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="0275f-534">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0275f-535">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="0275f-535">With this attribute:</span></span>

  * <span data-ttu-id="0275f-536">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="0275f-536">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0275f-537">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="0275f-537">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0275f-538">Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="0275f-538">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="0275f-539">Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="0275f-539">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="0275f-540">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="0275f-540">Scaffold the movie model</span></span>

<span data-ttu-id="0275f-541">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="0275f-541">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="0275f-542">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="0275f-542">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-543">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0275f-544">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="0275f-544">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="0275f-545">Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="0275f-545">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="0275f-546">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="0275f-546">Name the folder *Movies*.</span></span>

<span data-ttu-id="0275f-547">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="0275f-547">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/sca.png)

<span data-ttu-id="0275f-549">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="0275f-549">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

<span data-ttu-id="0275f-551">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0275f-551">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="0275f-552">Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="0275f-552">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="0275f-553">Dans la ligne de la **classe de contexte de données** , sélectionnez le **+** signe (plus) et acceptez le nom généré **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="0275f-553">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="0275f-554">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="0275f-554">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/arp.png)

<span data-ttu-id="0275f-556">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="0275f-556">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0275f-557">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0275f-557">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="0275f-558">Ouvrez une fenêtre de commande dans le répertoire du projet, qui contient les fichiers *Program.cs*, *Startup.cs* et *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0275f-558">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="0275f-559">**Pour Windows**: exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="0275f-559">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="0275f-560">**Pour macOS et Linux**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="0275f-560">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="0275f-561">Le tableau suivant détaille les options du générateur de code ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="0275f-561">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="0275f-562">Option</span><span class="sxs-lookup"><span data-stu-id="0275f-562">Option</span></span>               | <span data-ttu-id="0275f-563">Description</span><span class="sxs-lookup"><span data-stu-id="0275f-563">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="0275f-564">Nom du modèle.</span><span class="sxs-lookup"><span data-stu-id="0275f-564">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="0275f-565">Classe `DbContext` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="0275f-565">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="0275f-566">Utiliser la disposition par défaut.</span><span class="sxs-lookup"><span data-stu-id="0275f-566">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="0275f-567">Chemin du dossier de sortie relatif pour créer les vues.</span><span class="sxs-lookup"><span data-stu-id="0275f-567">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="0275f-568">Ajoute `_ValidationScriptsPartial` aux pages Modifier et Créer.</span><span class="sxs-lookup"><span data-stu-id="0275f-568">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="0275f-569">Utilisez l' `-h` option pour obtenir de l’aide sur la `aspnet-codegenerator razorpage` commande :</span><span class="sxs-lookup"><span data-stu-id="0275f-569">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="0275f-570">Pour plus d’informations, consultez [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="0275f-570">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0275f-571">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-571">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0275f-572">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="0275f-572">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="0275f-573">Cliquez avec le contrôle sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="0275f-573">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="0275f-574">Nommez le dossier *films*.</span><span class="sxs-lookup"><span data-stu-id="0275f-574">Name the folder *Movies*.</span></span>

<span data-ttu-id="0275f-575">Cliquez avec le contrôle sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="0275f-575">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

<span data-ttu-id="0275f-577">Dans la boîte de dialogue **Ajouter une nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="0275f-577">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="0275f-579">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0275f-579">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0275f-580">Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie**.</span><span class="sxs-lookup"><span data-stu-id="0275f-580">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="0275f-581">Dans la ligne de la **classe de contexte de données** , tapez Select the **Razor PagesMovieContext** This crée une nouvelle classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="0275f-581">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="0275f-582">Dans ce cas, il s’agit de **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="0275f-582">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="0275f-583">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="0275f-583">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

<span data-ttu-id="0275f-585">Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="0275f-585">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="0275f-586">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="0275f-586">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="0275f-587">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="0275f-587">Files created</span></span>

* <span data-ttu-id="0275f-588">*Pages/films*: créer, supprimer, détails, modifier et Index .</span><span class="sxs-lookup"><span data-stu-id="0275f-588">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0275f-589">*Données/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0275f-589">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="0275f-590">Fichier mis à jour</span><span class="sxs-lookup"><span data-stu-id="0275f-590">File updated</span></span>

* <span data-ttu-id="0275f-591">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0275f-591">*Startup.cs*</span></span>

<span data-ttu-id="0275f-592">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="0275f-592">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="0275f-593">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="0275f-593">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-594">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-594">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0275f-595">Dans cette section, la console du gestionnaire de package est utilisée pour :</span><span class="sxs-lookup"><span data-stu-id="0275f-595">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="0275f-596">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="0275f-596">Add an initial migration.</span></span>
* <span data-ttu-id="0275f-597">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="0275f-597">Update the database with the initial migration.</span></span>

<span data-ttu-id="0275f-598">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="0275f-598">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="0275f-600">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="0275f-600">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="0275f-601">La commande `Add-Migration` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="0275f-601">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="0275f-602">Le schéma est basé sur le modèle spécifié dans le `DbContext` , dans le fichier *Razor PagesMovieContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="0275f-602">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="0275f-603">L' `InitialCreate` argument est utilisé pour nommer la migration.</span><span class="sxs-lookup"><span data-stu-id="0275f-603">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="0275f-604">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est utilisé.</span><span class="sxs-lookup"><span data-stu-id="0275f-604">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="0275f-605">Pour plus d’informations, consultez <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="0275f-605">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="0275f-606">La `Update-Database` commande exécute la `Up` méthode dans le fichier *migrations/ \<time-stamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="0275f-606">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="0275f-607">La méthode `Up` crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-607">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0275f-608">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-608">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="0275f-609">Exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="0275f-609">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="0275f-610">Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ».</span><span class="sxs-lookup"><span data-stu-id="0275f-610">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="0275f-611">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="0275f-611">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="0275f-612">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »</span><span class="sxs-lookup"><span data-stu-id="0275f-612">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="0275f-613">Ignorez l’avertissement, car il sera traité dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="0275f-613">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0275f-614">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0275f-614">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0275f-615">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="0275f-615">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0275f-616">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0275f-616">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0275f-617">Les services (tels que le contexte de contexte de la base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="0275f-617">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0275f-618">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="0275f-618">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0275f-619">Le code du constructeur qui obtient une instance de contexte de contextB du contexte de base de données est indiqué plus loin dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="0275f-619">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0275f-620">L’outil de génération de modèles automatique a créé automatiquement un contexte de contexte de base de données et l’a inscrit auprès du conteneur d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="0275f-620">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0275f-621">Examinez la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0275f-621">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0275f-622">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="0275f-622">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="0275f-623">Les `RazorPagesMovieContext` coordonnées EF Core fonctionnalité, telles que la création, la lecture, la mise à jour et la suppression, pour le `Movie` modèle.</span><span class="sxs-lookup"><span data-stu-id="0275f-623">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="0275f-624">Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="0275f-624">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="0275f-625">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-625">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0275f-626">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="0275f-626">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="0275f-627">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="0275f-627">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="0275f-628">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="0275f-628">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0275f-629">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="0275f-629">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="0275f-630">Pour le développement local, le [système de configuration](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="0275f-630">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0275f-631">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0275f-631">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0275f-632">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="0275f-632">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="0275f-633">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="0275f-633">Test the app</span></span>

* <span data-ttu-id="0275f-634">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="0275f-634">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="0275f-635">Si vous obtenez cette erreur :</span><span class="sxs-lookup"><span data-stu-id="0275f-635">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="0275f-636">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="0275f-636">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="0275f-637">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="0275f-637">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="0275f-639">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="0275f-639">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0275f-640">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="0275f-640">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="0275f-641">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="0275f-641">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="0275f-642">Testez les liens **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="0275f-642">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="0275f-643">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="0275f-643">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0275f-644">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0275f-644">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0275f-645">[Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="0275f-645">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
