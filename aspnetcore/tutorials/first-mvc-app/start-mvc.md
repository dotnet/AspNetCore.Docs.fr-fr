---
title: Bien démarrer avec ASP.NET Core MVC
author: rick-anderson
description: Découvrez comment bien démarrer avec ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 1c703cdbd168c2e83d09c40f7740689df8938dad
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422784"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="29dcb-103">Bien démarrer avec ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="29dcb-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="29dcb-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="29dcb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="29dcb-105">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="29dcb-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="29dcb-106">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="29dcb-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="29dcb-107">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="29dcb-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="29dcb-108">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="29dcb-108">Create a web app.</span></span>
> * <span data-ttu-id="29dcb-109">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="29dcb-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="29dcb-110">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="29dcb-110">Work with a database.</span></span>
> * <span data-ttu-id="29dcb-111">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="29dcb-111">Add search and validation.</span></span>

<span data-ttu-id="29dcb-112">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="29dcb-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="29dcb-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="29dcb-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29dcb-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29dcb-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="29dcb-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29dcb-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="29dcb-116">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="29dcb-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="29dcb-117">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="29dcb-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29dcb-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29dcb-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="29dcb-119">Démarrez Visual Studio et sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="29dcb-120">Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **ASP.net Core application Web** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="29dcb-121">Dans la boîte de dialogue **configurer votre nouveau projet** , entrez `MvcMovie` pour **nom du projet**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="29dcb-122">Il est important d’utiliser ce nom exact, y compris la mise en majuscules, donc chaque correspond à la `namespace` copie du code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="29dcb-123">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="29dcb-123">Select **Create**.</span></span>
1. <span data-ttu-id="29dcb-124">Dans la boîte de dialogue **créer une application web ASP.net Core** , sélectionnez :</span><span class="sxs-lookup"><span data-stu-id="29dcb-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="29dcb-125">**.Net Core** et **ASP.net Core 5,0** dans les listes déroulantes.</span><span class="sxs-lookup"><span data-stu-id="29dcb-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="29dcb-126">**ASP.net Core application Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="29dcb-127">**Créer**</span><span class="sxs-lookup"><span data-stu-id="29dcb-127">**Create**</span></span>

![<span data-ttu-id="29dcb-128">Créer une application Web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29dcb-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/5/mvc.png)

<span data-ttu-id="29dcb-129">Pour obtenir d’autres approches de création du projet, consultez [créer un projet dans Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="29dcb-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="29dcb-130">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="29dcb-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="29dcb-131">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="29dcb-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="29dcb-132">Il s’agit d’un projet de démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="29dcb-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="29dcb-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29dcb-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29dcb-134">Ce didacticiel part du principe que vous êtes familiarisé avec VS Code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="29dcb-135">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="29dcb-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="29dcb-136">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="29dcb-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="29dcb-137">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="29dcb-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="29dcb-138">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="29dcb-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="29dcb-139">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="29dcb-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="29dcb-140">Sélectionnez **Oui**</span><span class="sxs-lookup"><span data-stu-id="29dcb-140">Select **Yes**</span></span>

  * <span data-ttu-id="29dcb-141">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="29dcb-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="29dcb-142">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="29dcb-143">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="29dcb-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="29dcb-144">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-144">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="29dcb-146">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="29dcb-147">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![sélection du modèle d’application Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="29dcb-149">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="29dcb-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="29dcb-150">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="29dcb-151">Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de 5. x.</span><span class="sxs-lookup"><span data-stu-id="29dcb-151">If presented an option to select a **Target Framework** , select the latest 5.x version.</span></span>

  <span data-ttu-id="29dcb-152">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-152">Select **Next**.</span></span>

* <span data-ttu-id="29dcb-153">Nommez le projet **MvcMovie** , puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-153">Name the project **MvcMovie** , and then select **Create**.</span></span>

  ![macOS nom du projet](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="29dcb-155">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="29dcb-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29dcb-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29dcb-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="29dcb-157">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="29dcb-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="29dcb-158">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="29dcb-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="29dcb-159">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="29dcb-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="29dcb-160">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="29dcb-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="29dcb-161">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="29dcb-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="29dcb-162">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="29dcb-163">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="29dcb-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="29dcb-164">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="29dcb-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="29dcb-166">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="29dcb-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="29dcb-168">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="29dcb-168">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="29dcb-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29dcb-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29dcb-171">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="29dcb-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="29dcb-172">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="29dcb-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="29dcb-173">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="29dcb-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="29dcb-174">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="29dcb-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="29dcb-175">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="29dcb-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="29dcb-176">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="29dcb-177">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="29dcb-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="29dcb-179">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="29dcb-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="29dcb-180">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="29dcb-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="29dcb-181">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="29dcb-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="29dcb-182">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="29dcb-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="29dcb-183">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="29dcb-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="29dcb-184">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="29dcb-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="29dcb-185">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="29dcb-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="29dcb-186">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="29dcb-187">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="29dcb-187">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="29dcb-189">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="29dcb-190">Next</span><span class="sxs-lookup"><span data-stu-id="29dcb-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="29dcb-191">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="29dcb-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="29dcb-192">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="29dcb-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="29dcb-193">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="29dcb-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="29dcb-194">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="29dcb-194">Create a web app.</span></span>
> * <span data-ttu-id="29dcb-195">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="29dcb-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="29dcb-196">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="29dcb-196">Work with a database.</span></span>
> * <span data-ttu-id="29dcb-197">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="29dcb-197">Add search and validation.</span></span>

<span data-ttu-id="29dcb-198">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="29dcb-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="29dcb-199">Prérequis</span><span class="sxs-lookup"><span data-stu-id="29dcb-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29dcb-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29dcb-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="29dcb-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29dcb-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="29dcb-202">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="29dcb-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="29dcb-203">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="29dcb-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29dcb-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29dcb-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29dcb-205">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="29dcb-206">Sélectionnez **ASP.net Core application Web** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="29dcb-208">Nommez le projet **MvcMovie** , puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="29dcb-209">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="29dcb-211">Sélectionnez **application Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="29dcb-212">Dans les zones de liste déroulante, sélectionnez **.net Core** et **ASP.net Core 3,1** , puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1** , then select **Create**.</span></span>

![<span data-ttu-id="29dcb-213">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29dcb-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="29dcb-214">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="29dcb-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="29dcb-215">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="29dcb-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="29dcb-216">Il s’agit d’un projet de démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="29dcb-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="29dcb-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29dcb-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29dcb-218">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="29dcb-219">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="29dcb-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="29dcb-220">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="29dcb-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="29dcb-221">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="29dcb-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="29dcb-222">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="29dcb-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="29dcb-223">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="29dcb-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="29dcb-224">Sélectionnez **Oui**</span><span class="sxs-lookup"><span data-stu-id="29dcb-224">Select **Yes**</span></span>

  * <span data-ttu-id="29dcb-225">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="29dcb-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="29dcb-226">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="29dcb-227">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="29dcb-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="29dcb-228">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-228">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="29dcb-230">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="29dcb-231">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![sélection du modèle d’application Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="29dcb-233">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="29dcb-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="29dcb-234">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="29dcb-235">Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de 3. x.</span><span class="sxs-lookup"><span data-stu-id="29dcb-235">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="29dcb-236">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-236">Select **Next**.</span></span>

* <span data-ttu-id="29dcb-237">Nommez le projet **MvcMovie** , puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-237">Name the project **MvcMovie** , and then select **Create**.</span></span>

  ![macOS nom du projet](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="29dcb-239">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="29dcb-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29dcb-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29dcb-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="29dcb-241">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="29dcb-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="29dcb-242">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="29dcb-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="29dcb-243">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="29dcb-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="29dcb-244">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="29dcb-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="29dcb-245">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="29dcb-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="29dcb-246">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="29dcb-247">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="29dcb-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="29dcb-248">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="29dcb-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="29dcb-250">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="29dcb-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="29dcb-252">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="29dcb-252">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="29dcb-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29dcb-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29dcb-255">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="29dcb-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="29dcb-256">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="29dcb-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="29dcb-257">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="29dcb-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="29dcb-258">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="29dcb-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="29dcb-259">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="29dcb-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="29dcb-260">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="29dcb-261">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="29dcb-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="29dcb-263">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="29dcb-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="29dcb-264">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="29dcb-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="29dcb-265">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="29dcb-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="29dcb-266">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="29dcb-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="29dcb-267">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="29dcb-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="29dcb-268">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="29dcb-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="29dcb-269">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="29dcb-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="29dcb-270">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="29dcb-271">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="29dcb-271">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="29dcb-273">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="29dcb-274">Next</span><span class="sxs-lookup"><span data-stu-id="29dcb-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="29dcb-275">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="29dcb-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="29dcb-276">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="29dcb-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="29dcb-277">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="29dcb-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="29dcb-278">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="29dcb-278">Create a web app.</span></span>
> * <span data-ttu-id="29dcb-279">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="29dcb-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="29dcb-280">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="29dcb-280">Work with a database.</span></span>
> * <span data-ttu-id="29dcb-281">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="29dcb-281">Add search and validation.</span></span>

<span data-ttu-id="29dcb-282">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="29dcb-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="29dcb-283">Prérequis</span><span class="sxs-lookup"><span data-stu-id="29dcb-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29dcb-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29dcb-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="29dcb-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29dcb-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="29dcb-286">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="29dcb-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="29dcb-287">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="29dcb-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29dcb-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29dcb-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29dcb-289">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="29dcb-290">Sélectionnez **Application web ASP.NET Core** , puis **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="29dcb-292">Nommez le projet **MvcMovie** , puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="29dcb-293">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="29dcb-295">Sélectionnez **Application web (modèle-vue-contrôleur)** , puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-295">Select **Web Application(Model-View-Controller)** , and then select **Create**.</span></span>

![<span data-ttu-id="29dcb-296">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29dcb-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="29dcb-297">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="29dcb-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="29dcb-298">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="29dcb-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="29dcb-299">Il s’agit d’un projet de démarrage de base qui constitue un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="29dcb-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="29dcb-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29dcb-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29dcb-301">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="29dcb-302">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="29dcb-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="29dcb-303">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="29dcb-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="29dcb-304">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="29dcb-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="29dcb-305">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="29dcb-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="29dcb-306">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="29dcb-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="29dcb-307">Sélectionnez **Oui**</span><span class="sxs-lookup"><span data-stu-id="29dcb-307">Select **Yes**</span></span>

  * <span data-ttu-id="29dcb-308">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="29dcb-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="29dcb-309">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="29dcb-310">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="29dcb-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="29dcb-311">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-311">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="29dcb-313">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="29dcb-314">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="29dcb-315">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="29dcb-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="29dcb-316">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="29dcb-317">Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de 2. x.</span><span class="sxs-lookup"><span data-stu-id="29dcb-317">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="29dcb-318">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-318">Select **Next**.</span></span>

* <span data-ttu-id="29dcb-319">Nommez le projet **MvcMovie** , puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-319">Name the project **MvcMovie** , and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="29dcb-320">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="29dcb-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29dcb-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29dcb-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="29dcb-322">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="29dcb-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="29dcb-323">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="29dcb-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="29dcb-324">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="29dcb-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="29dcb-325">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="29dcb-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="29dcb-326">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="29dcb-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="29dcb-327">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="29dcb-328">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="29dcb-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="29dcb-329">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="29dcb-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="29dcb-331">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="29dcb-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="29dcb-333">Sélectionnez **Accepter** pour consentir au suivi.</span><span class="sxs-lookup"><span data-stu-id="29dcb-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="29dcb-334">Cette application ne suit pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="29dcb-334">This app doesn't track personal information.</span></span> <span data-ttu-id="29dcb-335">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="29dcb-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="29dcb-337">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="29dcb-337">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="29dcb-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29dcb-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29dcb-340">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="29dcb-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="29dcb-341">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="29dcb-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="29dcb-342">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="29dcb-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="29dcb-343">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="29dcb-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="29dcb-344">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="29dcb-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="29dcb-345">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="29dcb-346">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="29dcb-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="29dcb-347">Sélectionnez **Accepter** pour consentir au suivi.</span><span class="sxs-lookup"><span data-stu-id="29dcb-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="29dcb-348">Cette application ne suit pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="29dcb-348">This app doesn't track personal information.</span></span> <span data-ttu-id="29dcb-349">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="29dcb-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="29dcb-351">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="29dcb-351">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="29dcb-353">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="29dcb-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="29dcb-354">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="29dcb-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="29dcb-355">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="29dcb-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="29dcb-356">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="29dcb-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="29dcb-357">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="29dcb-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="29dcb-358">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="29dcb-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="29dcb-359">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="29dcb-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="29dcb-360">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="29dcb-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="29dcb-361">Sélectionnez **Accepter** pour consentir au suivi.</span><span class="sxs-lookup"><span data-stu-id="29dcb-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="29dcb-362">Cette application ne suit pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="29dcb-362">This app doesn't track personal information.</span></span> <span data-ttu-id="29dcb-363">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="29dcb-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="29dcb-365">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="29dcb-365">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="29dcb-367">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="29dcb-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="29dcb-368">Next</span><span class="sxs-lookup"><span data-stu-id="29dcb-368">Next</span></span>](adding-controller.md)

::: moniker-end
