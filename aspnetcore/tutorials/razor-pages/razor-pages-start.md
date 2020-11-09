---
title: 'Didacticiel : prise en main des Razor pages dans ASP.net Core'
author: rick-anderson
description: 'Il s’agit du premier didacticiel d’une série qui enseigne les bases de la création d’une Razor application web ASP.net Core pages.'
ms.author: riande
ms.date: 09/15/2020
no-loc:
- 'Index'
- 'Create'
- 'Delete'
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: b4dcbe9536107cdc5b0342782abc4bad0b89a8dc
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360930"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="fe8bd-103">Didacticiel : prise en main des Razor pages dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="fe8bd-103">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="fe8bd-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fe8bd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="fe8bd-105">Il s’agit du premier didacticiel d’une série qui enseigne les bases de la création d’une Razor application web ASP.net Core pages.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="fe8bd-106">Pour obtenir une présentation plus avancée destinée aux développeurs qui connaissent bien les contrôleurs et les vues, consultez [Présentation des Razor pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="fe8bd-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="fe8bd-107">À la fin de la série, vous disposez d’une application qui gère une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="fe8bd-108">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fe8bd-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="fe8bd-109">Dans ce tutoriel, vous avez :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fe8bd-110">CreateRazorapplication Web pages.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="fe8bd-111">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-111">Run the app.</span></span>
> * <span data-ttu-id="fe8bd-112">Examiner les fichiers projet.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-112">Examine the project files.</span></span>

<span data-ttu-id="fe8bd-113">À la fin de ce didacticiel, vous disposerez d’une Razor application Web pages de travail que vous allez améliorer dans les didacticiels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll enhance in later tutorials.</span></span>

![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="fe8bd-115">Prérequis</span><span class="sxs-lookup"><span data-stu-id="fe8bd-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe8bd-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe8bd-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe8bd-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe8bd-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe8bd-118">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="fe8bd-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="fe8bd-119">Create une Razor application Web pages</span><span class="sxs-lookup"><span data-stu-id="fe8bd-119">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe8bd-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe8bd-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="fe8bd-121">Démarrez Visual Studio et sélectionnez **Create un nouveau projet**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-121">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="fe8bd-122">Pour plus d’informations, consultez [ Create un nouveau projet dans Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="fe8bd-122">For more information, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![::: No-Loc (créer) ::: un nouveau projet à partir de la fenêtre de démarrage](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="fe8bd-124">Dans la boîte de dialogue **Create nouveau projet** , sélectionnez **ASP.net Core application Web** , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-124">In the **Create a new project** dialog, select **ASP.NET Core Web Application** , and then select **Next**.</span></span>

    ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="fe8bd-126">Dans la boîte de dialogue **configurer votre nouveau projet** , entrez `RazorPagesMovie` pour **nom du projet**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-126">In the **Configure your new project** dialog, enter `RazorPagesMovie` for **Project name**.</span></span> <span data-ttu-id="fe8bd-127">Il est important de nommer le projet *Razor PagesMovie* , y compris la mise en majuscules, afin que les espaces de noms correspondent quand vous copiez et collez l’exemple de code.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-127">It's important to name the project *RazorPagesMovie* , including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="fe8bd-128">Sélectionnez **Create**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-128">Select **Create**.</span></span>

    ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

1. <span data-ttu-id="fe8bd-130">Dans la boîte de dialogue **Create nouvelle ASP.net Core application Web** , sélectionnez :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-130">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="fe8bd-131">**.Net Core** et **ASP.net Core 5,0** dans les listes déroulantes.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="fe8bd-132">**Application Web**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-132">**Web Application**.</span></span>
    1. <span data-ttu-id="fe8bd-133">**Create**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-133">**Create**.</span></span>

     ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="fe8bd-135">Le projet de démarrage suivant est créé :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-135">The following starter project is created:</span></span>

    ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe8bd-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe8bd-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="fe8bd-138">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="fe8bd-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="fe8bd-139">Accédez au répertoire (`cd`) qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="fe8bd-140">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * <span data-ttu-id="fe8bd-141">La `dotnet new` commande crée un Razor projet de pages dans le dossier *Razor PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="fe8bd-141">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
   * <span data-ttu-id="fe8bd-142">La `code` commande ouvre le dossier *Razor PagesMovie* dans l’instance actuelle de Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-142">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe8bd-143">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="fe8bd-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="fe8bd-144">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-144">Select **File** > **New Solution**.</span></span>

    ![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="fe8bd-146">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **Web Application**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="fe8bd-147">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application Web application** console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![sélection du modèle d’application Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="fe8bd-149">Dans la boîte de dialogue **configurer la nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="fe8bd-150">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="fe8bd-151">Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de .net 5. x.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-151">If presented an option to select a **Target Framework** , select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="fe8bd-152">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-152">Select **Next**.</span></span>

1. <span data-ttu-id="fe8bd-153">Nommez le projet *Razor PagesMovie* et sélectionnez **Create** .</span><span class="sxs-lookup"><span data-stu-id="fe8bd-153">Name the project *RazorPagesMovie* and select **Create**.</span></span>

    ![macOS nom du projet](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="fe8bd-155">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="fe8bd-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="fe8bd-156">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="fe8bd-156">Examine the project files</span></span>

<span data-ttu-id="fe8bd-157">Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="fe8bd-158">Dossier Pages</span><span class="sxs-lookup"><span data-stu-id="fe8bd-158">Pages folder</span></span>

<span data-ttu-id="fe8bd-159">Contient Razor des pages et des fichiers de prise en charge.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-159">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="fe8bd-160">Chaque Razor page est une paire de fichiers :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-160">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="fe8bd-161">Un fichier *. cshtml* contenant du balisage HTML avec du code C# à l’aide de la Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-161">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="fe8bd-162">Un fichier *. cshtml.cs* qui contient le code C# qui gère les événements de page.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="fe8bd-163">Les fichiers de prise en charge ont des noms commençant par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="fe8bd-164">Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="fe8bd-165">Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="fe8bd-166">Pour plus d'informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="fe8bd-167">Dossier racine</span><span class="sxs-lookup"><span data-stu-id="fe8bd-167">wwwroot folder</span></span>

<span data-ttu-id="fe8bd-168">Contient des ressources statiques, telles que des fichiers HTML, des fichiers JavaScript et des fichiers CSS.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="fe8bd-169">Pour plus d'informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### appsettings.json

<span data-ttu-id="fe8bd-170">Contient des données de configuration, telles que des chaînes de connexion.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="fe8bd-171">Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="fe8bd-172">Program.cs</span><span class="sxs-lookup"><span data-stu-id="fe8bd-172">Program.cs</span></span>

<span data-ttu-id="fe8bd-173">Contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-173">Contains the entry point for the app.</span></span> <span data-ttu-id="fe8bd-174">Pour plus d'informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="fe8bd-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="fe8bd-175">Startup.cs</span></span>

<span data-ttu-id="fe8bd-176">contient le code qui configure le comportement de l’application.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="fe8bd-177">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fe8bd-178">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="fe8bd-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="fe8bd-179">Suivant : ajouter un modèle</span><span class="sxs-lookup"><span data-stu-id="fe8bd-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="fe8bd-180">Il s’agit du premier didacticiel d’une série qui enseigne les bases de la création d’une Razor application web ASP.net Core pages.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="fe8bd-181">Pour obtenir une présentation plus avancée destinée aux développeurs qui connaissent bien les contrôleurs et les vues, consultez [Présentation des Razor pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="fe8bd-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="fe8bd-182">À la fin de la série, vous disposez d’une application qui gère une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="fe8bd-183">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fe8bd-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="fe8bd-184">Dans ce tutoriel, vous avez :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fe8bd-185">CreateRazorapplication Web pages.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-185">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="fe8bd-186">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-186">Run the app.</span></span>
> * <span data-ttu-id="fe8bd-187">Examiner les fichiers projet.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-187">Examine the project files.</span></span>

<span data-ttu-id="fe8bd-188">À la fin de ce didacticiel, vous disposerez d’une Razor application Web pages de travail sur laquelle vous allez vous appuyer dans des didacticiels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-188">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="fe8bd-190">Prérequis</span><span class="sxs-lookup"><span data-stu-id="fe8bd-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe8bd-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe8bd-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe8bd-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe8bd-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe8bd-193">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="fe8bd-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="fe8bd-194">Create une Razor application Web pages</span><span class="sxs-lookup"><span data-stu-id="fe8bd-194">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe8bd-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe8bd-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fe8bd-196">Dans Visual Studio, dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="fe8bd-197">Create une nouvelle ASP.NET Core application Web, puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-197">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="fe8bd-198">![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="fe8bd-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="fe8bd-199">Nommez le projet **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-199">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="fe8bd-200">Il est important de nommer le projet *Razor PagesMovie* afin que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-200">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="fe8bd-201">![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="fe8bd-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="fe8bd-202">Sélectionnez **ASP.NET Core 3,1** dans la liste déroulante, **application Web** , puis sélectionnez **Create** .</span><span class="sxs-lookup"><span data-stu-id="fe8bd-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application** , and then select **Create**.</span></span>

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="fe8bd-204">Le projet de démarrage suivant est créé :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-204">The following starter project is created:</span></span>

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe8bd-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe8bd-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fe8bd-207">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="fe8bd-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="fe8bd-208">Accédez au répertoire (`cd`) qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="fe8bd-209">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="fe8bd-210">La `dotnet new` commande crée un Razor projet de pages dans le dossier *Razor PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="fe8bd-210">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="fe8bd-211">La `code` commande ouvre le dossier *Razor PagesMovie* dans l’instance actuelle de Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-211">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="fe8bd-212">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « Razor PagesMovie ». Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="fe8bd-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="fe8bd-213">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-213">Select **Yes**.</span></span>

  <span data-ttu-id="fe8bd-214">Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe8bd-215">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="fe8bd-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fe8bd-216">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-216">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="fe8bd-218">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **Web Application**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="fe8bd-219">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application Web application** console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![sélection du modèle d’application Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="fe8bd-221">Dans la boîte de dialogue **configurer la nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="fe8bd-222">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="fe8bd-223">Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de 3. x.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-223">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="fe8bd-224">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-224">Select **Next**.</span></span>

* <span data-ttu-id="fe8bd-225">Nommez le projet **Razor PagesMovie** , puis sélectionnez **Create** .</span><span class="sxs-lookup"><span data-stu-id="fe8bd-225">Name the project **RazorPagesMovie** , and then select **Create**.</span></span>

  ![macOS nom du projet](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="fe8bd-227">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="fe8bd-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="fe8bd-228">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="fe8bd-228">Examine the project files</span></span>

<span data-ttu-id="fe8bd-229">Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="fe8bd-230">Dossier Pages</span><span class="sxs-lookup"><span data-stu-id="fe8bd-230">Pages folder</span></span>

<span data-ttu-id="fe8bd-231">Contient Razor des pages et des fichiers de prise en charge.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-231">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="fe8bd-232">Chaque Razor page est une paire de fichiers :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-232">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="fe8bd-233">Un fichier *. cshtml* contenant du balisage HTML avec du code C# à l’aide de la Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-233">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="fe8bd-234">Un fichier *. cshtml.cs* qui contient le code C# qui gère les événements de page.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="fe8bd-235">Les fichiers de prise en charge ont des noms commençant par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="fe8bd-236">Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="fe8bd-237">Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="fe8bd-238">Pour plus d'informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="fe8bd-239">Dossier racine</span><span class="sxs-lookup"><span data-stu-id="fe8bd-239">wwwroot folder</span></span>

<span data-ttu-id="fe8bd-240">Contient des fichiers statiques, tels que des fichiers HTML, des fichiers JavaScript et des fichiers CSS.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="fe8bd-241">Pour plus d'informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="fe8bd-242">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="fe8bd-242">appSettings.json</span></span>

<span data-ttu-id="fe8bd-243">Contient des données de configuration, telles que des chaînes de connexion.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="fe8bd-244">Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="fe8bd-245">Program.cs</span><span class="sxs-lookup"><span data-stu-id="fe8bd-245">Program.cs</span></span>

<span data-ttu-id="fe8bd-246">Contient le point d’entrée pour le programme.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-246">Contains the entry point for the program.</span></span> <span data-ttu-id="fe8bd-247">Pour plus d'informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="fe8bd-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="fe8bd-248">Startup.cs</span></span>

<span data-ttu-id="fe8bd-249">contient le code qui configure le comportement de l’application.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="fe8bd-250">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fe8bd-251">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="fe8bd-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="fe8bd-252">Suivant : ajouter un modèle</span><span class="sxs-lookup"><span data-stu-id="fe8bd-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fe8bd-253">Ceci est le premier didacticiel d’une série.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="fe8bd-254">[La série](xref:tutorials/razor-pages/index) enseigne les bases de la création d’une Razor application Web ASP.net Core pages.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="fe8bd-255">Pour obtenir une présentation plus avancée destinée aux développeurs qui connaissent bien les contrôleurs et les vues, consultez [Présentation des Razor pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="fe8bd-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="fe8bd-256">À la fin de la série, vous disposez d’une application qui gère une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="fe8bd-257">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fe8bd-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="fe8bd-258">Dans ce tutoriel, vous avez :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fe8bd-259">CreateRazorapplication Web pages.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-259">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="fe8bd-260">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-260">Run the app.</span></span>
> * <span data-ttu-id="fe8bd-261">Examiner les fichiers projet.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-261">Examine the project files.</span></span>

<span data-ttu-id="fe8bd-262">À la fin de ce didacticiel, vous disposerez d’une Razor application Web pages de travail sur laquelle vous allez vous appuyer dans des didacticiels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-262">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="fe8bd-264">Prérequis</span><span class="sxs-lookup"><span data-stu-id="fe8bd-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe8bd-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe8bd-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe8bd-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe8bd-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe8bd-267">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="fe8bd-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="fe8bd-268">Create une Razor application Web pages</span><span class="sxs-lookup"><span data-stu-id="fe8bd-268">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe8bd-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe8bd-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fe8bd-270">Dans Visual Studio, dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="fe8bd-271">Create une nouvelle ASP.NET Core application Web, puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-271">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="fe8bd-273">Nommez le projet **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-273">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="fe8bd-274">Il est important de nommer le projet *Razor PagesMovie* afin que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-274">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="fe8bd-276">Sélectionnez **ASP.NET Core 2,2** dans la liste déroulante, **application Web** , puis sélectionnez **Create** .</span><span class="sxs-lookup"><span data-stu-id="fe8bd-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application** , and then select **Create**.</span></span>

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="fe8bd-278">Le projet de démarrage suivant est créé :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-278">The following starter project is created:</span></span>

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe8bd-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe8bd-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fe8bd-281">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="fe8bd-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="fe8bd-282">Accédez au répertoire (`cd`) qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="fe8bd-283">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="fe8bd-284">La `dotnet new` commande crée un Razor projet de pages dans le dossier *Razor PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="fe8bd-284">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="fe8bd-285">La `code` commande ouvre le dossier *Razor PagesMovie* dans l’instance actuelle de Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-285">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="fe8bd-286">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « Razor PagesMovie ». Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="fe8bd-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="fe8bd-287">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-287">Select **Yes**.</span></span>

  <span data-ttu-id="fe8bd-288">Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe8bd-289">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="fe8bd-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fe8bd-290">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-290">Select **File** > **New Solution**.</span></span>

![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="fe8bd-292">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **Web Application**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="fe8bd-293">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application Web application** console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="fe8bd-294">Dans la boîte de dialogue **configurer la nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="fe8bd-295">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="fe8bd-296">Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de 2. x.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-296">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="fe8bd-297">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-297">Select **Next**.</span></span>

* <span data-ttu-id="fe8bd-298">Nommez le projet **Razor PagesMovie** , puis sélectionnez **Create** .</span><span class="sxs-lookup"><span data-stu-id="fe8bd-298">Name the project **RazorPagesMovie** , and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="fe8bd-300">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="fe8bd-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe8bd-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe8bd-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fe8bd-302">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="fe8bd-303">Le lancement de l’application avec <kbd>CTRL + F5</kbd> (mode non débogage) vous permet d’apporter des modifications au code, d’enregistrer le fichier, d’actualiser le navigateur et de voir les modifications du code.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="fe8bd-304">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>


  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="fe8bd-305">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="fe8bd-306">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fe8bd-307">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="fe8bd-308">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="fe8bd-309">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="fe8bd-310">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="fe8bd-311">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="fe8bd-313">L’illustration suivante montre l’application après avoir donné son consentement au suivi :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="fe8bd-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe8bd-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="fe8bd-316">Appuyez sur <kbd>CTRL + F5</kbd> pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="fe8bd-317">Le lancement de l’application avec <kbd>CTRL + F5</kbd> (mode non débogage) vous permet d’apporter des modifications au code, d’enregistrer le fichier, d’actualiser le navigateur et de voir les modifications du code.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="fe8bd-318">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="fe8bd-319">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur et accède à `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="fe8bd-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="fe8bd-320">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fe8bd-321">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="fe8bd-322">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="fe8bd-323">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="fe8bd-324">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="fe8bd-326">L’illustration suivante montre l’application après avoir consenti au suivi :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-326">The following image shows the app after you give consent to tracking:</span></span>

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe8bd-328">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="fe8bd-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="fe8bd-329">Appuyez sur **Cmd+Opt+F5** pour lancer l’exécution sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="fe8bd-330">Le lancement de l’application avec <kbd>cmd + opt + F5</kbd> (mode non débogage) vous permet d’apporter des modifications au code, d’enregistrer le fichier, d’actualiser le navigateur et de voir les modifications du code.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="fe8bd-331">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="fe8bd-332">Visual Studio démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur et accède à `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="fe8bd-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="fe8bd-333">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="fe8bd-334">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="fe8bd-336">L’illustration suivante montre l’application après avoir donné son consentement au suivi :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="fe8bd-338">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="fe8bd-338">Examine the project files</span></span>

<span data-ttu-id="fe8bd-339">Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="fe8bd-340">Dossier Pages</span><span class="sxs-lookup"><span data-stu-id="fe8bd-340">Pages folder</span></span>

<span data-ttu-id="fe8bd-341">Contient Razor des pages et des fichiers de prise en charge.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-341">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="fe8bd-342">Chaque Razor page est une paire de fichiers :</span><span class="sxs-lookup"><span data-stu-id="fe8bd-342">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="fe8bd-343">Un fichier *. cshtml* contenant du balisage HTML avec du code C# à l’aide de la Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-343">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="fe8bd-344">Un fichier *. cshtml.cs* qui contient le code C# qui gère les événements de page.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="fe8bd-345">Les fichiers de prise en charge ont des noms commençant par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="fe8bd-346">Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="fe8bd-347">Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="fe8bd-348">Pour plus d'informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="fe8bd-349">Razor Les pages sont dérivées de `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="fe8bd-349">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="fe8bd-350">Par Convention, la `PageModel` classe dérivée de est nommée `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="fe8bd-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="fe8bd-351">Dossier racine</span><span class="sxs-lookup"><span data-stu-id="fe8bd-351">wwwroot folder</span></span>

<span data-ttu-id="fe8bd-352">Contient des fichiers statiques, tels que des fichiers HTML, des fichiers JavaScript et des fichiers CSS.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="fe8bd-353">Pour plus d'informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="fe8bd-354">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="fe8bd-354">appSettings.json</span></span>

<span data-ttu-id="fe8bd-355">Contient des données de configuration, telles que des chaînes de connexion.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="fe8bd-356">Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="fe8bd-357">Program.cs</span><span class="sxs-lookup"><span data-stu-id="fe8bd-357">Program.cs</span></span>

<span data-ttu-id="fe8bd-358">Contient le point d’entrée pour le programme.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-358">Contains the entry point for the program.</span></span> <span data-ttu-id="fe8bd-359">Pour plus d'informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="fe8bd-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="fe8bd-360">Startup.cs</span></span>

<span data-ttu-id="fe8bd-361">Contient le code qui configure le comportement de l’application, par exemple s’il requiert le consentement pour les cookie s.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-361">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="fe8bd-362">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="fe8bd-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe8bd-363">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fe8bd-363">Additional resources</span></span>

* [<span data-ttu-id="fe8bd-364">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="fe8bd-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="fe8bd-365">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="fe8bd-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="fe8bd-366">Suivant : ajouter un modèle</span><span class="sxs-lookup"><span data-stu-id="fe8bd-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
