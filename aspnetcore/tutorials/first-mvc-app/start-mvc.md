---
title: Bien démarrer avec ASP.NET Core MVC
author: rick-anderson
description: Découvrez comment bien démarrer avec ASP.NET Core MVC.
ms.author: riande
ms.date: 01/20/2021
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
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710767"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="340af-103">Bien démarrer avec ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="340af-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="340af-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="340af-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="340af-105">Il s’agit du premier didacticiel d’une série qui enseigne ASP.NET Core développement Web MVC avec des contrôleurs et des vues.</span><span class="sxs-lookup"><span data-stu-id="340af-105">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="340af-106">À la fin de la série, vous disposerez d’une application qui gère et affiche des données de film.</span><span class="sxs-lookup"><span data-stu-id="340af-106">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="340af-107">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="340af-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="340af-108">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="340af-108">Create a web app.</span></span>
> * <span data-ttu-id="340af-109">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="340af-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="340af-110">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="340af-110">Work with a database.</span></span>
> * <span data-ttu-id="340af-111">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="340af-111">Add search and validation.</span></span>

<span data-ttu-id="340af-112">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="340af-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="340af-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="340af-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="340af-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="340af-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="340af-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="340af-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="340af-116">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="340af-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="340af-117">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="340af-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="340af-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="340af-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="340af-119">Démarrez Visual Studio et sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="340af-119">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="340af-120">Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **ASP.net Core application Web** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="340af-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
* <span data-ttu-id="340af-121">Dans la boîte de dialogue **configurer votre nouveau projet** , entrez `MvcMovie` pour **nom du projet**.</span><span class="sxs-lookup"><span data-stu-id="340af-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="340af-122">Il est important de nommer le projet *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="340af-122">It's important to name the project *MvcMovie*.</span></span> <span data-ttu-id="340af-123">La mise en majuscules doit correspondre à chaque `namespace` correspondance lorsque le code est copié.</span><span class="sxs-lookup"><span data-stu-id="340af-123">Capitalization needs to match each `namespace` matches when code is copied.</span></span>
* <span data-ttu-id="340af-124">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="340af-124">Select **Create**.</span></span>
* <span data-ttu-id="340af-125">Dans la boîte de dialogue **créer une application web ASP.net Core** , sélectionnez :</span><span class="sxs-lookup"><span data-stu-id="340af-125">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
  * <span data-ttu-id="340af-126">**.Net Core** et **ASP.net Core 5,0** dans les listes déroulantes.</span><span class="sxs-lookup"><span data-stu-id="340af-126">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
  * <span data-ttu-id="340af-127">**ASP.net Core application Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="340af-127">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
  * <span data-ttu-id="340af-128">**Créer**.</span><span class="sxs-lookup"><span data-stu-id="340af-128">**Create**.</span></span>

![<span data-ttu-id="340af-129">Créer une application Web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="340af-129">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="340af-130">Pour obtenir d’autres approches de création du projet, consultez [créer un projet dans Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="340af-130">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="340af-131">Visual Studio a utilisé le modèle de projet par défaut pour le projet MVC créé.</span><span class="sxs-lookup"><span data-stu-id="340af-131">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="340af-132">Le projet créé :</span><span class="sxs-lookup"><span data-stu-id="340af-132">The created project:</span></span>

* <span data-ttu-id="340af-133">Est une application active.</span><span class="sxs-lookup"><span data-stu-id="340af-133">Is a working app.</span></span>
* <span data-ttu-id="340af-134">Est un projet de démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="340af-134">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="340af-135">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="340af-135">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="340af-136">Ce didacticiel part du principe que vous êtes familiarisé avec VS Code.</span><span class="sxs-lookup"><span data-stu-id="340af-136">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="340af-137">Pour plus d’informations, consultez [prise en main de vs code](https://code.visualstudio.com/docs) et [Visual Studio code de l’aide](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="340af-137">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="340af-138">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="340af-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="340af-139">Accédez au répertoire ( `cd` ) qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="340af-139">Change to the directory (`cd`) that will contain the project.</span></span>
* <span data-ttu-id="340af-140">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="340af-140">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="340af-141">Si une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage, il manque la valeur « MvcMovie ». Ajoutez-les ?**, sélectionnez **Oui**</span><span class="sxs-lookup"><span data-stu-id="340af-141">If a dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**</span></span>

  * <span data-ttu-id="340af-142">`dotnet new mvc -o MvcMovie`: Crée un nouveau ASP.NET Core projet MVC dans le dossier *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="340af-142">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="340af-143">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="340af-143">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="340af-144">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="340af-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="340af-145">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="340af-145">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="340af-147">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >    >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="340af-147">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="340af-148">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >    >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="340af-148">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![sélection du modèle d’application Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="340af-150">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="340af-150">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="340af-151">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="340af-151">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="340af-152">Si une option permettant de sélectionner une version **cible du .NET Framework** est présentée, sélectionnez la version la plus récente de 5. x.</span><span class="sxs-lookup"><span data-stu-id="340af-152">If an option to select a **Target Framework** is presented, select the latest 5.x version.</span></span>
  * <span data-ttu-id="340af-153">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="340af-153">Select **Next**.</span></span>

* <span data-ttu-id="340af-154">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="340af-154">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nom du projet](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="340af-156">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="340af-156">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="340af-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="340af-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="340af-158">Appuyez sur CTRL + F5 pour exécuter l’application sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="340af-158">Select Ctrl+F5 to run the app without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="340af-159">Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="340af-159">Visual Studio:</span></span>

  * <span data-ttu-id="340af-160">Démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span><span class="sxs-lookup"><span data-stu-id="340af-160">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="340af-161">Exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="340af-161">Runs the app.</span></span>

  <span data-ttu-id="340af-162">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="340af-162">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="340af-163">Le nom d’hôte standard de votre ordinateur local est `localhost` .</span><span class="sxs-lookup"><span data-stu-id="340af-163">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="340af-164">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="340af-164">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="340af-165">Le lancement de l’application sans débogage en sélectionnant CTRL + F5 vous permet d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="340af-165">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="340af-166">Modifiez le code.</span><span class="sxs-lookup"><span data-stu-id="340af-166">Make code changes.</span></span>
* <span data-ttu-id="340af-167">Enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="340af-167">Save the file.</span></span>
* <span data-ttu-id="340af-168">Actualisez rapidement le navigateur et consultez les modifications de code.</span><span class="sxs-lookup"><span data-stu-id="340af-168">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="340af-169">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="340af-169">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menu Déboguer](start-mvc/_static/debug_menu50.png)

<span data-ttu-id="340af-171">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="340af-171">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express50.png)

<span data-ttu-id="340af-173">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="340af-173">The following image shows the app:</span></span>

![Page d’accueil ou d’index](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="340af-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="340af-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="340af-176">Appuyez sur CTRL + F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="340af-176">Select Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="340af-177">Visual Studio Code :</span><span class="sxs-lookup"><span data-stu-id="340af-177">Visual Studio Code:</span></span>

  * <span data-ttu-id="340af-178">Démarre [Kestrel](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="340af-178">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="340af-179">Lance un navigateur.</span><span class="sxs-lookup"><span data-stu-id="340af-179">Launches a browser.</span></span>
  * <span data-ttu-id="340af-180">Accède à `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="340af-180">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="340af-181">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="340af-181">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="340af-182">Le nom d’hôte standard de votre ordinateur local est `localhost` .</span><span class="sxs-lookup"><span data-stu-id="340af-182">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="340af-183">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="340af-183">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="340af-184">Le lancement de l’application sans débogage en sélectionnant CTRL + F5 vous permet d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="340af-184">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="340af-185">Modifiez le code.</span><span class="sxs-lookup"><span data-stu-id="340af-185">Make code changes.</span></span>
* <span data-ttu-id="340af-186">Enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="340af-186">Save the file.</span></span>
* <span data-ttu-id="340af-187">Actualisez rapidement le navigateur et consultez les modifications de code.</span><span class="sxs-lookup"><span data-stu-id="340af-187">Quickly refresh the browser and see the code changes.</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="340af-189">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="340af-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="340af-190">Sélectionnez **Exécuter** > **Exécuter sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="340af-190">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="340af-191">Visual Studio pour Mac:</span><span class="sxs-lookup"><span data-stu-id="340af-191">Visual Studio for Mac:</span></span>

  * <span data-ttu-id="340af-192">Démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel) .</span><span class="sxs-lookup"><span data-stu-id="340af-192">Starts [Kestrel](xref:fundamentals/servers/index#kestrel) server.</span></span>
  * <span data-ttu-id="340af-193">Lance un navigateur.</span><span class="sxs-lookup"><span data-stu-id="340af-193">Launches a browser.</span></span>
  * <span data-ttu-id="340af-194">Accède à `http://localhost:port` , où *port* est un numéro de port choisi au hasard.</span><span class="sxs-lookup"><span data-stu-id="340af-194">Navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

  [!INCLUDE[](~/includes/trustCertMac.md)]

  <span data-ttu-id="340af-195">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="340af-195">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="340af-196">Le nom d’hôte standard de votre ordinateur local est `localhost` .</span><span class="sxs-lookup"><span data-stu-id="340af-196">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="340af-197">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="340af-197">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="340af-198">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="340af-198">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="340af-199">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="340af-199">The following image shows the app:</span></span>

![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="340af-201">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="340af-201">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="340af-202">Étape suivante : ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="340af-202">Next: Add a controller</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="340af-203">Il s’agit du premier didacticiel d’une série qui enseigne ASP.NET Core développement Web MVC avec des contrôleurs et des vues.</span><span class="sxs-lookup"><span data-stu-id="340af-203">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="340af-204">À la fin de la série, vous disposerez d’une application qui gère et affiche des données de film.</span><span class="sxs-lookup"><span data-stu-id="340af-204">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="340af-205">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="340af-205">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="340af-206">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="340af-206">Create a web app.</span></span>
> * <span data-ttu-id="340af-207">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="340af-207">Add and scaffold a model.</span></span>
> * <span data-ttu-id="340af-208">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="340af-208">Work with a database.</span></span>
> * <span data-ttu-id="340af-209">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="340af-209">Add search and validation.</span></span>

<span data-ttu-id="340af-210">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="340af-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="340af-211">Prérequis</span><span class="sxs-lookup"><span data-stu-id="340af-211">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="340af-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="340af-212">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="340af-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="340af-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="340af-214">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="340af-214">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="340af-215">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="340af-215">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="340af-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="340af-216">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="340af-217">Dans Visual Studio, sélectionnez **créer un nouveau projet**.</span><span class="sxs-lookup"><span data-stu-id="340af-217">From the Visual Studio, select **Create a new project**.</span></span>

* <span data-ttu-id="340af-218">Sélectionnez **ASP.net Core application Web** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="340af-218">Select **ASP.NET Core Web Application** > **Next**.</span></span>

  ![Créer un projet d’application Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="340af-220">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="340af-220">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="340af-221">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="340af-221">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Configurer votre nouveau projet](start-mvc/_static/config.png)

* <span data-ttu-id="340af-223">Sélectionnez **application Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="340af-223">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="340af-224">Dans les zones de liste déroulante, sélectionnez **.net Core** et **ASP.net Core 3,1**, puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="340af-224">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

  ![<span data-ttu-id="340af-225">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="340af-225">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="340af-226">Visual Studio a utilisé le modèle de projet par défaut pour le projet MVC créé.</span><span class="sxs-lookup"><span data-stu-id="340af-226">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="340af-227">Le projet créé :</span><span class="sxs-lookup"><span data-stu-id="340af-227">The created project:</span></span>

* <span data-ttu-id="340af-228">Est une application active.</span><span class="sxs-lookup"><span data-stu-id="340af-228">Is a working app.</span></span>
* <span data-ttu-id="340af-229">Est un projet de démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="340af-229">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="340af-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="340af-230">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="340af-231">Ce didacticiel part du principe que vous êtes familiarisé avec VS Code.</span><span class="sxs-lookup"><span data-stu-id="340af-231">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="340af-232">Pour plus d’informations, consultez [prise en main de vs code](https://code.visualstudio.com/docs) et [Visual Studio code de l’aide](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="340af-232">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="340af-233">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="340af-233">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="340af-234">Remplacez répertoires ( `cd` ) par un dossier qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="340af-234">Change directories (`cd`) to a folder that will contain the project.</span></span>
* <span data-ttu-id="340af-235">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="340af-235">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="340af-236">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="340af-236">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**.</span></span>

  * <span data-ttu-id="340af-237">`dotnet new mvc -o MvcMovie`: Crée un nouveau ASP.NET Core projet MVC dans le dossier *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="340af-237">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="340af-238">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="340af-238">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="340af-239">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="340af-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="340af-240">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="340af-240">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="340af-242">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >    >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="340af-242">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="340af-243">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >    >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="340af-243">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![sélection du modèle d’application Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="340af-245">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="340af-245">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="340af-246">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="340af-246">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="340af-247">Si une option permettant de sélectionner un **Framework cible** s’affiche, sélectionnez la dernière version de 3. x.</span><span class="sxs-lookup"><span data-stu-id="340af-247">If an option to select a **Target Framework** is presented, select the latest 3.x version.</span></span>
  * <span data-ttu-id="340af-248">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="340af-248">Select **Next**.</span></span>

* <span data-ttu-id="340af-249">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="340af-249">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nom du projet](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="340af-251">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="340af-251">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="340af-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="340af-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="340af-253">Appuyez sur CTRL + F5 pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="340af-253">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="340af-254">Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="340af-254">Visual Studio:</span></span>

  * <span data-ttu-id="340af-255">Démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span><span class="sxs-lookup"><span data-stu-id="340af-255">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="340af-256">Exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="340af-256">Runs the app.</span></span>

  <span data-ttu-id="340af-257">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="340af-257">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="340af-258">Le nom d’hôte standard de votre ordinateur local est `localhost` .</span><span class="sxs-lookup"><span data-stu-id="340af-258">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="340af-259">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="340af-259">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="340af-260">Le lancement de l’application sans débogage en sélectionnant CTRL + F5 vous permet d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="340af-260">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="340af-261">Modifiez le code.</span><span class="sxs-lookup"><span data-stu-id="340af-261">Make code changes.</span></span>
* <span data-ttu-id="340af-262">Enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="340af-262">Save the file.</span></span>
* <span data-ttu-id="340af-263">Actualisez rapidement le navigateur et consultez les modifications de code.</span><span class="sxs-lookup"><span data-stu-id="340af-263">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="340af-264">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="340af-264">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menu Déboguer](start-mvc/_static/debug_menu.png)

<span data-ttu-id="340af-266">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="340af-266">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="340af-268">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="340af-268">The following image shows the app:</span></span>

![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="340af-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="340af-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="340af-271">Appuyez sur CTRL + F5 pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="340af-271">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="340af-272">Visual Studio Code :</span><span class="sxs-lookup"><span data-stu-id="340af-272">Visual Studio Code:</span></span>

  * <span data-ttu-id="340af-273">Démarre [Kestrel](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="340af-273">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="340af-274">Lance un navigateur.</span><span class="sxs-lookup"><span data-stu-id="340af-274">Launches a browser.</span></span>
  * <span data-ttu-id="340af-275">Accède à `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="340af-275">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="340af-276">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="340af-276">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="340af-277">Le nom d’hôte standard de votre ordinateur local est `localhost` .</span><span class="sxs-lookup"><span data-stu-id="340af-277">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="340af-278">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="340af-278">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="340af-279">Le lancement de l’application sans débogage en sélectionnant CTRL + F5 vous permet d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="340af-279">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="340af-280">Modifiez le code.</span><span class="sxs-lookup"><span data-stu-id="340af-280">Make code changes.</span></span>
* <span data-ttu-id="340af-281">Enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="340af-281">Save the file.</span></span>
* <span data-ttu-id="340af-282">Actualisez rapidement le navigateur et consultez les modifications de code.</span><span class="sxs-lookup"><span data-stu-id="340af-282">Quickly refresh the browser and see the code changes.</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="340af-284">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="340af-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="340af-285">Sélectionnez **Exécuter** > **Exécuter sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="340af-285">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="340af-286">Visual Studio pour Mac : démarre [Kestrel](xref:fundamentals/servers/index#kestrel) Server, lance un navigateur et accède à `http://localhost:port` , où *port* est un numéro de port choisi au hasard.</span><span class="sxs-lookup"><span data-stu-id="340af-286">Visual Studio for Mac: starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<span data-ttu-id="340af-287">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="340af-287">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="340af-288">Le nom d’hôte standard de votre ordinateur local est `localhost` .</span><span class="sxs-lookup"><span data-stu-id="340af-288">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="340af-289">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="340af-289">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="340af-290">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="340af-290">When you run the app, you'll see a different port number.</span></span>

<span data-ttu-id="340af-291">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="340af-291">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="340af-292">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="340af-292">The following image shows the app:</span></span>

![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="340af-294">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="340af-294">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="340af-295">Next</span><span class="sxs-lookup"><span data-stu-id="340af-295">Next</span></span>](adding-controller.md)

::: moniker-end
