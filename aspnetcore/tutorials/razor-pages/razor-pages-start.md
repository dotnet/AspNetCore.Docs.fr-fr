---
title: 'Didacticiel : prise en main des :::no-loc(Razor)::: pages dans ASP.net Core'
author: rick-anderson
description: 'Cette série de didacticiels montre comment utiliser :::no-loc(Razor)::: des pages dans ASP.net core. Découvrez comment créer un modèle, générer du code pour les :::no-loc(Razor)::: pages, utiliser des Entity Framework Core et des SQL Server pour l’accès aux données, ajouter des fonctionnalités de recherche, ajouter une validation d’entrée et utiliser des migrations pour mettre à jour le modèle.'
ms.author: riande
ms.date: 11/12/2019
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: ab890b956b1242f183054b7ab4575a59072b4f50
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060233"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="4bf5c-104">Didacticiel : prise en main des :::no-loc(Razor)::: pages dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="4bf5c-104">Tutorial: Get started with :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

<span data-ttu-id="4bf5c-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4bf5c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="4bf5c-106">Il s’agit du premier didacticiel d’une série qui enseigne les bases de la création d’une :::no-loc(Razor)::: application web ASP.net Core pages.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core :::no-loc(Razor)::: Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="4bf5c-107">À la fin de la série, vous disposez d’une application qui gère une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="4bf5c-108">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4bf5c-109">Créer une :::no-loc(Razor)::: application Web pages.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-109">Create a :::no-loc(Razor)::: Pages web app.</span></span>
> * <span data-ttu-id="4bf5c-110">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-110">Run the app.</span></span>
> * <span data-ttu-id="4bf5c-111">Examiner les fichiers projet.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-111">Examine the project files.</span></span>

<span data-ttu-id="4bf5c-112">À la fin de ce didacticiel, vous disposerez d’une :::no-loc(Razor)::: application Web pages de travail sur laquelle vous allez vous appuyer dans des didacticiels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-112">At the end of this tutorial, you'll have a working :::no-loc(Razor)::: Pages web app that you'll build on in later tutorials.</span></span>

![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="4bf5c-114">Prérequis</span><span class="sxs-lookup"><span data-stu-id="4bf5c-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4bf5c-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bf5c-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4bf5c-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bf5c-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4bf5c-117">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="4bf5c-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="4bf5c-118">Créer une :::no-loc(Razor)::: application Web pages</span><span class="sxs-lookup"><span data-stu-id="4bf5c-118">Create a :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4bf5c-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bf5c-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4bf5c-120">Dans Visual Studio, dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-120">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="4bf5c-121">Créez une application web ASP.NET Core, puis sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-121">Create a new ASP.NET Core Web Application and select **Next** .</span></span>
  <span data-ttu-id="4bf5c-122">![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="4bf5c-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="4bf5c-123">Nommez le projet **:::no-loc(Razor)::: PagesMovie** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-123">Name the project **:::no-loc(Razor):::PagesMovie** .</span></span> <span data-ttu-id="4bf5c-124">Il est important de nommer le projet *:::no-loc(Razor)::: PagesMovie* afin que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-124">It's important to name the project *:::no-loc(Razor):::PagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="4bf5c-125">![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="4bf5c-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="4bf5c-126">Sélectionnez **ASP.NET Core 3,1** dans la liste déroulante, **application Web** , puis sélectionnez **créer** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application** , and then select **Create** .</span></span>

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="4bf5c-128">Le projet de démarrage suivant est créé :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-128">The following starter project is created:</span></span>

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4bf5c-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bf5c-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4bf5c-131">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4bf5c-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="4bf5c-132">Accédez au répertoire (`cd`) qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="4bf5c-133">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o :::no-loc(Razor):::PagesMovie
  code -r :::no-loc(Razor):::PagesMovie
  ```

  * <span data-ttu-id="4bf5c-134">La `dotnet new` commande crée un :::no-loc(Razor)::: projet de pages dans le dossier *:::no-loc(Razor)::: PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-134">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *:::no-loc(Razor):::PagesMovie* folder.</span></span>
  * <span data-ttu-id="4bf5c-135">La `code` commande ouvre le dossier *:::no-loc(Razor)::: PagesMovie* dans l’instance actuelle de Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-135">The `code` command opens the *:::no-loc(Razor):::PagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="4bf5c-136">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « :::no-loc(Razor)::: PagesMovie ». Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="4bf5c-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from ':::no-loc(Razor):::PagesMovie'. Add them?**</span></span> <span data-ttu-id="4bf5c-137">Sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-137">Select **Yes** .</span></span>

  <span data-ttu-id="4bf5c-138">Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4bf5c-139">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="4bf5c-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4bf5c-140">Sélectionnez **Fichier** > **Nouvelle solution** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-140">Select **File** > **New Solution** .</span></span>

  ![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="4bf5c-142">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **Web Application**  >  **suivant** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next** .</span></span> <span data-ttu-id="4bf5c-143">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application Web application** console  >  **suivant** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next** .</span></span>

  ![sélection du modèle d’application Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="4bf5c-145">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="4bf5c-146">Vérifiez que **l’authentification** est définie sur **aucune authentification** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-146">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="4bf5c-147">Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de 3. x.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-147">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="4bf5c-148">Sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-148">Select **Next** .</span></span>

* <span data-ttu-id="4bf5c-149">Nommez le projet **:::no-loc(Razor)::: PagesMovie** , puis sélectionnez **créer** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-149">Name the project **:::no-loc(Razor):::PagesMovie** , and then select **Create** .</span></span>

  ![macOS nom du projet](razor-pages-start/_static/:::no-loc(Razor):::PagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="4bf5c-151">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="4bf5c-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="4bf5c-152">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="4bf5c-152">Examine the project files</span></span>

<span data-ttu-id="4bf5c-153">Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="4bf5c-154">Dossier Pages</span><span class="sxs-lookup"><span data-stu-id="4bf5c-154">Pages folder</span></span>

<span data-ttu-id="4bf5c-155">Contient :::no-loc(Razor)::: des pages et des fichiers de prise en charge.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-155">Contains :::no-loc(Razor)::: pages and supporting files.</span></span> <span data-ttu-id="4bf5c-156">Chaque :::no-loc(Razor)::: page est une paire de fichiers :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-156">Each :::no-loc(Razor)::: page is a pair of files:</span></span>

* <span data-ttu-id="4bf5c-157">Un fichier *. cshtml* qui contient le balisage HTML avec du code C# à l’aide de la :::no-loc(Razor)::: syntaxe.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-157">A *.cshtml* file that contains HTML markup with C# code using :::no-loc(Razor)::: syntax.</span></span>
* <span data-ttu-id="4bf5c-158">Un fichier *. cshtml.cs* qui contient du code C# gérant les événements de page.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="4bf5c-159">Les fichiers de prise en charge ont des noms commençant par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="4bf5c-160">Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="4bf5c-161">Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="4bf5c-162">Pour plus d'informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="4bf5c-163">Dossier racine</span><span class="sxs-lookup"><span data-stu-id="4bf5c-163">wwwroot folder</span></span>

<span data-ttu-id="4bf5c-164">Contient des fichiers statiques, tels que les fichiers HTML, JavaScript et CSS.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="4bf5c-165">Pour plus d'informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="4bf5c-166">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="4bf5c-166">appSettings.json</span></span>

<span data-ttu-id="4bf5c-167">Contient les données de configuration, comme les chaînes de connexion.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="4bf5c-168">Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="4bf5c-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="4bf5c-169">Program.cs</span></span>

<span data-ttu-id="4bf5c-170">Contient le point d’entrée pour le programme.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-170">Contains the entry point for the program.</span></span> <span data-ttu-id="4bf5c-171">Pour plus d'informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="4bf5c-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="4bf5c-172">Startup.cs</span></span>

<span data-ttu-id="4bf5c-173">contient le code qui configure le comportement de l’application.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="4bf5c-174">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4bf5c-175">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="4bf5c-175">Next steps</span></span>

<span data-ttu-id="4bf5c-176">Passez au tutoriel suivant dans la série :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4bf5c-177">Ajouter un modèle</span><span class="sxs-lookup"><span data-stu-id="4bf5c-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4bf5c-178">Ceci est le premier didacticiel d’une série.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="4bf5c-179">[La série](xref:tutorials/razor-pages/index) enseigne les bases de la création d’une :::no-loc(Razor)::: application Web ASP.net Core pages.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core :::no-loc(Razor)::: Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="4bf5c-180">À la fin de la série, vous disposez d’une application qui gère une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="4bf5c-181">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4bf5c-182">Créer une :::no-loc(Razor)::: application Web pages.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-182">Create a :::no-loc(Razor)::: Pages web app.</span></span>
> * <span data-ttu-id="4bf5c-183">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-183">Run the app.</span></span>
> * <span data-ttu-id="4bf5c-184">Examiner les fichiers projet.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-184">Examine the project files.</span></span>

<span data-ttu-id="4bf5c-185">À la fin de ce didacticiel, vous disposerez d’une :::no-loc(Razor)::: application Web pages de travail sur laquelle vous allez vous appuyer dans des didacticiels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-185">At the end of this tutorial, you'll have a working :::no-loc(Razor)::: Pages web app that you'll build on in later tutorials.</span></span>

![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="4bf5c-187">Prérequis</span><span class="sxs-lookup"><span data-stu-id="4bf5c-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4bf5c-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bf5c-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4bf5c-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bf5c-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4bf5c-190">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="4bf5c-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="4bf5c-191">Créer une :::no-loc(Razor)::: application Web pages</span><span class="sxs-lookup"><span data-stu-id="4bf5c-191">Create a :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4bf5c-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bf5c-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4bf5c-193">Dans Visual Studio, dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-193">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>

* <span data-ttu-id="4bf5c-194">Créez une application web ASP.NET Core, puis sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-194">Create a new ASP.NET Core Web Application and select **Next** .</span></span>

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="4bf5c-196">Nommez le projet **:::no-loc(Razor)::: PagesMovie** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-196">Name the project **:::no-loc(Razor):::PagesMovie** .</span></span> <span data-ttu-id="4bf5c-197">Il est important de nommer le projet *:::no-loc(Razor)::: PagesMovie* afin que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-197">It's important to name the project *:::no-loc(Razor):::PagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="4bf5c-199">Sélectionnez **ASP.NET Core 2.2** dans la liste déroulante, sélectionnez **Application web** , puis **Créer** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application** , and then select **Create** .</span></span>

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="4bf5c-201">Le projet de démarrage suivant est créé :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-201">The following starter project is created:</span></span>

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4bf5c-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bf5c-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4bf5c-204">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4bf5c-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="4bf5c-205">Accédez au répertoire (`cd`) qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="4bf5c-206">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o :::no-loc(Razor):::PagesMovie
  code -r :::no-loc(Razor):::PagesMovie
  ```

  * <span data-ttu-id="4bf5c-207">La `dotnet new` commande crée un :::no-loc(Razor)::: projet de pages dans le dossier *:::no-loc(Razor)::: PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-207">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *:::no-loc(Razor):::PagesMovie* folder.</span></span>
  * <span data-ttu-id="4bf5c-208">La `code` commande ouvre le dossier *:::no-loc(Razor)::: PagesMovie* dans l’instance actuelle de Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-208">The `code` command opens the *:::no-loc(Razor):::PagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="4bf5c-209">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « :::no-loc(Razor)::: PagesMovie ». Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="4bf5c-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from ':::no-loc(Razor):::PagesMovie'. Add them?**</span></span> <span data-ttu-id="4bf5c-210">Sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-210">Select **Yes** .</span></span>

  <span data-ttu-id="4bf5c-211">Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4bf5c-212">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="4bf5c-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4bf5c-213">Sélectionnez **Fichier** > **Nouvelle solution** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-213">Select **File** > **New Solution** .</span></span>

![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="4bf5c-215">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **Web Application**  >  **suivant** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next** .</span></span> <span data-ttu-id="4bf5c-216">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application Web application** console  >  **suivant** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next** .</span></span>

* <span data-ttu-id="4bf5c-217">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="4bf5c-218">Vérifiez que **l’authentification** est définie sur **aucune authentification** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-218">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="4bf5c-219">Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de 2. x.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-219">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="4bf5c-220">Sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-220">Select **Next** .</span></span>

* <span data-ttu-id="4bf5c-221">Nommez le projet **:::no-loc(Razor)::: PagesMovie** , puis sélectionnez **créer** .</span><span class="sxs-lookup"><span data-stu-id="4bf5c-221">Name the project **:::no-loc(Razor):::PagesMovie** , and then select **Create** .</span></span>

  ![nameproj](razor-pages-start/_static/:::no-loc(Razor):::PagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="4bf5c-223">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="4bf5c-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4bf5c-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bf5c-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4bf5c-225">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="4bf5c-226">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="4bf5c-227">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4bf5c-228">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="4bf5c-229">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="4bf5c-230">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="4bf5c-231">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4bf5c-232">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="4bf5c-234">L’illustration suivante montre l’application après avoir consenti au suivi :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="4bf5c-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bf5c-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="4bf5c-237">Appuyez sur **Ctrl+F5** pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="4bf5c-238">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="4bf5c-239">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4bf5c-240">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="4bf5c-241">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="4bf5c-242">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4bf5c-243">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="4bf5c-245">L’illustration suivante montre l’application après avoir consenti au suivi :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4bf5c-247">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="4bf5c-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="4bf5c-248">Appuyez sur **Cmd+Opt+F5** pour lancer l’exécution sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="4bf5c-249">Visual Studio démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="4bf5c-250">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4bf5c-251">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="4bf5c-253">L’illustration suivante montre l’application après avoir consenti au suivi :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="4bf5c-255">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="4bf5c-255">Examine the project files</span></span>

<span data-ttu-id="4bf5c-256">Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="4bf5c-257">Dossier Pages</span><span class="sxs-lookup"><span data-stu-id="4bf5c-257">Pages folder</span></span>

<span data-ttu-id="4bf5c-258">Contient :::no-loc(Razor)::: des pages et des fichiers de prise en charge.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-258">Contains :::no-loc(Razor)::: pages and supporting files.</span></span> <span data-ttu-id="4bf5c-259">Chaque :::no-loc(Razor)::: page est une paire de fichiers :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-259">Each :::no-loc(Razor)::: page is a pair of files:</span></span>

* <span data-ttu-id="4bf5c-260">Un fichier *. cshtml* qui contient le balisage HTML avec du code C# à l’aide de la :::no-loc(Razor)::: syntaxe.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-260">A *.cshtml* file that contains HTML markup with C# code using :::no-loc(Razor)::: syntax.</span></span>
* <span data-ttu-id="4bf5c-261">Un fichier *. cshtml.cs* qui contient du code C# gérant les événements de page.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="4bf5c-262">Les fichiers de prise en charge ont des noms commençant par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="4bf5c-263">Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="4bf5c-264">Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="4bf5c-265">Pour plus d'informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="4bf5c-266">Dossier racine</span><span class="sxs-lookup"><span data-stu-id="4bf5c-266">wwwroot folder</span></span>

<span data-ttu-id="4bf5c-267">Contient des fichiers statiques, tels que les fichiers HTML, JavaScript et CSS.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="4bf5c-268">Pour plus d'informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="4bf5c-269">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="4bf5c-269">appSettings.json</span></span>

<span data-ttu-id="4bf5c-270">Contient les données de configuration, comme les chaînes de connexion.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="4bf5c-271">Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="4bf5c-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="4bf5c-272">Program.cs</span></span>

<span data-ttu-id="4bf5c-273">Contient le point d’entrée pour le programme.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-273">Contains the entry point for the program.</span></span> <span data-ttu-id="4bf5c-274">Pour plus d'informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="4bf5c-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="4bf5c-275">Startup.cs</span></span>

<span data-ttu-id="4bf5c-276">Contient le code qui configure le comportement de l’application, par exemple s’il requiert le consentement pour les :::no-loc(cookie)::: s.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-276">Contains code that configures app behavior, such as whether it requires consent for :::no-loc(cookie):::s.</span></span> <span data-ttu-id="4bf5c-277">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4bf5c-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4bf5c-278">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4bf5c-278">Additional resources</span></span>

* [<span data-ttu-id="4bf5c-279">Version YouTube de ce didacticiel</span><span class="sxs-lookup"><span data-stu-id="4bf5c-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="4bf5c-280">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="4bf5c-280">Next steps</span></span>

<span data-ttu-id="4bf5c-281">Passez au tutoriel suivant dans la série :</span><span class="sxs-lookup"><span data-stu-id="4bf5c-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4bf5c-282">Ajouter un modèle</span><span class="sxs-lookup"><span data-stu-id="4bf5c-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
