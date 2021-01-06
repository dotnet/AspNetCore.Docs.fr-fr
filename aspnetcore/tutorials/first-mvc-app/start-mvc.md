---
title: Bien démarrer avec ASP.NET Core MVC
author: rick-anderson
description: Découvrez comment bien démarrer avec ASP.NET Core MVC.
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c96e7107c85bf36f55f6571c71c20d09bc94ddb3
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94688499"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="57c60-103">Bien démarrer avec ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="57c60-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="57c60-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="57c60-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="57c60-105">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="57c60-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="57c60-106">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="57c60-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="57c60-107">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="57c60-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="57c60-108">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="57c60-108">Create a web app.</span></span>
> * <span data-ttu-id="57c60-109">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="57c60-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="57c60-110">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="57c60-110">Work with a database.</span></span>
> * <span data-ttu-id="57c60-111">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="57c60-111">Add search and validation.</span></span>

<span data-ttu-id="57c60-112">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="57c60-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="57c60-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="57c60-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c60-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c60-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c60-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c60-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c60-116">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="57c60-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="57c60-117">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="57c60-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c60-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c60-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="57c60-119">Démarrez Visual Studio et sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="57c60-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="57c60-120">Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **ASP.net Core application Web** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="57c60-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="57c60-121">Dans la boîte de dialogue **configurer votre nouveau projet** , entrez `MvcMovie` pour **nom du projet**.</span><span class="sxs-lookup"><span data-stu-id="57c60-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="57c60-122">Il est important d’utiliser ce nom exact, y compris la mise en majuscules, donc chaque correspond à la `namespace` copie du code.</span><span class="sxs-lookup"><span data-stu-id="57c60-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="57c60-123">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="57c60-123">Select **Create**.</span></span>
1. <span data-ttu-id="57c60-124">Dans la boîte de dialogue **créer une application web ASP.net Core** , sélectionnez :</span><span class="sxs-lookup"><span data-stu-id="57c60-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="57c60-125">**.Net Core** et **ASP.net Core 5,0** dans les listes déroulantes.</span><span class="sxs-lookup"><span data-stu-id="57c60-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="57c60-126">**ASP.net Core application Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="57c60-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="57c60-127">**Créer**</span><span class="sxs-lookup"><span data-stu-id="57c60-127">**Create**</span></span>

![<span data-ttu-id="57c60-128">Créer une application Web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57c60-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="57c60-129">Pour obtenir d’autres approches de création du projet, consultez [créer un projet dans Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="57c60-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="57c60-130">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="57c60-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="57c60-131">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="57c60-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="57c60-132">Il s’agit d’un projet de démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="57c60-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c60-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c60-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57c60-134">Ce didacticiel part du principe que vous êtes familiarisé avec VS Code.</span><span class="sxs-lookup"><span data-stu-id="57c60-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="57c60-135">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="57c60-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="57c60-136">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="57c60-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="57c60-137">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="57c60-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="57c60-138">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="57c60-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="57c60-139">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="57c60-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="57c60-140">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="57c60-140">Select **Yes**</span></span>

  * <span data-ttu-id="57c60-141">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="57c60-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="57c60-142">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="57c60-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c60-143">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="57c60-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="57c60-144">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="57c60-144">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="57c60-146">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >    >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="57c60-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="57c60-147">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >    >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="57c60-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![sélection du modèle d’application Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="57c60-149">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="57c60-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="57c60-150">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="57c60-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="57c60-151">Si vous avez présenté une option permettant de sélectionner un **Framework cible**, sélectionnez la version la plus récente de 5. x.</span><span class="sxs-lookup"><span data-stu-id="57c60-151">If presented an option to select a **Target Framework**, select the latest 5.x version.</span></span>

  <span data-ttu-id="57c60-152">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="57c60-152">Select **Next**.</span></span>

* <span data-ttu-id="57c60-153">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="57c60-153">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nom du projet](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="57c60-155">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="57c60-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c60-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c60-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="57c60-157">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="57c60-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="57c60-158">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="57c60-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="57c60-159">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="57c60-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57c60-160">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="57c60-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="57c60-161">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="57c60-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="57c60-162">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="57c60-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="57c60-163">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="57c60-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="57c60-164">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="57c60-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="57c60-166">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="57c60-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="57c60-168">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="57c60-168">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c60-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c60-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57c60-171">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="57c60-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="57c60-172">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="57c60-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="57c60-173">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="57c60-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="57c60-174">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="57c60-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="57c60-175">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="57c60-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="57c60-176">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="57c60-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="57c60-177">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="57c60-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c60-179">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="57c60-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="57c60-180">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="57c60-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="57c60-181">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="57c60-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="57c60-182">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="57c60-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57c60-183">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="57c60-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="57c60-184">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="57c60-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="57c60-185">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="57c60-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="57c60-186">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="57c60-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="57c60-187">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="57c60-187">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="57c60-189">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="57c60-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="57c60-190">Next</span><span class="sxs-lookup"><span data-stu-id="57c60-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="57c60-191">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="57c60-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="57c60-192">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="57c60-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="57c60-193">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="57c60-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="57c60-194">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="57c60-194">Create a web app.</span></span>
> * <span data-ttu-id="57c60-195">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="57c60-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="57c60-196">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="57c60-196">Work with a database.</span></span>
> * <span data-ttu-id="57c60-197">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="57c60-197">Add search and validation.</span></span>

<span data-ttu-id="57c60-198">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="57c60-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="57c60-199">Prérequis</span><span class="sxs-lookup"><span data-stu-id="57c60-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c60-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c60-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c60-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c60-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c60-202">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="57c60-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="57c60-203">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="57c60-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c60-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c60-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57c60-205">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="57c60-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="57c60-206">Sélectionnez **ASP.net Core application Web** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="57c60-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="57c60-208">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="57c60-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="57c60-209">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="57c60-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="57c60-211">Sélectionnez **application Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="57c60-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="57c60-212">Dans les zones de liste déroulante, sélectionnez **.net Core** et **ASP.net Core 3,1**, puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="57c60-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="57c60-213">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57c60-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="57c60-214">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="57c60-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="57c60-215">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="57c60-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="57c60-216">Il s’agit d’un projet de démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="57c60-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c60-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c60-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57c60-218">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="57c60-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="57c60-219">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="57c60-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="57c60-220">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="57c60-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="57c60-221">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="57c60-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="57c60-222">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="57c60-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="57c60-223">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="57c60-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="57c60-224">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="57c60-224">Select **Yes**</span></span>

  * <span data-ttu-id="57c60-225">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="57c60-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="57c60-226">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="57c60-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c60-227">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="57c60-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="57c60-228">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="57c60-228">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="57c60-230">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >    >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="57c60-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="57c60-231">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >    >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="57c60-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![sélection du modèle d’application Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="57c60-233">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="57c60-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="57c60-234">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="57c60-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="57c60-235">Si vous avez présenté une option permettant de sélectionner un **Framework cible**, sélectionnez la version la plus récente de 3. x.</span><span class="sxs-lookup"><span data-stu-id="57c60-235">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="57c60-236">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="57c60-236">Select **Next**.</span></span>

* <span data-ttu-id="57c60-237">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="57c60-237">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nom du projet](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="57c60-239">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="57c60-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c60-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c60-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="57c60-241">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="57c60-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="57c60-242">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="57c60-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="57c60-243">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="57c60-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57c60-244">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="57c60-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="57c60-245">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="57c60-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="57c60-246">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="57c60-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="57c60-247">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="57c60-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="57c60-248">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="57c60-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="57c60-250">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="57c60-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="57c60-252">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="57c60-252">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c60-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c60-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57c60-255">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="57c60-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="57c60-256">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="57c60-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="57c60-257">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="57c60-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="57c60-258">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="57c60-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="57c60-259">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="57c60-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="57c60-260">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="57c60-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="57c60-261">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="57c60-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c60-263">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="57c60-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="57c60-264">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="57c60-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="57c60-265">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="57c60-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="57c60-266">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="57c60-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57c60-267">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="57c60-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="57c60-268">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="57c60-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="57c60-269">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="57c60-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="57c60-270">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="57c60-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="57c60-271">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="57c60-271">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="57c60-273">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="57c60-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="57c60-274">Next</span><span class="sxs-lookup"><span data-stu-id="57c60-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="57c60-275">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="57c60-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="57c60-276">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="57c60-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="57c60-277">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="57c60-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="57c60-278">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="57c60-278">Create a web app.</span></span>
> * <span data-ttu-id="57c60-279">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="57c60-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="57c60-280">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="57c60-280">Work with a database.</span></span>
> * <span data-ttu-id="57c60-281">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="57c60-281">Add search and validation.</span></span>

<span data-ttu-id="57c60-282">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="57c60-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="57c60-283">Prérequis</span><span class="sxs-lookup"><span data-stu-id="57c60-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c60-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c60-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c60-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c60-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c60-286">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="57c60-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="57c60-287">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="57c60-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c60-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c60-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57c60-289">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="57c60-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="57c60-290">Sélectionnez **Application web ASP.NET Core**, puis **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="57c60-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="57c60-292">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="57c60-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="57c60-293">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="57c60-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="57c60-295">Sélectionnez **Application web (modèle-vue-contrôleur)**, puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="57c60-295">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="57c60-296">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57c60-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="57c60-297">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="57c60-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="57c60-298">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="57c60-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="57c60-299">Il s’agit d’un projet de démarrage de base qui constitue un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="57c60-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c60-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c60-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57c60-301">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="57c60-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="57c60-302">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="57c60-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="57c60-303">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="57c60-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="57c60-304">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="57c60-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="57c60-305">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="57c60-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="57c60-306">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="57c60-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="57c60-307">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="57c60-307">Select **Yes**</span></span>

  * <span data-ttu-id="57c60-308">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="57c60-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="57c60-309">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="57c60-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c60-310">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="57c60-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="57c60-311">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="57c60-311">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="57c60-313">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >    >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="57c60-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="57c60-314">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >    >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="57c60-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="57c60-315">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="57c60-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="57c60-316">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="57c60-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="57c60-317">Si vous avez présenté une option permettant de sélectionner un **Framework cible**, sélectionnez la version la plus récente de 2. x.</span><span class="sxs-lookup"><span data-stu-id="57c60-317">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="57c60-318">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="57c60-318">Select **Next**.</span></span>

* <span data-ttu-id="57c60-319">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="57c60-319">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="57c60-320">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="57c60-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c60-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c60-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="57c60-322">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="57c60-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="57c60-323">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="57c60-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="57c60-324">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="57c60-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57c60-325">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="57c60-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="57c60-326">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="57c60-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="57c60-327">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="57c60-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="57c60-328">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="57c60-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="57c60-329">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="57c60-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="57c60-331">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="57c60-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="57c60-333">Sélectionnez **Accepter** pour consentir au suivi.</span><span class="sxs-lookup"><span data-stu-id="57c60-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="57c60-334">Cette application ne suit pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="57c60-334">This app doesn't track personal information.</span></span> <span data-ttu-id="57c60-335">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="57c60-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="57c60-337">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="57c60-337">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c60-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c60-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57c60-340">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="57c60-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="57c60-341">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="57c60-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="57c60-342">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="57c60-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="57c60-343">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="57c60-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="57c60-344">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="57c60-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="57c60-345">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="57c60-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="57c60-346">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="57c60-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="57c60-347">Sélectionnez **Accepter** pour consentir au suivi.</span><span class="sxs-lookup"><span data-stu-id="57c60-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="57c60-348">Cette application ne suit pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="57c60-348">This app doesn't track personal information.</span></span> <span data-ttu-id="57c60-349">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="57c60-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="57c60-351">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="57c60-351">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c60-353">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="57c60-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="57c60-354">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="57c60-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="57c60-355">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="57c60-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="57c60-356">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="57c60-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57c60-357">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="57c60-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="57c60-358">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="57c60-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="57c60-359">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="57c60-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="57c60-360">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="57c60-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="57c60-361">Sélectionnez **Accepter** pour consentir au suivi.</span><span class="sxs-lookup"><span data-stu-id="57c60-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="57c60-362">Cette application ne suit pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="57c60-362">This app doesn't track personal information.</span></span> <span data-ttu-id="57c60-363">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="57c60-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="57c60-365">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="57c60-365">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="57c60-367">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="57c60-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="57c60-368">Next</span><span class="sxs-lookup"><span data-stu-id="57c60-368">Next</span></span>](adding-controller.md)

::: moniker-end
