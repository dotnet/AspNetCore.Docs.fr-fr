---
title: 'Partie 2, ajouter un modèle à une :::no-loc(Razor)::: application pages dans ASP.net Core'
author: rick-anderson
description: 'Partie 2 de la série de didacticiels sur les :::no-loc(Razor)::: pages.'
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 84198760cf8302d379c7630b65641e65b66d72a2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050925"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="8d558-103">Partie 2, ajouter un modèle à une :::no-loc(Razor)::: application pages dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="8d558-103">Part 2, add a model to a :::no-loc(Razor)::: Pages app in ASP.NET Core</span></span>

<span data-ttu-id="8d558-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8d558-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="8d558-105">Dans cette section, des classes sont ajoutées pour la gestion des films.</span><span class="sxs-lookup"><span data-stu-id="8d558-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="8d558-106">Les classes de modèle de l’application utilisent [Entity Framework Core (EF Core)](/ef/core) pour travailler avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="8d558-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="8d558-107">EF Core est un mappeur objet-relationnel (O/RM) qui simplifie l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="8d558-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="8d558-108">Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core.</span><span class="sxs-lookup"><span data-stu-id="8d558-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="8d558-109">Elles définissent les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="8d558-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="8d558-110">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="8d558-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d558-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d558-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d558-112">Cliquez avec le bouton droit sur le projet **:::no-loc(Razor)::: PagesMovie** > **Ajouter**  >  **un nouveau dossier** .</span><span class="sxs-lookup"><span data-stu-id="8d558-112">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder** .</span></span> <span data-ttu-id="8d558-113">Nommez le dossier *modèles* .</span><span class="sxs-lookup"><span data-stu-id="8d558-113">Name the folder *Models* .</span></span>

<span data-ttu-id="8d558-114">Cliquez avec le bouton droit sur le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="8d558-114">Right click the *Models* folder.</span></span> <span data-ttu-id="8d558-115">Sélectionnez **Ajouter** une  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="8d558-115">Select **Add** > **Class** .</span></span> <span data-ttu-id="8d558-116">Nommez la classe **Movie** .</span><span class="sxs-lookup"><span data-stu-id="8d558-116">Name the class **Movie** .</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d558-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d558-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8d558-118">Ajoutez un dossier nommé *Models* .</span><span class="sxs-lookup"><span data-stu-id="8d558-118">Add a folder named *Models* .</span></span>
* <span data-ttu-id="8d558-119">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs* .</span><span class="sxs-lookup"><span data-stu-id="8d558-119">Add a class to the *Models* folder named *Movie.cs* .</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8d558-120">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="8d558-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8d558-121">Dans panneau solutions, cliquez avec le bouton droit sur le projet **:::no-loc(Razor)::: PagesMovie** , puis sélectionnez **Ajouter** > **un nouveau dossier...** . Nommez le dossier *modèles* .</span><span class="sxs-lookup"><span data-stu-id="8d558-121">In Solution Pad, right-click the **:::no-loc(Razor):::PagesMovie** project, and then select **Add** > **New Folder...** . Name the folder *Models* .</span></span>
* <span data-ttu-id="8d558-122">Cliquez avec le bouton droit sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier...** .</span><span class="sxs-lookup"><span data-stu-id="8d558-122">Right-click the *Models* folder, and then select **Add** > **New File...** .</span></span>
* <span data-ttu-id="8d558-123">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="8d558-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="8d558-124">Dans le volet gauche, sélectionnez **Général** .</span><span class="sxs-lookup"><span data-stu-id="8d558-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="8d558-125">Dans le volet central, sélectionnez **Classe vide** .</span><span class="sxs-lookup"><span data-stu-id="8d558-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="8d558-126">Nommez la classe **Movie** , puis sélectionnez **Nouveau** .</span><span class="sxs-lookup"><span data-stu-id="8d558-126">Name the class **Movie** and select **New** .</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="8d558-127">Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="8d558-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="8d558-128">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="8d558-128">Scaffold the movie model</span></span>

<span data-ttu-id="8d558-129">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="8d558-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="8d558-130">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="8d558-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d558-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d558-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d558-132">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="8d558-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8d558-133">Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier** .</span><span class="sxs-lookup"><span data-stu-id="8d558-133">Right click on the *Pages* folder > **Add** > **New Folder** .</span></span>
* <span data-ttu-id="8d558-134">Nommez le dossier *Movies* .</span><span class="sxs-lookup"><span data-stu-id="8d558-134">Name the folder *Movies*</span></span>

<span data-ttu-id="8d558-135">Cliquez avec le bouton droit sur le dossier *Pages/Movies* > **Ajouter** > **Nouvel élément généré automatiquement** .</span><span class="sxs-lookup"><span data-stu-id="8d558-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item** .</span></span>

![Image illustrant les instructions précédentes.](model/_static/sca.png)

<span data-ttu-id="8d558-137">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **:::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** > **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="8d558-137">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add** .</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

<span data-ttu-id="8d558-139">Complétez la boîte de dialogue **Ajouter des :::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="8d558-139">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="8d558-140">Dans la liste déroulante **classe de modèle** , sélectionnez **film ( :::no-loc(Razor)::: PagesMovie. Models)** .</span><span class="sxs-lookup"><span data-stu-id="8d558-140">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)** .</span></span>
* <span data-ttu-id="8d558-141">Dans la ligne de la **classe de contexte de données** , sélectionnez le **+** signe (plus), puis modifiez le nom généré à partir de :::no-loc(Razor)::: PagesMovie. **Modèles** . :::no-loc(Razor)::: PagesMovieContext à :::no-loc(Razor)::: PagesMovie. **Données** . :::no-loc(Razor)::: PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="8d558-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from :::no-loc(Razor):::PagesMovie. **Models** .:::no-loc(Razor):::PagesMovieContext to :::no-loc(Razor):::PagesMovie. **Data** .:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="8d558-142">[Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise.</span><span class="sxs-lookup"><span data-stu-id="8d558-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="8d558-143">Elle crée la classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="8d558-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="8d558-144">Sélectionnez **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="8d558-144">Select **Add** .</span></span>

![Image illustrant les instructions précédentes.](model/_static/3/arp.png)

<span data-ttu-id="8d558-146">Le *:::no-loc(appsettings.json):::* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="8d558-146">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d558-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d558-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="8d558-148">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="8d558-148">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="8d558-149">Installez l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="8d558-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="8d558-150">**Pour Windows** : exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="8d558-150">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="8d558-151">**Pour macOS et Linux** , exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="8d558-151">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8d558-152">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="8d558-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8d558-153">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="8d558-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8d558-154">Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier** .</span><span class="sxs-lookup"><span data-stu-id="8d558-154">Right click on the *Pages* folder > **Add** > **New Folder** .</span></span>
* <span data-ttu-id="8d558-155">Nommez le dossier *Movies* .</span><span class="sxs-lookup"><span data-stu-id="8d558-155">Name the folder *Movies*</span></span>

<span data-ttu-id="8d558-156">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** une > **nouvelle génération de modèles automatique...** .</span><span class="sxs-lookup"><span data-stu-id="8d558-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...** .</span></span>

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

<span data-ttu-id="8d558-158">Dans la boîte de dialogue **nouvelle génération de modèles** automatique, sélectionnez **:::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** > **suivant** .</span><span class="sxs-lookup"><span data-stu-id="8d558-158">In the **New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Next** .</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="8d558-160">Complétez la boîte de dialogue **Ajouter des :::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="8d558-160">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="8d558-161">Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie ( :::no-loc(Razor)::: PagesMovie. Models)** .</span><span class="sxs-lookup"><span data-stu-id="8d558-161">In the **Model class** drop down, select, or type, **Movie (:::no-loc(Razor):::PagesMovie.Models)** .</span></span>
* <span data-ttu-id="8d558-162">Dans la ligne de la **classe de contexte de données** , tapez le nom de la nouvelle classe, :::no-loc(Razor)::: PagesMovie. **Données** . :::no-loc(Razor)::: PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="8d558-162">In the **Data context class** row, type the name for the new class, :::no-loc(Razor):::PagesMovie. **Data** .:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="8d558-163">[Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise.</span><span class="sxs-lookup"><span data-stu-id="8d558-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="8d558-164">Elle crée la classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="8d558-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="8d558-165">Sélectionnez **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="8d558-165">Select **Add** .</span></span>

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

<span data-ttu-id="8d558-167">Le *:::no-loc(appsettings.json):::* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="8d558-167">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="8d558-168">Ajouter des outils EF</span><span class="sxs-lookup"><span data-stu-id="8d558-168">Add EF tools</span></span>

<span data-ttu-id="8d558-169">Exécutez la commande CLI .NET Core suivante :</span><span class="sxs-lookup"><span data-stu-id="8d558-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="8d558-170">La commande précédente ajoute les outils de Entity Framework Core pour le CLI .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8d558-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="8d558-171">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="8d558-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d558-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d558-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d558-173">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="8d558-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="8d558-174">*Pages/Movies*  : Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="8d558-174">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="8d558-175">*Données/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="8d558-175">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="8d558-176">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="8d558-176">Updated</span></span>

* <span data-ttu-id="8d558-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="8d558-177">*Startup.cs*</span></span>

<span data-ttu-id="8d558-178">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="8d558-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8d558-179">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="8d558-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8d558-180">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="8d558-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="8d558-181">*Pages/Movies*  : Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="8d558-181">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="8d558-182">*Données/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="8d558-182">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="8d558-183">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="8d558-183">Updated</span></span>

* <span data-ttu-id="8d558-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="8d558-184">*Startup.cs*</span></span>

<span data-ttu-id="8d558-185">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="8d558-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d558-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d558-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8d558-187">Le processus de génération de modèles automatique crée les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="8d558-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="8d558-188">*Pages/Movies*  : Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="8d558-188">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="8d558-189">Les fichiers créés sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="8d558-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="8d558-190">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="8d558-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d558-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d558-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d558-192">Dans cette section, la console du gestionnaire de package est utilisée pour :</span><span class="sxs-lookup"><span data-stu-id="8d558-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="8d558-193">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="8d558-193">Add an initial migration.</span></span>
* <span data-ttu-id="8d558-194">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="8d558-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="8d558-195">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** .</span><span class="sxs-lookup"><span data-stu-id="8d558-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** .</span></span>

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="8d558-197">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="8d558-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d558-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d558-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8d558-199">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="8d558-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="8d558-200">Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ».</span><span class="sxs-lookup"><span data-stu-id="8d558-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="8d558-201">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="8d558-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="8d558-202">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »</span><span class="sxs-lookup"><span data-stu-id="8d558-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="8d558-203">Vous pouvez ignorer cet avertissement, il sera corrigé dans un prochain tutoriel.</span><span class="sxs-lookup"><span data-stu-id="8d558-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="8d558-204">La commande migrations génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="8d558-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="8d558-205">Le schéma est basé sur le modèle spécifié dans `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="8d558-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="8d558-206">L’argument `InitialCreate` est utilisé pour nommer les migrations.</span><span class="sxs-lookup"><span data-stu-id="8d558-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="8d558-207">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="8d558-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="8d558-208">La `update` commande exécute la `Up` méthode dans des migrations qui n’ont pas été appliquées.</span><span class="sxs-lookup"><span data-stu-id="8d558-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="8d558-209">Dans ce cas, `update` exécute la `Up` méthode dans le fichier  *migrations/ \<time-stamp> _InitialCreate. cs* , qui crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="8d558-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d558-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d558-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="8d558-211">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="8d558-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="8d558-212">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8d558-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8d558-213">Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="8d558-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="8d558-214">Ces services sont fournis par les composants qui requièrent ces services (tels que les :::no-loc(Razor)::: pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="8d558-214">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8d558-215">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="8d558-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="8d558-216">L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="8d558-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="8d558-217">Examinez la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8d558-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8d558-218">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="8d558-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="8d558-219">`:::no-loc(Razor):::PagesMovieContext` coordonne les fonctionnalités d’EF Core (Create, Read, Update, Delete, etc.) pour le modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="8d558-219">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="8d558-220">Le contexte de données (`:::no-loc(Razor):::PagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="8d558-220">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="8d558-221">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="8d558-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="8d558-222">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="8d558-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8d558-223">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="8d558-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8d558-224">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="8d558-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="8d558-225">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="8d558-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8d558-226">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *:::no-loc(appsettings.json):::* fichier.</span><span class="sxs-lookup"><span data-stu-id="8d558-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d558-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d558-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8d558-228">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="8d558-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8d558-229">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="8d558-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8d558-230">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="8d558-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="8d558-231">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="8d558-231">Test the app</span></span>

* <span data-ttu-id="8d558-232">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="8d558-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="8d558-233">Si vous obtenez cette erreur :</span><span class="sxs-lookup"><span data-stu-id="8d558-233">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="8d558-234">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="8d558-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="8d558-235">Testez le lien **Créer** .</span><span class="sxs-lookup"><span data-stu-id="8d558-235">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="8d558-237">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="8d558-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8d558-238">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="8d558-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8d558-239">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="8d558-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8d558-240">Testez les liens **Edit** , **Details** et **Delete** .</span><span class="sxs-lookup"><span data-stu-id="8d558-240">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="8d558-241">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="8d558-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8d558-242">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8d558-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8d558-243">[Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles :::no-loc(Razor)::: automatique Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="8d558-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8d558-244">Dans cette section, des classes sont ajoutées pour la gestion des films dans une [base de données SQLite](https://www.sqlite.org/index.html)multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="8d558-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="8d558-245">Les applications créées à partir d’un modèle de ASP.NET Core utilisent une base de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="8d558-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="8d558-246">Les classes de modèle de l’application sont utilisées avec [Entity Framework Core (EF Core)](/ef/core) ([fournisseur de base de données SQLite EF Core](/ef/core/providers/sqlite)) pour fonctionner avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="8d558-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="8d558-247">EF Core est un framework de mappage relationnel d’objets qui simplifie l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="8d558-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="8d558-248">Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core.</span><span class="sxs-lookup"><span data-stu-id="8d558-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="8d558-249">Elles définissent les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="8d558-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="8d558-250">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="8d558-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d558-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d558-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d558-252">Cliquez avec le bouton droit sur le projet **:::no-loc(Razor)::: PagesMovie** > **Ajouter**  >  **un nouveau dossier** .</span><span class="sxs-lookup"><span data-stu-id="8d558-252">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder** .</span></span> <span data-ttu-id="8d558-253">Nommez le dossier *modèles* .</span><span class="sxs-lookup"><span data-stu-id="8d558-253">Name the folder *Models* .</span></span>

<span data-ttu-id="8d558-254">Cliquez avec le bouton droit sur le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="8d558-254">Right click the *Models* folder.</span></span> <span data-ttu-id="8d558-255">Sélectionnez **Ajouter** une  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="8d558-255">Select **Add** > **Class** .</span></span> <span data-ttu-id="8d558-256">Nommez la classe **Movie** .</span><span class="sxs-lookup"><span data-stu-id="8d558-256">Name the class **Movie** .</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d558-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d558-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8d558-258">Ajoutez un dossier nommé *Models* .</span><span class="sxs-lookup"><span data-stu-id="8d558-258">Add a folder named *Models* .</span></span>
* <span data-ttu-id="8d558-259">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs* .</span><span class="sxs-lookup"><span data-stu-id="8d558-259">Add a class to the *Models* folder named *Movie.cs* .</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8d558-260">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="8d558-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8d558-261">Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet **:::no-loc(Razor)::: PagesMovie** , puis sélectionnez **Ajouter**  >  **un nouveau dossier** .</span><span class="sxs-lookup"><span data-stu-id="8d558-261">In Solution Explorer, right-click the **:::no-loc(Razor):::PagesMovie** project, and then select **Add** > **New Folder** .</span></span> <span data-ttu-id="8d558-262">Nommez le dossier *modèles* .</span><span class="sxs-lookup"><span data-stu-id="8d558-262">Name the folder *Models* .</span></span>
* <span data-ttu-id="8d558-263">Cliquez avec le bouton droit sur le dossier *Modèles* , puis sélectionnez **Ajouter** > **Nouveau fichier** .</span><span class="sxs-lookup"><span data-stu-id="8d558-263">Right-click the *Models* folder, and then select **Add** > **New File** .</span></span>
* <span data-ttu-id="8d558-264">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="8d558-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="8d558-265">Dans le volet gauche, sélectionnez **Général** .</span><span class="sxs-lookup"><span data-stu-id="8d558-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="8d558-266">Dans le volet central, sélectionnez **Classe vide** .</span><span class="sxs-lookup"><span data-stu-id="8d558-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="8d558-267">Nommez la classe **Movie** , puis sélectionnez **Nouveau** .</span><span class="sxs-lookup"><span data-stu-id="8d558-267">Name the class **Movie** and select **New** .</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="8d558-268">Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="8d558-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="8d558-269">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="8d558-269">Scaffold the movie model</span></span>

<span data-ttu-id="8d558-270">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="8d558-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="8d558-271">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="8d558-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d558-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d558-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d558-273">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="8d558-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8d558-274">Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier** .</span><span class="sxs-lookup"><span data-stu-id="8d558-274">Right click on the *Pages* folder > **Add** > **New Folder** .</span></span>
* <span data-ttu-id="8d558-275">Nommez le dossier *Movies* .</span><span class="sxs-lookup"><span data-stu-id="8d558-275">Name the folder *Movies*</span></span>

<span data-ttu-id="8d558-276">Cliquez avec le bouton droit sur le dossier *Pages/Movies* > **Ajouter** > **Nouvel élément généré automatiquement** .</span><span class="sxs-lookup"><span data-stu-id="8d558-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item** .</span></span>

![Image illustrant les instructions précédentes.](model/_static/sca.png)

<span data-ttu-id="8d558-278">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **:::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** > **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="8d558-278">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add** .</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

<span data-ttu-id="8d558-280">Complétez la boîte de dialogue **Ajouter des :::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="8d558-280">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="8d558-281">Dans la liste déroulante **classe de modèle** , sélectionnez **film ( :::no-loc(Razor)::: PagesMovie. Models)** .</span><span class="sxs-lookup"><span data-stu-id="8d558-281">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)** .</span></span>
* <span data-ttu-id="8d558-282">Dans la ligne de la **classe de contexte de données** , sélectionnez le **+** signe (plus) et acceptez le nom généré **:::no-loc(Razor)::: PagesMovie. Models. :::no-loc(Razor)::: PagesMovieContext** .</span><span class="sxs-lookup"><span data-stu-id="8d558-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext** .</span></span>
* <span data-ttu-id="8d558-283">Sélectionnez **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="8d558-283">Select **Add** .</span></span>

![Image illustrant les instructions précédentes.](model/_static/arp.png)

<span data-ttu-id="8d558-285">Le *:::no-loc(appsettings.json):::* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="8d558-285">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d558-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d558-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="8d558-287">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="8d558-287">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="8d558-288">**Pour Windows** : exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="8d558-288">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="8d558-289">**Pour macOS et Linux** , exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="8d558-289">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8d558-290">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="8d558-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8d558-291">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="8d558-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8d558-292">Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier** .</span><span class="sxs-lookup"><span data-stu-id="8d558-292">Right click on the *Pages* folder > **Add** > **New Folder** .</span></span>
* <span data-ttu-id="8d558-293">Nommez le dossier *Movies* .</span><span class="sxs-lookup"><span data-stu-id="8d558-293">Name the folder *Movies*</span></span>

<span data-ttu-id="8d558-294">Cliquez avec le bouton droit sur le dossier *Pages/Movies* > **Ajouter** > **Nouvel élément généré automatiquement** .</span><span class="sxs-lookup"><span data-stu-id="8d558-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item** .</span></span>

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

<span data-ttu-id="8d558-296">Dans la boîte de dialogue **Ajouter une nouvelle génération de modèles** automatique, sélectionnez **:::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** > **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="8d558-296">In the **Add New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add** .</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="8d558-298">Complétez la boîte de dialogue **Ajouter des :::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="8d558-298">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="8d558-299">Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie** .</span><span class="sxs-lookup"><span data-stu-id="8d558-299">In the **Model class** drop down, select or type **Movie** .</span></span>
* <span data-ttu-id="8d558-300">Dans la ligne de la **classe de contexte de données** , tapez Select the **:::no-loc(Razor)::: PagesMovieContext** This crée une nouvelle classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="8d558-300">In the **Data context class** row, type select the **:::no-loc(Razor):::PagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="8d558-301">Dans ce cas, il s’agit de **:::no-loc(Razor)::: PagesMovie. Models. :::no-loc(Razor)::: PagesMovieContext** .</span><span class="sxs-lookup"><span data-stu-id="8d558-301">In this case it will be  **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext** .</span></span>
* <span data-ttu-id="8d558-302">Sélectionnez **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="8d558-302">Select **Add** .</span></span>

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

<span data-ttu-id="8d558-304">Le *:::no-loc(appsettings.json):::* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="8d558-304">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="8d558-305">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="8d558-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="8d558-306">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="8d558-306">Files created</span></span>

* <span data-ttu-id="8d558-307">*Pages/Movies*  : Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="8d558-307">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="8d558-308">*Données/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="8d558-308">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="8d558-309">Fichier mis à jour</span><span class="sxs-lookup"><span data-stu-id="8d558-309">File updated</span></span>

* <span data-ttu-id="8d558-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="8d558-310">*Startup.cs*</span></span>

<span data-ttu-id="8d558-311">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="8d558-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="8d558-312">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="8d558-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d558-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d558-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d558-314">Dans cette section, la console du gestionnaire de package est utilisée pour :</span><span class="sxs-lookup"><span data-stu-id="8d558-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="8d558-315">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="8d558-315">Add an initial migration.</span></span>
* <span data-ttu-id="8d558-316">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="8d558-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="8d558-317">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** .</span><span class="sxs-lookup"><span data-stu-id="8d558-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** .</span></span>

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="8d558-319">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="8d558-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="8d558-320">La commande `Add-Migration` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="8d558-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="8d558-321">Le schéma est basé sur le modèle spécifié dans le `DbContext` (dans le fichier *:::no-loc(Razor)::: PagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="8d558-321">The schema is based on the model specified in the `DbContext` (In the *:::no-loc(Razor):::PagesMovieContext.cs* file).</span></span> <span data-ttu-id="8d558-322">L' `InitialCreate` argument est utilisé pour nommer la migration.</span><span class="sxs-lookup"><span data-stu-id="8d558-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="8d558-323">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est utilisé.</span><span class="sxs-lookup"><span data-stu-id="8d558-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="8d558-324">Pour plus d'informations, consultez <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="8d558-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="8d558-325">La `Update-Database` commande exécute la `Up` méthode dans le fichier *migrations/ \<time-stamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="8d558-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="8d558-326">La méthode `Up` crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="8d558-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d558-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d558-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8d558-328">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="8d558-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="8d558-329">Les commandes précédentes génèrent l’avertissement suivant : « *aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ». Les valeurs sont tronquées en mode silencieux si elles ne tiennent pas dans la précision et l’échelle par défaut. Spécifiez explicitement le type de colonne SQL Server qui peut prendre en charge toutes les valeurs à l’aide de’HasColumnType () '.* Vous pouvez ignorer cet avertissement. il sera corrigé dans un didacticiel ultérieur.</span><span class="sxs-lookup"><span data-stu-id="8d558-329">The preceding commands generate the following warning: " *No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.* " You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d558-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d558-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="8d558-331">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="8d558-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="8d558-332">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8d558-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8d558-333">Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="8d558-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="8d558-334">Ces services sont fournis par les composants qui requièrent ces services (tels que les :::no-loc(Razor)::: pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="8d558-334">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8d558-335">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="8d558-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="8d558-336">L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="8d558-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="8d558-337">Examinez la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8d558-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8d558-338">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="8d558-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="8d558-339">`:::no-loc(Razor):::PagesMovieContext` coordonne les fonctionnalités d’EF Core (Create, Read, Update, Delete, etc.) pour le modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="8d558-339">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="8d558-340">Le contexte de données (`:::no-loc(Razor):::PagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="8d558-340">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="8d558-341">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="8d558-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="8d558-342">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="8d558-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8d558-343">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="8d558-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8d558-344">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="8d558-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="8d558-345">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="8d558-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8d558-346">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *:::no-loc(appsettings.json):::* fichier.</span><span class="sxs-lookup"><span data-stu-id="8d558-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d558-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d558-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8d558-348">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="8d558-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8d558-349">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="8d558-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8d558-350">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="8d558-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="8d558-351">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="8d558-351">Test the app</span></span>

* <span data-ttu-id="8d558-352">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="8d558-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="8d558-353">Si vous obtenez cette erreur :</span><span class="sxs-lookup"><span data-stu-id="8d558-353">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="8d558-354">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="8d558-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="8d558-355">Testez le lien **Créer** .</span><span class="sxs-lookup"><span data-stu-id="8d558-355">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="8d558-357">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="8d558-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8d558-358">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="8d558-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8d558-359">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="8d558-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8d558-360">Testez les liens **Edit** , **Details** et **Delete** .</span><span class="sxs-lookup"><span data-stu-id="8d558-360">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="8d558-361">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="8d558-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8d558-362">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8d558-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8d558-363">[Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles :::no-loc(Razor)::: automatique Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="8d558-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
