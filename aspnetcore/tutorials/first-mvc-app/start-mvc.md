---
title: Bien démarrer avec ASP.NET Core MVC
author: rick-anderson
description: Découvrez comment bien démarrer avec ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
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
ms.openlocfilehash: 177112106d143a6826c1f927aac807da0aa9f2b4
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113827"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="a5504-103">Bien démarrer avec ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="a5504-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="a5504-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a5504-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="a5504-105">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="a5504-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="a5504-106">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="a5504-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="a5504-107">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="a5504-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a5504-108">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="a5504-108">Create a web app.</span></span>
> * <span data-ttu-id="a5504-109">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="a5504-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="a5504-110">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="a5504-110">Work with a database.</span></span>
> * <span data-ttu-id="a5504-111">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="a5504-111">Add search and validation.</span></span>

<span data-ttu-id="a5504-112">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="a5504-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="a5504-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="a5504-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a5504-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5504-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a5504-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5504-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a5504-116">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a5504-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="a5504-117">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="a5504-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a5504-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5504-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a5504-119">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="a5504-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="a5504-120">Sélectionnez **ASP.net Core application Web** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="a5504-120">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="a5504-122">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="a5504-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="a5504-123">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="a5504-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="a5504-125">Sélectionnez **application Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="a5504-125">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="a5504-126">Dans les zones de liste déroulante, sélectionnez **.net Core** et **ASP.net Core 3,1**, puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="a5504-126">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="a5504-127">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5504-127">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="a5504-128">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="a5504-128">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="a5504-129">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="a5504-129">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="a5504-130">Il s’agit d’un projet de démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="a5504-130">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a5504-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5504-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a5504-132">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="a5504-132">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="a5504-133">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="a5504-133">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="a5504-134">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a5504-134">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="a5504-135">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="a5504-135">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="a5504-136">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a5504-136">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="a5504-137">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="a5504-137">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="a5504-138">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="a5504-138">Select **Yes**</span></span>

  * <span data-ttu-id="a5504-139">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="a5504-139">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="a5504-140">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="a5504-140">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a5504-141">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a5504-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a5504-142">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="a5504-142">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="a5504-144">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="a5504-144">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="a5504-145">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="a5504-145">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![sélection du modèle d’application Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="a5504-147">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="a5504-147">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="a5504-148">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="a5504-148">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="a5504-149">Si vous avez présenté une option permettant de sélectionner un **Framework cible**, sélectionnez la version la plus récente de 3. x.</span><span class="sxs-lookup"><span data-stu-id="a5504-149">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="a5504-150">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="a5504-150">Select **Next**.</span></span>

* <span data-ttu-id="a5504-151">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="a5504-151">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nom du projet](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="a5504-153">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="a5504-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a5504-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5504-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a5504-155">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="a5504-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="a5504-156">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="a5504-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="a5504-157">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="a5504-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a5504-158">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="a5504-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="a5504-159">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="a5504-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="a5504-160">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="a5504-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="a5504-161">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="a5504-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="a5504-162">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="a5504-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="a5504-164">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="a5504-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="a5504-166">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="a5504-166">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a5504-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5504-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a5504-169">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="a5504-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="a5504-170">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="a5504-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="a5504-171">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="a5504-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="a5504-172">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="a5504-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="a5504-173">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="a5504-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="a5504-174">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="a5504-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="a5504-175">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="a5504-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a5504-177">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a5504-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a5504-178">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="a5504-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="a5504-179">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="a5504-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="a5504-180">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="a5504-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a5504-181">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="a5504-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="a5504-182">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="a5504-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="a5504-183">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="a5504-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="a5504-184">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="a5504-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="a5504-185">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="a5504-185">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="a5504-187">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="a5504-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a5504-188">Next</span><span class="sxs-lookup"><span data-stu-id="a5504-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="a5504-189">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="a5504-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="a5504-190">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="a5504-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="a5504-191">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="a5504-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a5504-192">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="a5504-192">Create a web app.</span></span>
> * <span data-ttu-id="a5504-193">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="a5504-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="a5504-194">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="a5504-194">Work with a database.</span></span>
> * <span data-ttu-id="a5504-195">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="a5504-195">Add search and validation.</span></span>

<span data-ttu-id="a5504-196">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="a5504-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="a5504-197">Prérequis</span><span class="sxs-lookup"><span data-stu-id="a5504-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a5504-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5504-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a5504-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5504-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a5504-200">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a5504-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="a5504-201">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="a5504-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a5504-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5504-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a5504-203">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="a5504-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="a5504-204">Sélectionnez **Application web ASP.NET Core**, puis **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="a5504-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="a5504-206">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="a5504-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="a5504-207">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="a5504-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="a5504-209">Sélectionnez **Application web (modèle-vue-contrôleur)**, puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="a5504-209">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="a5504-210">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5504-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="a5504-211">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="a5504-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="a5504-212">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="a5504-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="a5504-213">Il s’agit d’un projet de démarrage de base qui constitue un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="a5504-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a5504-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5504-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a5504-215">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="a5504-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="a5504-216">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="a5504-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="a5504-217">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a5504-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="a5504-218">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="a5504-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="a5504-219">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a5504-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="a5504-220">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="a5504-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="a5504-221">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="a5504-221">Select **Yes**</span></span>

  * <span data-ttu-id="a5504-222">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="a5504-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="a5504-223">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="a5504-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a5504-224">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a5504-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a5504-225">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="a5504-225">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="a5504-227">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="a5504-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="a5504-228">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="a5504-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="a5504-229">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="a5504-229">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="a5504-230">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="a5504-230">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="a5504-231">Si vous avez présenté une option permettant de sélectionner un **Framework cible**, sélectionnez la version la plus récente de 2. x.</span><span class="sxs-lookup"><span data-stu-id="a5504-231">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="a5504-232">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="a5504-232">Select **Next**.</span></span>

* <span data-ttu-id="a5504-233">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="a5504-233">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="a5504-234">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="a5504-234">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a5504-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5504-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a5504-236">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="a5504-236">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="a5504-237">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="a5504-237">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="a5504-238">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="a5504-238">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a5504-239">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="a5504-239">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="a5504-240">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="a5504-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="a5504-241">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="a5504-241">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="a5504-242">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="a5504-242">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="a5504-243">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="a5504-243">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="a5504-245">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="a5504-245">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="a5504-247">Sélectionnez **Accepter** pour consentir au suivi.</span><span class="sxs-lookup"><span data-stu-id="a5504-247">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="a5504-248">Cette application ne suit pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="a5504-248">This app doesn't track personal information.</span></span> <span data-ttu-id="a5504-249">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="a5504-249">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="a5504-251">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="a5504-251">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a5504-253">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5504-253">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a5504-254">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="a5504-254">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="a5504-255">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="a5504-255">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="a5504-256">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="a5504-256">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="a5504-257">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="a5504-257">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="a5504-258">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="a5504-258">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="a5504-259">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="a5504-259">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="a5504-260">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="a5504-260">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="a5504-261">Sélectionnez **Accepter** pour consentir au suivi.</span><span class="sxs-lookup"><span data-stu-id="a5504-261">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="a5504-262">Cette application ne suit pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="a5504-262">This app doesn't track personal information.</span></span> <span data-ttu-id="a5504-263">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="a5504-263">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="a5504-265">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="a5504-265">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a5504-267">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a5504-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a5504-268">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="a5504-268">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="a5504-269">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="a5504-269">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="a5504-270">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="a5504-270">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a5504-271">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="a5504-271">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="a5504-272">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="a5504-272">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="a5504-273">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="a5504-273">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="a5504-274">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="a5504-274">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="a5504-275">Sélectionnez **Accepter** pour consentir au suivi.</span><span class="sxs-lookup"><span data-stu-id="a5504-275">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="a5504-276">Cette application ne suit pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="a5504-276">This app doesn't track personal information.</span></span> <span data-ttu-id="a5504-277">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="a5504-277">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="a5504-279">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="a5504-279">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="a5504-281">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="a5504-281">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a5504-282">Next</span><span class="sxs-lookup"><span data-stu-id="a5504-282">Next</span></span>](adding-controller.md)

::: moniker-end
