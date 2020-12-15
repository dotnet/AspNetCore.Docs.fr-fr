---
title: 'Didacticiel : prise en main des Razor pages dans ASP.net Core'
author: rick-anderson
description: Il s’agit du premier didacticiel d’une série qui enseigne les bases de la création d’une Razor application web ASP.net Core pages.
ms.author: riande
ms.date: 09/15/2020
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 4d4e50f8acea73859f5e839616f13f90a42291c4
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486224"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="ac106-103">Didacticiel : prise en main des Razor pages dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ac106-103">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="ac106-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ac106-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="ac106-105">Il s’agit du premier didacticiel d’une série qui enseigne les bases de la création d’une Razor application web ASP.net Core pages.</span><span class="sxs-lookup"><span data-stu-id="ac106-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="ac106-106">Pour obtenir une présentation plus avancée destinée aux développeurs qui connaissent bien les contrôleurs et les vues, consultez [Présentation des Razor pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="ac106-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="ac106-107">À la fin de la série, vous disposez d’une application qui gère une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="ac106-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="ac106-108">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ac106-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ac106-109">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="ac106-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ac106-110">Créer une Razor application Web pages.</span><span class="sxs-lookup"><span data-stu-id="ac106-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="ac106-111">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="ac106-111">Run the app.</span></span>
> * <span data-ttu-id="ac106-112">Examiner les fichiers projet.</span><span class="sxs-lookup"><span data-stu-id="ac106-112">Examine the project files.</span></span>

<span data-ttu-id="ac106-113">À la fin de ce didacticiel, vous disposerez d’une Razor application Web pages de travail que vous allez améliorer dans les didacticiels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="ac106-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll enhance in later tutorials.</span></span>

![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="ac106-115">Prérequis</span><span class="sxs-lookup"><span data-stu-id="ac106-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac106-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac106-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac106-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac106-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ac106-118">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="ac106-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="ac106-119">Créer une Razor application Web pages</span><span class="sxs-lookup"><span data-stu-id="ac106-119">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac106-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac106-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ac106-121">Démarrez Visual Studio et sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="ac106-121">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="ac106-122">Pour plus d’informations, consultez [créer un nouveau projet dans Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="ac106-122">For more information, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![Créer un projet à partir de la fenêtre démarrer](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="ac106-124">Dans la boîte de dialogue **Créer un projet**, sélectionnez **Application web ASP.NET Core**, puis **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="ac106-124">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

    ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="ac106-126">Dans la boîte de dialogue **configurer votre nouveau projet** , entrez `RazorPagesMovie` pour **nom du projet**.</span><span class="sxs-lookup"><span data-stu-id="ac106-126">In the **Configure your new project** dialog, enter `RazorPagesMovie` for **Project name**.</span></span> <span data-ttu-id="ac106-127">Il est important de nommer le projet *Razor PagesMovie*, y compris la mise en majuscules, afin que les espaces de noms correspondent quand vous copiez et collez l’exemple de code.</span><span class="sxs-lookup"><span data-stu-id="ac106-127">It's important to name the project *RazorPagesMovie*, including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="ac106-128">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="ac106-128">Select **Create**.</span></span>

    ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

1. <span data-ttu-id="ac106-130">Dans la boîte de dialogue **créer une application web ASP.net Core** , sélectionnez :</span><span class="sxs-lookup"><span data-stu-id="ac106-130">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="ac106-131">**.Net Core** et **ASP.net Core 5,0** dans les listes déroulantes.</span><span class="sxs-lookup"><span data-stu-id="ac106-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="ac106-132">**Application Web**.</span><span class="sxs-lookup"><span data-stu-id="ac106-132">**Web Application**.</span></span>
    1. <span data-ttu-id="ac106-133">**Créer**.</span><span class="sxs-lookup"><span data-stu-id="ac106-133">**Create**.</span></span>

     ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="ac106-135">Le projet de démarrage suivant est créé :</span><span class="sxs-lookup"><span data-stu-id="ac106-135">The following starter project is created:</span></span>

    ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac106-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac106-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ac106-138">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ac106-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="ac106-139">Accédez au répertoire (`cd`) qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="ac106-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="ac106-140">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="ac106-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * <span data-ttu-id="ac106-141">La `dotnet new` commande crée un Razor projet de pages dans le dossier *Razor PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="ac106-141">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
   * <span data-ttu-id="ac106-142">La `code` commande ouvre le dossier *Razor PagesMovie* dans l’instance actuelle de Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="ac106-142">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ac106-143">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="ac106-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ac106-144">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="ac106-144">Select **File** > **New Solution**.</span></span>

    ![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="ac106-146">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >    >    >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="ac106-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="ac106-147">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >    >  **application Web application** console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="ac106-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![sélection du modèle d’application Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="ac106-149">Dans la boîte de dialogue **configurer la nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="ac106-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="ac106-150">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="ac106-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="ac106-151">Si vous avez présenté une option permettant de sélectionner un **Framework cible**, sélectionnez la version la plus récente de .net 5. x.</span><span class="sxs-lookup"><span data-stu-id="ac106-151">If presented an option to select a **Target Framework**, select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="ac106-152">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="ac106-152">Select **Next**.</span></span>

1. <span data-ttu-id="ac106-153">Nommez le projet *Razor PagesMovie* et sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="ac106-153">Name the project *RazorPagesMovie* and select **Create**.</span></span>

    ![macOS nom du projet](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="ac106-155">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="ac106-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="ac106-156">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="ac106-156">Examine the project files</span></span>

<span data-ttu-id="ac106-157">Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="ac106-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="ac106-158">Dossier Pages</span><span class="sxs-lookup"><span data-stu-id="ac106-158">Pages folder</span></span>

<span data-ttu-id="ac106-159">Contient Razor des pages et des fichiers de prise en charge.</span><span class="sxs-lookup"><span data-stu-id="ac106-159">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="ac106-160">Chaque Razor page est une paire de fichiers :</span><span class="sxs-lookup"><span data-stu-id="ac106-160">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="ac106-161">Un fichier *. cshtml* contenant du balisage HTML avec du code C# à l’aide de la Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="ac106-161">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="ac106-162">Un fichier *. cshtml.cs* qui contient le code C# qui gère les événements de page.</span><span class="sxs-lookup"><span data-stu-id="ac106-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="ac106-163">Les fichiers de prise en charge ont des noms commençant par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="ac106-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="ac106-164">Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages.</span><span class="sxs-lookup"><span data-stu-id="ac106-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="ac106-165">Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page.</span><span class="sxs-lookup"><span data-stu-id="ac106-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="ac106-166">Pour plus d’informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="ac106-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="ac106-167">Dossier racine</span><span class="sxs-lookup"><span data-stu-id="ac106-167">wwwroot folder</span></span>

<span data-ttu-id="ac106-168">Contient des ressources statiques, telles que des fichiers HTML, des fichiers JavaScript et des fichiers CSS.</span><span class="sxs-lookup"><span data-stu-id="ac106-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="ac106-169">Pour plus d’informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="ac106-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### appsettings.json

<span data-ttu-id="ac106-170">Contient des données de configuration, telles que des chaînes de connexion.</span><span class="sxs-lookup"><span data-stu-id="ac106-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="ac106-171">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ac106-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="ac106-172">Program.cs</span><span class="sxs-lookup"><span data-stu-id="ac106-172">Program.cs</span></span>

<span data-ttu-id="ac106-173">Contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="ac106-173">Contains the entry point for the app.</span></span> <span data-ttu-id="ac106-174">Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="ac106-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="ac106-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="ac106-175">Startup.cs</span></span>

<span data-ttu-id="ac106-176">contient le code qui configure le comportement de l’application.</span><span class="sxs-lookup"><span data-stu-id="ac106-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="ac106-177">Pour plus d’informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ac106-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ac106-178">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="ac106-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ac106-179">Suivant : ajouter un modèle</span><span class="sxs-lookup"><span data-stu-id="ac106-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="ac106-180">Il s’agit du premier didacticiel d’une série qui enseigne les bases de la création d’une Razor application web ASP.net Core pages.</span><span class="sxs-lookup"><span data-stu-id="ac106-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="ac106-181">Pour obtenir une présentation plus avancée destinée aux développeurs qui connaissent bien les contrôleurs et les vues, consultez [Présentation des Razor pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="ac106-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="ac106-182">À la fin de la série, vous disposez d’une application qui gère une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="ac106-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="ac106-183">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ac106-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ac106-184">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="ac106-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ac106-185">Créer une Razor application Web pages.</span><span class="sxs-lookup"><span data-stu-id="ac106-185">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="ac106-186">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="ac106-186">Run the app.</span></span>
> * <span data-ttu-id="ac106-187">Examiner les fichiers projet.</span><span class="sxs-lookup"><span data-stu-id="ac106-187">Examine the project files.</span></span>

<span data-ttu-id="ac106-188">À la fin de ce didacticiel, vous disposerez d’une Razor application Web pages de travail sur laquelle vous allez vous appuyer dans des didacticiels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="ac106-188">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="ac106-190">Prérequis</span><span class="sxs-lookup"><span data-stu-id="ac106-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac106-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac106-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac106-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac106-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ac106-193">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="ac106-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="ac106-194">Créer une Razor application Web pages</span><span class="sxs-lookup"><span data-stu-id="ac106-194">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac106-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac106-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac106-196">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="ac106-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ac106-197">Créez une application web ASP.NET Core, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="ac106-197">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="ac106-198">![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="ac106-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="ac106-199">Nommez le projet **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="ac106-199">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="ac106-200">Il est important de nommer le projet *Razor PagesMovie* afin que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="ac106-200">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="ac106-201">![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="ac106-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="ac106-202">Sélectionnez **ASP.NET Core 3,1** dans la liste déroulante, **application Web**, puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="ac106-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="ac106-204">Le projet de démarrage suivant est créé :</span><span class="sxs-lookup"><span data-stu-id="ac106-204">The following starter project is created:</span></span>

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac106-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac106-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac106-207">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ac106-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="ac106-208">Accédez au répertoire (`cd`) qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="ac106-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="ac106-209">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="ac106-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="ac106-210">La `dotnet new` commande crée un Razor projet de pages dans le dossier *Razor PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="ac106-210">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="ac106-211">La `code` commande ouvre le dossier *Razor PagesMovie* dans l’instance actuelle de Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="ac106-211">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="ac106-212">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « Razor PagesMovie ». Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="ac106-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="ac106-213">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="ac106-213">Select **Yes**.</span></span>

  <span data-ttu-id="ac106-214">Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="ac106-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ac106-215">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="ac106-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ac106-216">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="ac106-216">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="ac106-218">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >    >    >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="ac106-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="ac106-219">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >    >  **application Web application** console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="ac106-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![sélection du modèle d’application Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="ac106-221">Dans la boîte de dialogue **configurer la nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="ac106-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="ac106-222">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="ac106-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="ac106-223">Si vous avez présenté une option permettant de sélectionner un **Framework cible**, sélectionnez la version la plus récente de 3. x.</span><span class="sxs-lookup"><span data-stu-id="ac106-223">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="ac106-224">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="ac106-224">Select **Next**.</span></span>

* <span data-ttu-id="ac106-225">Nommez le projet **Razor PagesMovie**, puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="ac106-225">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS nom du projet](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="ac106-227">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="ac106-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="ac106-228">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="ac106-228">Examine the project files</span></span>

<span data-ttu-id="ac106-229">Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="ac106-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="ac106-230">Dossier Pages</span><span class="sxs-lookup"><span data-stu-id="ac106-230">Pages folder</span></span>

<span data-ttu-id="ac106-231">Contient Razor des pages et des fichiers de prise en charge.</span><span class="sxs-lookup"><span data-stu-id="ac106-231">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="ac106-232">Chaque Razor page est une paire de fichiers :</span><span class="sxs-lookup"><span data-stu-id="ac106-232">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="ac106-233">Un fichier *. cshtml* contenant du balisage HTML avec du code C# à l’aide de la Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="ac106-233">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="ac106-234">Un fichier *. cshtml.cs* qui contient le code C# qui gère les événements de page.</span><span class="sxs-lookup"><span data-stu-id="ac106-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="ac106-235">Les fichiers de prise en charge ont des noms commençant par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="ac106-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="ac106-236">Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages.</span><span class="sxs-lookup"><span data-stu-id="ac106-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="ac106-237">Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page.</span><span class="sxs-lookup"><span data-stu-id="ac106-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="ac106-238">Pour plus d’informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="ac106-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="ac106-239">Dossier racine</span><span class="sxs-lookup"><span data-stu-id="ac106-239">wwwroot folder</span></span>

<span data-ttu-id="ac106-240">Contient des fichiers statiques, tels que des fichiers HTML, des fichiers JavaScript et des fichiers CSS.</span><span class="sxs-lookup"><span data-stu-id="ac106-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="ac106-241">Pour plus d’informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="ac106-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="ac106-242">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="ac106-242">appSettings.json</span></span>

<span data-ttu-id="ac106-243">Contient des données de configuration, telles que des chaînes de connexion.</span><span class="sxs-lookup"><span data-stu-id="ac106-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="ac106-244">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ac106-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="ac106-245">Program.cs</span><span class="sxs-lookup"><span data-stu-id="ac106-245">Program.cs</span></span>

<span data-ttu-id="ac106-246">Contient le point d’entrée pour le programme.</span><span class="sxs-lookup"><span data-stu-id="ac106-246">Contains the entry point for the program.</span></span> <span data-ttu-id="ac106-247">Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="ac106-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="ac106-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="ac106-248">Startup.cs</span></span>

<span data-ttu-id="ac106-249">contient le code qui configure le comportement de l’application.</span><span class="sxs-lookup"><span data-stu-id="ac106-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="ac106-250">Pour plus d’informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ac106-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ac106-251">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="ac106-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ac106-252">Suivant : ajouter un modèle</span><span class="sxs-lookup"><span data-stu-id="ac106-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ac106-253">Ceci est le premier didacticiel d’une série.</span><span class="sxs-lookup"><span data-stu-id="ac106-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="ac106-254">[La série](xref:tutorials/razor-pages/index) enseigne les bases de la création d’une Razor application Web ASP.net Core pages.</span><span class="sxs-lookup"><span data-stu-id="ac106-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="ac106-255">Pour obtenir une présentation plus avancée destinée aux développeurs qui connaissent bien les contrôleurs et les vues, consultez [Présentation des Razor pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="ac106-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="ac106-256">À la fin de la série, vous disposez d’une application qui gère une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="ac106-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="ac106-257">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ac106-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ac106-258">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="ac106-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ac106-259">Créer une Razor application Web pages.</span><span class="sxs-lookup"><span data-stu-id="ac106-259">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="ac106-260">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="ac106-260">Run the app.</span></span>
> * <span data-ttu-id="ac106-261">Examiner les fichiers projet.</span><span class="sxs-lookup"><span data-stu-id="ac106-261">Examine the project files.</span></span>

<span data-ttu-id="ac106-262">À la fin de ce didacticiel, vous disposerez d’une Razor application Web pages de travail sur laquelle vous allez vous appuyer dans des didacticiels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="ac106-262">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="ac106-264">Prérequis</span><span class="sxs-lookup"><span data-stu-id="ac106-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac106-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac106-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac106-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac106-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ac106-267">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="ac106-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="ac106-268">Créer une Razor application Web pages</span><span class="sxs-lookup"><span data-stu-id="ac106-268">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac106-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac106-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac106-270">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="ac106-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="ac106-271">Créez une application web ASP.NET Core, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="ac106-271">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="ac106-273">Nommez le projet **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="ac106-273">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="ac106-274">Il est important de nommer le projet *Razor PagesMovie* afin que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="ac106-274">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="ac106-276">Sélectionnez **ASP.NET Core 2.2** dans la liste déroulante, sélectionnez **Application web**, puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="ac106-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="ac106-278">Le projet de démarrage suivant est créé :</span><span class="sxs-lookup"><span data-stu-id="ac106-278">The following starter project is created:</span></span>

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac106-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac106-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac106-281">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ac106-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="ac106-282">Accédez au répertoire (`cd`) qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="ac106-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="ac106-283">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="ac106-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="ac106-284">La `dotnet new` commande crée un Razor projet de pages dans le dossier *Razor PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="ac106-284">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="ac106-285">La `code` commande ouvre le dossier *Razor PagesMovie* dans l’instance actuelle de Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="ac106-285">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="ac106-286">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « Razor PagesMovie ». Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="ac106-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="ac106-287">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="ac106-287">Select **Yes**.</span></span>

  <span data-ttu-id="ac106-288">Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="ac106-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ac106-289">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="ac106-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ac106-290">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="ac106-290">Select **File** > **New Solution**.</span></span>

![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="ac106-292">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >    >    >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="ac106-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="ac106-293">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >    >  **application Web application** console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="ac106-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="ac106-294">Dans la boîte de dialogue **configurer la nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="ac106-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="ac106-295">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="ac106-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="ac106-296">Si vous avez présenté une option permettant de sélectionner un **Framework cible**, sélectionnez la version la plus récente de 2. x.</span><span class="sxs-lookup"><span data-stu-id="ac106-296">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="ac106-297">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="ac106-297">Select **Next**.</span></span>

* <span data-ttu-id="ac106-298">Nommez le projet **Razor PagesMovie**, puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="ac106-298">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="ac106-300">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="ac106-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac106-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac106-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac106-302">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="ac106-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="ac106-303">Le lancement de l’application avec <kbd>CTRL + F5</kbd> (mode non débogage) vous permet d’apporter des modifications au code, d’enregistrer le fichier, d’actualiser le navigateur et de voir les modifications du code.</span><span class="sxs-lookup"><span data-stu-id="ac106-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="ac106-304">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="ac106-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="ac106-305">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="ac106-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="ac106-306">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="ac106-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ac106-307">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="ac106-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="ac106-308">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="ac106-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="ac106-309">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="ac106-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="ac106-310">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="ac106-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="ac106-311">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="ac106-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="ac106-313">L’illustration suivante montre l’application après avoir donné son consentement au suivi :</span><span class="sxs-lookup"><span data-stu-id="ac106-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="ac106-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac106-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="ac106-316">Appuyez sur <kbd>CTRL + F5</kbd> pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="ac106-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="ac106-317">Le lancement de l’application avec <kbd>CTRL + F5</kbd> (mode non débogage) vous permet d’apporter des modifications au code, d’enregistrer le fichier, d’actualiser le navigateur et de voir les modifications du code.</span><span class="sxs-lookup"><span data-stu-id="ac106-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="ac106-318">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="ac106-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="ac106-319">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur et accède à `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="ac106-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="ac106-320">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="ac106-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ac106-321">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="ac106-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="ac106-322">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="ac106-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="ac106-323">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="ac106-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="ac106-324">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="ac106-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="ac106-326">L’illustration suivante montre l’application après avoir consenti au suivi :</span><span class="sxs-lookup"><span data-stu-id="ac106-326">The following image shows the app after you give consent to tracking:</span></span>

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ac106-328">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="ac106-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="ac106-329">Appuyez sur **Cmd+Opt+F5** pour lancer l’exécution sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="ac106-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="ac106-330">Le lancement de l’application avec <kbd>cmd + opt + F5</kbd> (mode non débogage) vous permet d’apporter des modifications au code, d’enregistrer le fichier, d’actualiser le navigateur et de voir les modifications du code.</span><span class="sxs-lookup"><span data-stu-id="ac106-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="ac106-331">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="ac106-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="ac106-332">Visual Studio démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur et accède à `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="ac106-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="ac106-333">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="ac106-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="ac106-334">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="ac106-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="ac106-336">L’illustration suivante montre l’application après avoir donné son consentement au suivi :</span><span class="sxs-lookup"><span data-stu-id="ac106-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="ac106-338">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="ac106-338">Examine the project files</span></span>

<span data-ttu-id="ac106-339">Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="ac106-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="ac106-340">Dossier Pages</span><span class="sxs-lookup"><span data-stu-id="ac106-340">Pages folder</span></span>

<span data-ttu-id="ac106-341">Contient Razor des pages et des fichiers de prise en charge.</span><span class="sxs-lookup"><span data-stu-id="ac106-341">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="ac106-342">Chaque Razor page est une paire de fichiers :</span><span class="sxs-lookup"><span data-stu-id="ac106-342">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="ac106-343">Un fichier *. cshtml* contenant du balisage HTML avec du code C# à l’aide de la Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="ac106-343">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="ac106-344">Un fichier *. cshtml.cs* qui contient le code C# qui gère les événements de page.</span><span class="sxs-lookup"><span data-stu-id="ac106-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="ac106-345">Les fichiers de prise en charge ont des noms commençant par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="ac106-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="ac106-346">Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages.</span><span class="sxs-lookup"><span data-stu-id="ac106-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="ac106-347">Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page.</span><span class="sxs-lookup"><span data-stu-id="ac106-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="ac106-348">Pour plus d’informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="ac106-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="ac106-349">Razor Les pages sont dérivées de `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="ac106-349">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="ac106-350">Par Convention, la `PageModel` classe dérivée de est nommée `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="ac106-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="ac106-351">Dossier racine</span><span class="sxs-lookup"><span data-stu-id="ac106-351">wwwroot folder</span></span>

<span data-ttu-id="ac106-352">Contient des fichiers statiques, tels que des fichiers HTML, des fichiers JavaScript et des fichiers CSS.</span><span class="sxs-lookup"><span data-stu-id="ac106-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="ac106-353">Pour plus d’informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="ac106-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="ac106-354">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="ac106-354">appSettings.json</span></span>

<span data-ttu-id="ac106-355">Contient des données de configuration, telles que des chaînes de connexion.</span><span class="sxs-lookup"><span data-stu-id="ac106-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="ac106-356">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ac106-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="ac106-357">Program.cs</span><span class="sxs-lookup"><span data-stu-id="ac106-357">Program.cs</span></span>

<span data-ttu-id="ac106-358">Contient le point d’entrée pour le programme.</span><span class="sxs-lookup"><span data-stu-id="ac106-358">Contains the entry point for the program.</span></span> <span data-ttu-id="ac106-359">Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="ac106-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="ac106-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="ac106-360">Startup.cs</span></span>

<span data-ttu-id="ac106-361">Contient le code qui configure le comportement de l’application, par exemple s’il requiert le consentement pour les cookie s.</span><span class="sxs-lookup"><span data-stu-id="ac106-361">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="ac106-362">Pour plus d’informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ac106-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac106-363">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ac106-363">Additional resources</span></span>

* [<span data-ttu-id="ac106-364">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="ac106-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="ac106-365">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="ac106-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ac106-366">Suivant : ajouter un modèle</span><span class="sxs-lookup"><span data-stu-id="ac106-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
