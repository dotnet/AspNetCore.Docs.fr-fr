---
title: 'Didacticiel : créer une API Web avec ASP.NET Core'
author: rick-anderson
description: Apprendre à créer une API web avec ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
no-loc:
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
- ':::no-loc(Models):::'
uid: tutorials/first-web-api
ms.openlocfilehash: 17f04dc9a0bdcf8ff016d83b915c017ff485cb36
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690700"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="a0197-103">Didacticiel : créer une API Web avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0197-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="a0197-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)et [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="a0197-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="a0197-105">Ce tutoriel décrit les principes fondamentaux liés à la génération d’une API web avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a0197-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a0197-106">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="a0197-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a0197-107">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="a0197-107">Create a web API project.</span></span>
> * <span data-ttu-id="a0197-108">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="a0197-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="a0197-109">Générer automatiquement des modèles pour un contrôleur avec des méthodes CRUD.</span><span class="sxs-lookup"><span data-stu-id="a0197-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="a0197-110">Configurer le routage, les chemins d’URL et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="a0197-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="a0197-111">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="a0197-111">Call the web API with Postman.</span></span>

<span data-ttu-id="a0197-112">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="a0197-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="a0197-113">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="a0197-113">Overview</span></span>

<span data-ttu-id="a0197-114">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="a0197-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="a0197-115">API</span><span class="sxs-lookup"><span data-stu-id="a0197-115">API</span></span> | <span data-ttu-id="a0197-116">Description</span><span class="sxs-lookup"><span data-stu-id="a0197-116">Description</span></span> | <span data-ttu-id="a0197-117">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="a0197-117">Request body</span></span> | <span data-ttu-id="a0197-118">Response body</span><span class="sxs-lookup"><span data-stu-id="a0197-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="a0197-119">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="a0197-119">Get all to-do items</span></span> | <span data-ttu-id="a0197-120">None</span><span class="sxs-lookup"><span data-stu-id="a0197-120">None</span></span> | <span data-ttu-id="a0197-121">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="a0197-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="a0197-122">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="a0197-122">Get an item by ID</span></span> | <span data-ttu-id="a0197-123">None</span><span class="sxs-lookup"><span data-stu-id="a0197-123">None</span></span> | <span data-ttu-id="a0197-124">Tâche</span><span class="sxs-lookup"><span data-stu-id="a0197-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="a0197-125">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="a0197-125">Add a new item</span></span> | <span data-ttu-id="a0197-126">Tâche</span><span class="sxs-lookup"><span data-stu-id="a0197-126">To-do item</span></span> | <span data-ttu-id="a0197-127">Tâche</span><span class="sxs-lookup"><span data-stu-id="a0197-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="a0197-128">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="a0197-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="a0197-129">Tâche</span><span class="sxs-lookup"><span data-stu-id="a0197-129">To-do item</span></span> | <span data-ttu-id="a0197-130">None</span><span class="sxs-lookup"><span data-stu-id="a0197-130">None</span></span> |
|<span data-ttu-id="a0197-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="a0197-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="a0197-132">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="a0197-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="a0197-133">None</span><span class="sxs-lookup"><span data-stu-id="a0197-133">None</span></span> | <span data-ttu-id="a0197-134">None</span><span class="sxs-lookup"><span data-stu-id="a0197-134">None</span></span>|

<span data-ttu-id="a0197-135">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-135">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="a0197-141">Prérequis</span><span class="sxs-lookup"><span data-stu-id="a0197-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0197-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0197-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0197-144">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="a0197-145">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="a0197-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0197-147">Dans le menu **fichier** , sélectionnez **nouveau** > **projet** .</span><span class="sxs-lookup"><span data-stu-id="a0197-147">From the **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="a0197-148">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="a0197-148">Select the **ASP.NET Core Web Application** template and click **Next** .</span></span>
* <span data-ttu-id="a0197-149">Nommez le projet *TodoApi* et cliquez sur **Créer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-149">Name the project *TodoApi* and click **Create** .</span></span>
* <span data-ttu-id="a0197-150">Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 5,0** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="a0197-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="a0197-151">Sélectionnez le modèle **API** et cliquez sur **Créer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-151">Select the **API** template and click **Create** .</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0197-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0197-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a0197-154">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a0197-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="a0197-155">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="a0197-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="a0197-156">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0197-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="a0197-157">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="a0197-157">When a dialog box asks if you want to add required assets to the project, select **Yes** .</span></span>

  <span data-ttu-id="a0197-158">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="a0197-158">The preceding commands:</span></span>

  * <span data-ttu-id="a0197-159">Créent un projet API Web et l’ouvrent dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a0197-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="a0197-160">Ajoutent les packages NuGet qui sont requis dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="a0197-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0197-161">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a0197-162">Sélectionnez **Fichier** > **Nouvelle solution** .</span><span class="sxs-lookup"><span data-stu-id="a0197-162">Select **File** > **New Solution** .</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="a0197-164">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >  **App**  >  **API**  >  **suivant** .</span><span class="sxs-lookup"><span data-stu-id="a0197-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next** .</span></span> <span data-ttu-id="a0197-165">Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >  **App**  >  **API** application console  >  **suivant** .</span><span class="sxs-lookup"><span data-stu-id="a0197-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![sélection du modèle d’API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="a0197-167">Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 3. x **Target Framework** .</span><span class="sxs-lookup"><span data-stu-id="a0197-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework** .</span></span> <span data-ttu-id="a0197-168">Sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="a0197-168">Select **Next** .</span></span>

* <span data-ttu-id="a0197-169">Entrez *TodoApi* comme **Nom du projet** , puis sélectionnez **Créer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-169">Enter *TodoApi* for the **Project Name** and then select **Create** .</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="a0197-171">Ouvrez un terminal de commande dans le dossier de projet, puis exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0197-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="a0197-172">Tester le projet</span><span class="sxs-lookup"><span data-stu-id="a0197-172">Test the project</span></span>

<span data-ttu-id="a0197-173">Le modèle de projet crée une `WeatherForecast` API avec prise en charge de [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="a0197-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a0197-175">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="a0197-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="a0197-176">Lancements de Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="a0197-176">Visual Studio launches:</span></span>

* <span data-ttu-id="a0197-177">Serveur Web IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a0197-177">The IIS Express web server.</span></span>
* <span data-ttu-id="a0197-178">Le navigateur par défaut et navigue vers `https://localhost:<port>/https://localhost:5001/swagger/index.html` , où `<port>` est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="a0197-178">The default browser and navigates to `https://localhost:<port>/https://localhost:5001/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0197-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0197-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="a0197-180">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="a0197-181">Dans un navigateur, accédez à l’URL suivante : [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="a0197-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0197-182">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a0197-183">Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="a0197-184">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="a0197-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="a0197-185">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="a0197-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="a0197-186">Ajoutez `/swagger` à l’URL (définissez-la sur `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="a0197-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="a0197-187">La page Swagger `/swagger/index.html` s’affiche.</span><span class="sxs-lookup"><span data-stu-id="a0197-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="a0197-188">Sélectionnez **faire**  >  **essayer** l'  >  **exécution** .</span><span class="sxs-lookup"><span data-stu-id="a0197-188">Select **GET** > **Try it out** > **Execute** .</span></span> <span data-ttu-id="a0197-189">La page affiche :</span><span class="sxs-lookup"><span data-stu-id="a0197-189">The page displays:</span></span>

* <span data-ttu-id="a0197-190">Commande de [boucle](https://curl.haxx.se/) pour tester l’API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="a0197-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="a0197-191">URL pour tester l’API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="a0197-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="a0197-192">Code, corps et en-têtes de la réponse.</span><span class="sxs-lookup"><span data-stu-id="a0197-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="a0197-193">Zone de liste déroulante avec les types de média et l’exemple de valeur et de schéma.</span><span class="sxs-lookup"><span data-stu-id="a0197-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="a0197-194">Swagger est utilisé pour générer une documentation utile et des pages d’aide pour les API Web.</span><span class="sxs-lookup"><span data-stu-id="a0197-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="a0197-195">Ce didacticiel se concentre sur la création d’une API Web.</span><span class="sxs-lookup"><span data-stu-id="a0197-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="a0197-196">Pour plus d’informations sur Swagger, consultez <xref:tutorials/web-api-help-pages-using-swagger> .</span><span class="sxs-lookup"><span data-stu-id="a0197-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="a0197-197">Copiez et collez l’URL de la **requête** dans le navigateur :  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="a0197-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="a0197-198">Un code JSON similaire au suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="a0197-198">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a><span data-ttu-id="a0197-199">Mettre à jour launchUrl</span><span class="sxs-lookup"><span data-stu-id="a0197-199">Update the launchUrl</span></span>

<span data-ttu-id="a0197-200">Dans *Properties\launchSettings.js* , mettez à jour `launchUrl` de `"swagger"` vers `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="a0197-200">In *Properties\launchSettings.json* , update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="a0197-201">Comme Swagger a été supprimé, le balisage précédent modifie l’URL qui est lancée vers la méthode d’extraction du contrôleur ajoutée dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="a0197-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="a0197-202">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="a0197-202">Add a model class</span></span>

<span data-ttu-id="a0197-203">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="a0197-204">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="a0197-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0197-206">Dans l’ **Explorateur de solutions** , cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="a0197-206">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="a0197-207">Sélectionnez **Ajouter**  >  **un nouveau dossier** .</span><span class="sxs-lookup"><span data-stu-id="a0197-207">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="a0197-208">Nommez le dossier *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="a0197-208">Name the folder *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="a0197-209">Cliquez avec le bouton droit sur le *:::no-loc(Models):::* dossier et sélectionnez **Ajouter** une  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="a0197-209">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="a0197-210">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="a0197-210">Name the class *TodoItem* and select **Add** .</span></span>

* <span data-ttu-id="a0197-211">Remplacez le code du modèle par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="a0197-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0197-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0197-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a0197-213">Ajoutez un dossier nommé *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="a0197-213">Add a folder named *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="a0197-214">Ajoutez une `TodoItem` classe au dossier à l' *:::no-loc(Models):::* aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-214">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0197-215">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a0197-216">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="a0197-216">Right-click the project.</span></span> <span data-ttu-id="a0197-217">Sélectionnez **Ajouter**  >  **un nouveau dossier** .</span><span class="sxs-lookup"><span data-stu-id="a0197-217">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="a0197-218">Nommez le dossier *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="a0197-218">Name the folder *:::no-loc(Models):::* .</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="a0197-220">Cliquez avec le bouton droit sur le *:::no-loc(Models):::* dossier, puis sélectionnez **Ajouter** > **un nouveau fichier** > **General** > **classe générale vide** .</span><span class="sxs-lookup"><span data-stu-id="a0197-220">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class** .</span></span>

* <span data-ttu-id="a0197-221">Nommez la classe *TodoItem* et cliquez sur **Nouveau** .</span><span class="sxs-lookup"><span data-stu-id="a0197-221">Name the class *TodoItem* , and then click **New** .</span></span>

* <span data-ttu-id="a0197-222">Remplacez le code du modèle par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="a0197-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/:::no-loc(Models):::/TodoItem.cs?name=snippet)]

<span data-ttu-id="a0197-223">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="a0197-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="a0197-224">Les classes de modèle peuvent se trouver n’importe où dans le projet, mais le *:::no-loc(Models):::* dossier est utilisé par Convention.</span><span class="sxs-lookup"><span data-stu-id="a0197-224">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="a0197-225">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="a0197-225">Add a database context</span></span>

<span data-ttu-id="a0197-226">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="a0197-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="a0197-227">Cette classe est créée en dérivant de la classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="a0197-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="a0197-229">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="a0197-229">Add NuGet packages</span></span>

* <span data-ttu-id="a0197-230">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution** .</span><span class="sxs-lookup"><span data-stu-id="a0197-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution** .</span></span>
* <span data-ttu-id="a0197-231">Sélectionnez l’onglet **Parcourir** , puis entrez \* \* Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a0197-231">Select the **Browse** tab, and then enter \*\*Microsoft.</span></span>
<span data-ttu-id="a0197-232">**EntityFrameworkCore. SqlServer** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="a0197-232">**EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="a0197-233">Activez la case à cocher inclure la version **préliminaire** afin que la version 5,0 RC soit disponible.</span><span class="sxs-lookup"><span data-stu-id="a0197-233">Select the **Include prerelease** checkbox so the 5.0 RC version is available.</span></span> 
* <span data-ttu-id="a0197-234">Dans le volet gauche, sélectionnez **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-234">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="a0197-235">Cochez la case **Projet** dans le volet droit, puis sélectionnez **Installer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-235">Select the **Project** check box in the right pane and then select **Install** .</span></span>
* <span data-ttu-id="a0197-236">Utilisez les instructions précédentes pour ajouter le package NuGet **Microsoft. EntityFrameworkCore. InMemory** .</span><span class="sxs-lookup"><span data-stu-id="a0197-236">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="a0197-237">![Gestionnaire de package NuGet](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="a0197-237">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="a0197-238">Ajouter le contexte de base de données TodoContext</span><span class="sxs-lookup"><span data-stu-id="a0197-238">Add the TodoContext database context</span></span>

* <span data-ttu-id="a0197-239">Cliquez avec le bouton droit sur le *:::no-loc(Models):::* dossier et sélectionnez **Ajouter** une  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="a0197-239">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="a0197-240">Nommez la classe *TodoContext* et cliquez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="a0197-240">Name the class *TodoContext* and click **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a0197-241">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="a0197-242">Ajoutez une `TodoContext` classe au *:::no-loc(Models):::* dossier.</span><span class="sxs-lookup"><span data-stu-id="a0197-242">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="a0197-243">Entrez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-243">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="a0197-244">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="a0197-244">Register the database context</span></span>

<span data-ttu-id="a0197-245">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a0197-245">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a0197-246">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="a0197-246">The container provides the service to controllers.</span></span>

<span data-ttu-id="a0197-247">Mettez à jour *Startup.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-247">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="a0197-248">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="a0197-248">The preceding code:</span></span>

* <span data-ttu-id="a0197-249">Supprime les appels Swagger.</span><span class="sxs-lookup"><span data-stu-id="a0197-249">Removes the Swagger calls.</span></span>
* <span data-ttu-id="a0197-250">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="a0197-250">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="a0197-251">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="a0197-251">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="a0197-252">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="a0197-252">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="a0197-253">Générer automatiquement des modèles pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="a0197-253">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-254">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0197-255">Cliquez avec le bouton droit sur le dossier *Contrôleurs* .</span><span class="sxs-lookup"><span data-stu-id="a0197-255">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="a0197-256">Sélectionnez **Ajouter** > **Nouvel élément généré automatiquement** .</span><span class="sxs-lookup"><span data-stu-id="a0197-256">Select **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="a0197-257">Sélectionnez **Contrôleur d’API avec actions, utilisant Entity Framework** , puis **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="a0197-257">Select **API Controller with actions, using Entity Framework** , and then select **Add** .</span></span>
* <span data-ttu-id="a0197-258">Dans la boîte de dialogue **Contrôleur d’API avec actions, utilisant Entity Framework**  :</span><span class="sxs-lookup"><span data-stu-id="a0197-258">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="a0197-259">Sélectionnez **TodoItem (TodoApi. :::no-loc(Models)::: )** dans la **classe de modèle** .</span><span class="sxs-lookup"><span data-stu-id="a0197-259">Select **TodoItem (TodoApi.:::no-loc(Models):::)** in the **Model class** .</span></span>
  * <span data-ttu-id="a0197-260">Sélectionnez **TodoContext (TodoApi. :::no-loc(Models)::: )** dans la **classe de contexte de données** .</span><span class="sxs-lookup"><span data-stu-id="a0197-260">Select **TodoContext (TodoApi.:::no-loc(Models):::)** in the **Data context class** .</span></span>
  * <span data-ttu-id="a0197-261">Sélectionnez **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="a0197-261">Select **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a0197-262">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-262">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="a0197-263">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0197-263">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="a0197-264">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="a0197-264">The preceding commands:</span></span>

* <span data-ttu-id="a0197-265">Ajoutent les packages NuGet nécessaires à la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="a0197-265">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="a0197-266">Installent le moteur de génération de modèles automatique (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="a0197-266">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="a0197-267">Génèrent automatiquement des modèles pour `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="a0197-267">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="a0197-268">Le code généré :</span><span class="sxs-lookup"><span data-stu-id="a0197-268">The generated code:</span></span>

* <span data-ttu-id="a0197-269">Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="a0197-269">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="a0197-270">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="a0197-270">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="a0197-271">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="a0197-271">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="a0197-272">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a0197-272">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="a0197-273">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a0197-273">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="a0197-274">Les modèles de ASP.NET Core pour :</span><span class="sxs-lookup"><span data-stu-id="a0197-274">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="a0197-275">Les contrôleurs avec des vues sont inclus `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="a0197-275">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="a0197-276">Les contrôleurs d’API n’incluent pas `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="a0197-276">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="a0197-277">Lorsque le `[action]` jeton n’est pas dans le modèle de routage, le nom de l' [action](xref:mvc/controllers/routing#action) est exclu de l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="a0197-277">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="a0197-278">Autrement dit, le nom de la méthode associée à l’action n’est pas utilisé dans l’itinéraire correspondant.</span><span class="sxs-lookup"><span data-stu-id="a0197-278">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="a0197-279">Mettre à jour la méthode de création de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0197-279">Update the PostTodoItem create method</span></span>

<span data-ttu-id="a0197-280">Remplacez l’instruction return dans `PostTodoItem` pour utiliser l’opérateur [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="a0197-280">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="a0197-281">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="a0197-281">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="a0197-282">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0197-282">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="a0197-283">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="a0197-283">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="a0197-284">La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> :</span><span class="sxs-lookup"><span data-stu-id="a0197-284">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="a0197-285">Retourne un [code d’état HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="a0197-285">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="a0197-286">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="a0197-286">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="a0197-287">Ajoute un en-tête [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à la réponse.</span><span class="sxs-lookup"><span data-stu-id="a0197-287">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="a0197-288">L’en-tête `Location` spécifie l’[URI](https://developer.mozilla.org/docs/Glossary/URI) de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="a0197-288">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="a0197-289">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="a0197-289">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="a0197-290">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="a0197-290">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="a0197-291">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="a0197-291">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="a0197-292">Installer Postman</span><span class="sxs-lookup"><span data-stu-id="a0197-292">Install Postman</span></span>

<span data-ttu-id="a0197-293">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="a0197-293">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="a0197-294">Installer le [poste](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="a0197-294">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="a0197-295">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="a0197-295">Start the web app.</span></span>
* <span data-ttu-id="a0197-296">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="a0197-296">Start Postman.</span></span>
* <span data-ttu-id="a0197-297">Désactivez la **vérification du certificat SSL** .</span><span class="sxs-lookup"><span data-stu-id="a0197-297">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="a0197-298">À partir de **Fichier** > **Paramètres** (onglet **Général** ), désactivez **Vérification du certificat SSL** .</span><span class="sxs-lookup"><span data-stu-id="a0197-298">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification** .</span></span>
    > [!WARNING]
    > <span data-ttu-id="a0197-299">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a0197-299">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="a0197-300">Tester PostTodoItem avec Postman</span><span class="sxs-lookup"><span data-stu-id="a0197-300">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="a0197-301">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="a0197-301">Create a new request.</span></span>
* <span data-ttu-id="a0197-302">Affectez `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0197-302">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="a0197-303">Définissez l’URI sur `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="a0197-303">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="a0197-304">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="a0197-304">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="a0197-305">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="a0197-305">Select the **Body** tab.</span></span>
* <span data-ttu-id="a0197-306">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="a0197-306">Select the **raw** radio button.</span></span>
* <span data-ttu-id="a0197-307">Définissez le type sur **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="a0197-307">Set the type to **JSON (application/json)** .</span></span>
* <span data-ttu-id="a0197-308">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="a0197-308">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="a0197-309">Sélectionnez **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-309">Select **Send** .</span></span>

  ![Postman avec requête de création](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="a0197-311">Tester l’URI de l’en-tête d’emplacement</span><span class="sxs-lookup"><span data-stu-id="a0197-311">Test the location header URI</span></span>

<span data-ttu-id="a0197-312">L’URI d’en-tête d’emplacement peut être testé dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="a0197-312">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="a0197-313">Copiez et collez l’URI d’en-tête d’emplacement dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="a0197-313">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="a0197-314">Pour effectuer un test dans le poster :</span><span class="sxs-lookup"><span data-stu-id="a0197-314">To test in Postman:</span></span>

* <span data-ttu-id="a0197-315">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="a0197-315">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="a0197-316">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="a0197-316">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="a0197-318">Affectez `GET` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0197-318">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="a0197-319">Définissez l’URI sur `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="a0197-319">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="a0197-320">Par exemple : `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="a0197-320">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="a0197-321">Sélectionnez **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-321">Select **Send** .</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="a0197-322">Examiner les méthodes GET</span><span class="sxs-lookup"><span data-stu-id="a0197-322">Examine the GET methods</span></span>

<span data-ttu-id="a0197-323">Deux points de terminaison d’extraction sont implémentés :</span><span class="sxs-lookup"><span data-stu-id="a0197-323">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="a0197-324">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur ou de Postman.</span><span class="sxs-lookup"><span data-stu-id="a0197-324">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="a0197-325">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a0197-325">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="a0197-326">Une réponse semblable à la suivante est produite par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="a0197-326">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="a0197-327">Tester Get avec Postman</span><span class="sxs-lookup"><span data-stu-id="a0197-327">Test Get with Postman</span></span>

* <span data-ttu-id="a0197-328">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="a0197-328">Create a new request.</span></span>
* <span data-ttu-id="a0197-329">Définissez la méthode HTTP sur **GET** .</span><span class="sxs-lookup"><span data-stu-id="a0197-329">Set the HTTP method to **GET** .</span></span>
* <span data-ttu-id="a0197-330">Définissez l’URI de la demande sur `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="a0197-330">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="a0197-331">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="a0197-331">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="a0197-332">Définissez l’ **affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="a0197-332">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="a0197-333">Sélectionnez **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-333">Select **Send** .</span></span>

<span data-ttu-id="a0197-334">Cette application utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="a0197-334">This app uses an in-memory database.</span></span> <span data-ttu-id="a0197-335">Si l’application est arrêtée et démarrée, la requête GET précédente ne retourne aucune donnée.</span><span class="sxs-lookup"><span data-stu-id="a0197-335">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="a0197-336">Si aucune donnée n’est retournée, publiez ([POST](#post)) les données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-336">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="a0197-337">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="a0197-337">Routing and URL paths</span></span>

<span data-ttu-id="a0197-338">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="a0197-338">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="a0197-339">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="a0197-339">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="a0197-340">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="a0197-340">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="a0197-341">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="a0197-341">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="a0197-342">Pour cet exemple, le nom de la classe du contrôleur étant **TodoItems** Controller, le nom du contrôleur est « TodoItems ».</span><span class="sxs-lookup"><span data-stu-id="a0197-342">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="a0197-343">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="a0197-343">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="a0197-344">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="a0197-344">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="a0197-345">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="a0197-345">This sample doesn't use a template.</span></span> <span data-ttu-id="a0197-346">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="a0197-346">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="a0197-347">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="a0197-347">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="a0197-348">Lorsque `GetTodoItem` est appelé, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son `id` paramètre.</span><span class="sxs-lookup"><span data-stu-id="a0197-348">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="a0197-349">Valeurs retournées</span><span class="sxs-lookup"><span data-stu-id="a0197-349">Return values</span></span>

<span data-ttu-id="a0197-350">Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="a0197-350">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="a0197-351">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="a0197-351">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="a0197-352">Le code de réponse pour ce type de retour est [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), en supposant qu’il n’existe aucune exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="a0197-352">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="a0197-353">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="a0197-353">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="a0197-354">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0197-354">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="a0197-355">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="a0197-355">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="a0197-356">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un code d’erreur d' [état 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .</span><span class="sxs-lookup"><span data-stu-id="a0197-356">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="a0197-357">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="a0197-357">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="a0197-358">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="a0197-358">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="a0197-359">Méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0197-359">The PutTodoItem method</span></span>

<span data-ttu-id="a0197-360">Examinez la méthode `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="a0197-360">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="a0197-361">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="a0197-361">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="a0197-362">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="a0197-362">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="a0197-363">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="a0197-363">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="a0197-364">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="a0197-364">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="a0197-365">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="a0197-365">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="a0197-366">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0197-366">Test the PutTodoItem method</span></span>

<span data-ttu-id="a0197-367">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="a0197-367">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="a0197-368">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="a0197-368">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="a0197-369">Appelez obtenir pour vous assurer qu’il y a un élément dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="a0197-369">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="a0197-370">Mettez à jour l’élément de tâche dont l’ID est égal à 1 et affectez-lui comme nom `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="a0197-370">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="a0197-371">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="a0197-371">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="a0197-373">Méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0197-373">The DeleteTodoItem method</span></span>

<span data-ttu-id="a0197-374">Examinez la méthode `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="a0197-374">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="a0197-375">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0197-375">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="a0197-376">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="a0197-376">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="a0197-377">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="a0197-377">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="a0197-378">Définissez l’URI de l’objet à supprimer (par exemple `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="a0197-378">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="a0197-379">Sélectionnez **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-379">Select **Send** .</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="a0197-380">Empêcher la post-validation</span><span class="sxs-lookup"><span data-stu-id="a0197-380">Prevent over-posting</span></span>

<span data-ttu-id="a0197-381">Actuellement, l’exemple d’application expose l' `TodoItem` objet entier.</span><span class="sxs-lookup"><span data-stu-id="a0197-381">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="a0197-382">En général, les applications de production limitent les données entrées et retournées à l’aide d’un sous-ensemble du modèle.</span><span class="sxs-lookup"><span data-stu-id="a0197-382">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="a0197-383">Il existe plusieurs raisons à cela, et la sécurité est essentielle.</span><span class="sxs-lookup"><span data-stu-id="a0197-383">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="a0197-384">Le sous-ensemble d’un modèle est généralement appelé un objet Transfert de données (DTO), un modèle d’entrée ou un modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="a0197-384">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="a0197-385">Le **DTO** est utilisé dans cet article.</span><span class="sxs-lookup"><span data-stu-id="a0197-385">**DTO** is used in this article.</span></span>

<span data-ttu-id="a0197-386">Un DTO peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="a0197-386">A DTO may be used to:</span></span>

* <span data-ttu-id="a0197-387">Empêcher la survalidation.</span><span class="sxs-lookup"><span data-stu-id="a0197-387">Prevent over-posting.</span></span>
* <span data-ttu-id="a0197-388">Masquer les propriétés que les clients ne sont pas censés afficher.</span><span class="sxs-lookup"><span data-stu-id="a0197-388">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="a0197-389">Omettez certaines propriétés afin de réduire la taille de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="a0197-389">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="a0197-390">Aplatir les graphiques d’objets qui contiennent des objets imbriqués.</span><span class="sxs-lookup"><span data-stu-id="a0197-390">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="a0197-391">Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.</span><span class="sxs-lookup"><span data-stu-id="a0197-391">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="a0197-392">Pour illustrer l’approche DTO, mettez à jour la `TodoItem` classe pour inclure un champ secret :</span><span class="sxs-lookup"><span data-stu-id="a0197-392">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/:::no-loc(Models):::/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="a0197-393">Le champ secret doit être masqué à partir de cette application, mais une application administrative peut choisir de l’exposer.</span><span class="sxs-lookup"><span data-stu-id="a0197-393">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="a0197-394">Vérifiez que vous pouvez poster et recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="a0197-394">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="a0197-395">Créer un modèle DTO :</span><span class="sxs-lookup"><span data-stu-id="a0197-395">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/:::no-loc(Models):::/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="a0197-396">Mettez à jour le `TodoItemsController` pour utiliser `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="a0197-396">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="a0197-397">Vérifiez que vous ne pouvez pas poster ou recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="a0197-397">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="a0197-398">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="a0197-398">Call the web API with JavaScript</span></span>

<span data-ttu-id="a0197-399">Consultez [Didacticiel : appeler une API web ASP.net core avec JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="a0197-399">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="a0197-400">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="a0197-400">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a0197-401">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="a0197-401">Create a web API project.</span></span>
> * <span data-ttu-id="a0197-402">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="a0197-402">Add a model class and a database context.</span></span>
> * <span data-ttu-id="a0197-403">Générer automatiquement des modèles pour un contrôleur avec des méthodes CRUD.</span><span class="sxs-lookup"><span data-stu-id="a0197-403">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="a0197-404">Configurer le routage, les chemins d’URL et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="a0197-404">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="a0197-405">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="a0197-405">Call the web API with Postman.</span></span>

<span data-ttu-id="a0197-406">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="a0197-406">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="a0197-407">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="a0197-407">Overview</span></span>

<span data-ttu-id="a0197-408">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="a0197-408">This tutorial creates the following API:</span></span>

|<span data-ttu-id="a0197-409">API</span><span class="sxs-lookup"><span data-stu-id="a0197-409">API</span></span> | <span data-ttu-id="a0197-410">Description</span><span class="sxs-lookup"><span data-stu-id="a0197-410">Description</span></span> | <span data-ttu-id="a0197-411">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="a0197-411">Request body</span></span> | <span data-ttu-id="a0197-412">Response body</span><span class="sxs-lookup"><span data-stu-id="a0197-412">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="a0197-413">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="a0197-413">Get all to-do items</span></span> | <span data-ttu-id="a0197-414">None</span><span class="sxs-lookup"><span data-stu-id="a0197-414">None</span></span> | <span data-ttu-id="a0197-415">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="a0197-415">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="a0197-416">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="a0197-416">Get an item by ID</span></span> | <span data-ttu-id="a0197-417">None</span><span class="sxs-lookup"><span data-stu-id="a0197-417">None</span></span> | <span data-ttu-id="a0197-418">Tâche</span><span class="sxs-lookup"><span data-stu-id="a0197-418">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="a0197-419">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="a0197-419">Add a new item</span></span> | <span data-ttu-id="a0197-420">Tâche</span><span class="sxs-lookup"><span data-stu-id="a0197-420">To-do item</span></span> | <span data-ttu-id="a0197-421">Tâche</span><span class="sxs-lookup"><span data-stu-id="a0197-421">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="a0197-422">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="a0197-422">Update an existing item &nbsp;</span></span> | <span data-ttu-id="a0197-423">Tâche</span><span class="sxs-lookup"><span data-stu-id="a0197-423">To-do item</span></span> | <span data-ttu-id="a0197-424">None</span><span class="sxs-lookup"><span data-stu-id="a0197-424">None</span></span> |
|<span data-ttu-id="a0197-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="a0197-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="a0197-426">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="a0197-426">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="a0197-427">None</span><span class="sxs-lookup"><span data-stu-id="a0197-427">None</span></span> | <span data-ttu-id="a0197-428">None</span><span class="sxs-lookup"><span data-stu-id="a0197-428">None</span></span>|

<span data-ttu-id="a0197-429">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-429">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="a0197-435">Prérequis</span><span class="sxs-lookup"><span data-stu-id="a0197-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0197-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0197-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0197-438">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="a0197-439">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="a0197-439">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-440">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0197-441">Dans le menu **fichier** , sélectionnez **nouveau** > **projet** .</span><span class="sxs-lookup"><span data-stu-id="a0197-441">From the **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="a0197-442">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="a0197-442">Select the **ASP.NET Core Web Application** template and click **Next** .</span></span>
* <span data-ttu-id="a0197-443">Nommez le projet *TodoApi* et cliquez sur **Créer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-443">Name the project *TodoApi* and click **Create** .</span></span>
* <span data-ttu-id="a0197-444">Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 3,1** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="a0197-444">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="a0197-445">Sélectionnez le modèle **API** et cliquez sur **Créer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-445">Select the **API** template and click **Create** .</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0197-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0197-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a0197-448">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a0197-448">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="a0197-449">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="a0197-449">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="a0197-450">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0197-450">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="a0197-451">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="a0197-451">When a dialog box asks if you want to add required assets to the project, select **Yes** .</span></span>

  <span data-ttu-id="a0197-452">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="a0197-452">The preceding commands:</span></span>

  * <span data-ttu-id="a0197-453">Créent un projet API Web et l’ouvrent dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a0197-453">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="a0197-454">Ajoutent les packages NuGet qui sont requis dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="a0197-454">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0197-455">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a0197-456">Sélectionnez **Fichier** > **Nouvelle solution** .</span><span class="sxs-lookup"><span data-stu-id="a0197-456">Select **File** > **New Solution** .</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="a0197-458">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >  **App**  >  **API**  >  **suivant** .</span><span class="sxs-lookup"><span data-stu-id="a0197-458">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next** .</span></span> <span data-ttu-id="a0197-459">Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >  **App**  >  **API** application console  >  **suivant** .</span><span class="sxs-lookup"><span data-stu-id="a0197-459">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![sélection du modèle d’API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="a0197-461">Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 3. x **Target Framework** .</span><span class="sxs-lookup"><span data-stu-id="a0197-461">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework** .</span></span> <span data-ttu-id="a0197-462">Sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="a0197-462">Select **Next** .</span></span>

* <span data-ttu-id="a0197-463">Entrez *TodoApi* comme **Nom du projet** , puis sélectionnez **Créer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-463">Enter *TodoApi* for the **Project Name** and then select **Create** .</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="a0197-465">Ouvrez un terminal de commande dans le dossier de projet, puis exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0197-465">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="a0197-466">Tester l’API</span><span class="sxs-lookup"><span data-stu-id="a0197-466">Test the API</span></span>

<span data-ttu-id="a0197-467">Le modèle de projet crée une API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="a0197-467">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="a0197-468">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-468">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a0197-470">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-470">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="a0197-471">Visual Studio lance un navigateur et accède à `https://localhost:<port>/WeatherForecast`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="a0197-471">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="a0197-472">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="a0197-472">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes** .</span></span> <span data-ttu-id="a0197-473">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="a0197-473">In the **Security Warning** dialog that appears next, select **Yes** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0197-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0197-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a0197-475">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-475">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="a0197-476">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="a0197-476">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0197-477">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a0197-478">Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-478">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="a0197-479">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="a0197-479">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="a0197-480">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="a0197-480">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="a0197-481">Ajoutez `/WeatherForecast` à l’URL (définissez-la sur `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="a0197-481">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="a0197-482">Un code JSON similaire au suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="a0197-482">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="a0197-483">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="a0197-483">Add a model class</span></span>

<span data-ttu-id="a0197-484">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-484">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="a0197-485">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="a0197-485">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0197-487">Dans l’ **Explorateur de solutions** , cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="a0197-487">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="a0197-488">Sélectionnez **Ajouter**  >  **un nouveau dossier** .</span><span class="sxs-lookup"><span data-stu-id="a0197-488">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="a0197-489">Nommez le dossier *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="a0197-489">Name the folder *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="a0197-490">Cliquez avec le bouton droit sur le *:::no-loc(Models):::* dossier et sélectionnez **Ajouter** une  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="a0197-490">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="a0197-491">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="a0197-491">Name the class *TodoItem* and select **Add** .</span></span>

* <span data-ttu-id="a0197-492">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-492">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0197-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0197-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a0197-494">Ajoutez un dossier nommé *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="a0197-494">Add a folder named *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="a0197-495">Ajoutez une `TodoItem` classe au dossier à l' *:::no-loc(Models):::* aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-495">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0197-496">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-496">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a0197-497">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="a0197-497">Right-click the project.</span></span> <span data-ttu-id="a0197-498">Sélectionnez **Ajouter**  >  **un nouveau dossier** .</span><span class="sxs-lookup"><span data-stu-id="a0197-498">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="a0197-499">Nommez le dossier *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="a0197-499">Name the folder *:::no-loc(Models):::* .</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="a0197-501">Cliquez avec le bouton droit sur le *:::no-loc(Models):::* dossier, puis sélectionnez **Ajouter** > **un nouveau fichier** > **General** > **classe générale vide** .</span><span class="sxs-lookup"><span data-stu-id="a0197-501">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class** .</span></span>

* <span data-ttu-id="a0197-502">Nommez la classe *TodoItem* et cliquez sur **Nouveau** .</span><span class="sxs-lookup"><span data-stu-id="a0197-502">Name the class *TodoItem* , and then click **New** .</span></span>

* <span data-ttu-id="a0197-503">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-503">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/:::no-loc(Models):::/TodoItem.cs?name=snippet)]

<span data-ttu-id="a0197-504">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="a0197-504">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="a0197-505">Les classes de modèle peuvent se trouver n’importe où dans le projet, mais le *:::no-loc(Models):::* dossier est utilisé par Convention.</span><span class="sxs-lookup"><span data-stu-id="a0197-505">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="a0197-506">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="a0197-506">Add a database context</span></span>

<span data-ttu-id="a0197-507">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="a0197-507">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="a0197-508">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="a0197-508">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-509">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="a0197-510">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="a0197-510">Add NuGet packages</span></span>

* <span data-ttu-id="a0197-511">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution** .</span><span class="sxs-lookup"><span data-stu-id="a0197-511">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution** .</span></span>
* <span data-ttu-id="a0197-512">Sélectionnez l’onglet **Parcourir** , puis entrez **Microsoft.EntityFrameworkCore.SqlServer** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="a0197-512">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="a0197-513">Dans le volet gauche, sélectionnez **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-513">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="a0197-514">Cochez la case **Projet** dans le volet droit, puis sélectionnez **Installer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-514">Select the **Project** check box in the right pane and then select **Install** .</span></span>
* <span data-ttu-id="a0197-515">Utilisez les instructions précédentes pour ajouter le package NuGet **Microsoft. EntityFrameworkCore. InMemory** .</span><span class="sxs-lookup"><span data-stu-id="a0197-515">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Gestionnaire de package NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="a0197-517">Ajouter le contexte de base de données TodoContext</span><span class="sxs-lookup"><span data-stu-id="a0197-517">Add the TodoContext database context</span></span>

* <span data-ttu-id="a0197-518">Cliquez avec le bouton droit sur le *:::no-loc(Models):::* dossier et sélectionnez **Ajouter** une  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="a0197-518">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="a0197-519">Nommez la classe *TodoContext* et cliquez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="a0197-519">Name the class *TodoContext* and click **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a0197-520">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-520">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="a0197-521">Ajoutez une `TodoContext` classe au *:::no-loc(Models):::* dossier.</span><span class="sxs-lookup"><span data-stu-id="a0197-521">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="a0197-522">Entrez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-522">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="a0197-523">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="a0197-523">Register the database context</span></span>

<span data-ttu-id="a0197-524">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a0197-524">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a0197-525">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="a0197-525">The container provides the service to controllers.</span></span>

<span data-ttu-id="a0197-526">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-526">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="a0197-527">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="a0197-527">The preceding code:</span></span>

* <span data-ttu-id="a0197-528">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="a0197-528">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="a0197-529">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="a0197-529">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="a0197-530">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="a0197-530">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="a0197-531">Générer automatiquement des modèles pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="a0197-531">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-532">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-532">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0197-533">Cliquez avec le bouton droit sur le dossier *Contrôleurs* .</span><span class="sxs-lookup"><span data-stu-id="a0197-533">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="a0197-534">Sélectionnez **Ajouter** > **Nouvel élément généré automatiquement** .</span><span class="sxs-lookup"><span data-stu-id="a0197-534">Select **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="a0197-535">Sélectionnez **Contrôleur d’API avec actions, utilisant Entity Framework** , puis **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="a0197-535">Select **API Controller with actions, using Entity Framework** , and then select **Add** .</span></span>
* <span data-ttu-id="a0197-536">Dans la boîte de dialogue **Contrôleur d’API avec actions, utilisant Entity Framework**  :</span><span class="sxs-lookup"><span data-stu-id="a0197-536">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="a0197-537">Sélectionnez **TodoItem (TodoApi. :::no-loc(Models)::: )** dans la **classe de modèle** .</span><span class="sxs-lookup"><span data-stu-id="a0197-537">Select **TodoItem (TodoApi.:::no-loc(Models):::)** in the **Model class** .</span></span>
  * <span data-ttu-id="a0197-538">Sélectionnez **TodoContext (TodoApi. :::no-loc(Models)::: )** dans la **classe de contexte de données** .</span><span class="sxs-lookup"><span data-stu-id="a0197-538">Select **TodoContext (TodoApi.:::no-loc(Models):::)** in the **Data context class** .</span></span>
  * <span data-ttu-id="a0197-539">Sélectionnez **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="a0197-539">Select **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a0197-540">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-540">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="a0197-541">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0197-541">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="a0197-542">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="a0197-542">The preceding commands:</span></span>

* <span data-ttu-id="a0197-543">Ajoutent les packages NuGet nécessaires à la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="a0197-543">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="a0197-544">Installent le moteur de génération de modèles automatique (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="a0197-544">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="a0197-545">Génèrent automatiquement des modèles pour `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="a0197-545">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="a0197-546">Le code généré :</span><span class="sxs-lookup"><span data-stu-id="a0197-546">The generated code:</span></span>

* <span data-ttu-id="a0197-547">Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="a0197-547">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="a0197-548">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="a0197-548">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="a0197-549">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="a0197-549">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="a0197-550">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a0197-550">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="a0197-551">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a0197-551">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="a0197-552">Les modèles de ASP.NET Core pour :</span><span class="sxs-lookup"><span data-stu-id="a0197-552">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="a0197-553">Les contrôleurs avec des vues sont inclus `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="a0197-553">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="a0197-554">Les contrôleurs d’API n’incluent pas `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="a0197-554">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="a0197-555">Lorsque le `[action]` jeton n’est pas dans le modèle de routage, le nom de l' [action](xref:mvc/controllers/routing#action) est exclu de l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="a0197-555">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="a0197-556">Autrement dit, le nom de la méthode associée à l’action n’est pas utilisé dans l’itinéraire correspondant.</span><span class="sxs-lookup"><span data-stu-id="a0197-556">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="a0197-557">Examiner la méthode de création de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0197-557">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="a0197-558">Remplacez l’instruction return dans `PostTodoItem` pour utiliser l’opérateur [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="a0197-558">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="a0197-559">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="a0197-559">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="a0197-560">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0197-560">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="a0197-561">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="a0197-561">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="a0197-562">La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> :</span><span class="sxs-lookup"><span data-stu-id="a0197-562">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="a0197-563">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="a0197-563">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="a0197-564">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="a0197-564">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="a0197-565">Ajoute un en-tête [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à la réponse.</span><span class="sxs-lookup"><span data-stu-id="a0197-565">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="a0197-566">L’en-tête `Location` spécifie l’[URI](https://developer.mozilla.org/docs/Glossary/URI) de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="a0197-566">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="a0197-567">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="a0197-567">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="a0197-568">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="a0197-568">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="a0197-569">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="a0197-569">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="a0197-570">Installer Postman</span><span class="sxs-lookup"><span data-stu-id="a0197-570">Install Postman</span></span>

<span data-ttu-id="a0197-571">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="a0197-571">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="a0197-572">Installer le [poste](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="a0197-572">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="a0197-573">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="a0197-573">Start the web app.</span></span>
* <span data-ttu-id="a0197-574">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="a0197-574">Start Postman.</span></span>
* <span data-ttu-id="a0197-575">Désactivez la **vérification du certificat SSL** .</span><span class="sxs-lookup"><span data-stu-id="a0197-575">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="a0197-576">À partir de **Fichier** > **Paramètres** (onglet **Général** ), désactivez **Vérification du certificat SSL** .</span><span class="sxs-lookup"><span data-stu-id="a0197-576">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification** .</span></span>
    > [!WARNING]
    > <span data-ttu-id="a0197-577">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a0197-577">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="a0197-578">Tester PostTodoItem avec Postman</span><span class="sxs-lookup"><span data-stu-id="a0197-578">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="a0197-579">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="a0197-579">Create a new request.</span></span>
* <span data-ttu-id="a0197-580">Affectez `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0197-580">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="a0197-581">Définissez l’URI sur `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="a0197-581">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="a0197-582">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="a0197-582">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="a0197-583">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="a0197-583">Select the **Body** tab.</span></span>
* <span data-ttu-id="a0197-584">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="a0197-584">Select the **raw** radio button.</span></span>
* <span data-ttu-id="a0197-585">Définissez le type sur **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="a0197-585">Set the type to **JSON (application/json)** .</span></span>
* <span data-ttu-id="a0197-586">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="a0197-586">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="a0197-587">Sélectionnez **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-587">Select **Send** .</span></span>

  ![Postman avec requête de création](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="a0197-589">Tester l’URI d’en-tête d’emplacement avec le poster</span><span class="sxs-lookup"><span data-stu-id="a0197-589">Test the location header URI with Postman</span></span>

* <span data-ttu-id="a0197-590">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="a0197-590">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="a0197-591">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="a0197-591">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="a0197-593">Affectez `GET` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0197-593">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="a0197-594">Définissez l’URI sur `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="a0197-594">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="a0197-595">Par exemple : `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="a0197-595">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="a0197-596">Sélectionnez **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-596">Select **Send** .</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="a0197-597">Examiner les méthodes GET</span><span class="sxs-lookup"><span data-stu-id="a0197-597">Examine the GET methods</span></span>

<span data-ttu-id="a0197-598">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="a0197-598">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="a0197-599">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur ou de Postman.</span><span class="sxs-lookup"><span data-stu-id="a0197-599">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="a0197-600">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a0197-600">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="a0197-601">Une réponse semblable à la suivante est produite par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="a0197-601">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="a0197-602">Tester Get avec Postman</span><span class="sxs-lookup"><span data-stu-id="a0197-602">Test Get with Postman</span></span>

* <span data-ttu-id="a0197-603">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="a0197-603">Create a new request.</span></span>
* <span data-ttu-id="a0197-604">Définissez la méthode HTTP sur **GET** .</span><span class="sxs-lookup"><span data-stu-id="a0197-604">Set the HTTP method to **GET** .</span></span>
* <span data-ttu-id="a0197-605">Définissez l’URI de la demande sur `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="a0197-605">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="a0197-606">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="a0197-606">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="a0197-607">Définissez l’ **affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="a0197-607">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="a0197-608">Sélectionnez **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-608">Select **Send** .</span></span>

<span data-ttu-id="a0197-609">Cette application utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="a0197-609">This app uses an in-memory database.</span></span> <span data-ttu-id="a0197-610">Si l’application est arrêtée et démarrée, la requête GET précédente ne retourne aucune donnée.</span><span class="sxs-lookup"><span data-stu-id="a0197-610">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="a0197-611">Si aucune donnée n’est retournée, publiez ([POST](#post)) les données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-611">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="a0197-612">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="a0197-612">Routing and URL paths</span></span>

<span data-ttu-id="a0197-613">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="a0197-613">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="a0197-614">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="a0197-614">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="a0197-615">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="a0197-615">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="a0197-616">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="a0197-616">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="a0197-617">Pour cet exemple, le nom de la classe du contrôleur étant **TodoItems** Controller, le nom du contrôleur est « TodoItems ».</span><span class="sxs-lookup"><span data-stu-id="a0197-617">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="a0197-618">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="a0197-618">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="a0197-619">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="a0197-619">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="a0197-620">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="a0197-620">This sample doesn't use a template.</span></span> <span data-ttu-id="a0197-621">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="a0197-621">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="a0197-622">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="a0197-622">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="a0197-623">Lorsque `GetTodoItem` est appelé, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son `id` paramètre.</span><span class="sxs-lookup"><span data-stu-id="a0197-623">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="a0197-624">Valeurs retournées</span><span class="sxs-lookup"><span data-stu-id="a0197-624">Return values</span></span> 

<span data-ttu-id="a0197-625">Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="a0197-625">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="a0197-626">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="a0197-626">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="a0197-627">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="a0197-627">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="a0197-628">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="a0197-628">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="a0197-629">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0197-629">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="a0197-630">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="a0197-630">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="a0197-631">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> code d’erreur 404.</span><span class="sxs-lookup"><span data-stu-id="a0197-631">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="a0197-632">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="a0197-632">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="a0197-633">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="a0197-633">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="a0197-634">Méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0197-634">The PutTodoItem method</span></span>

<span data-ttu-id="a0197-635">Examinez la méthode `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="a0197-635">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="a0197-636">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="a0197-636">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="a0197-637">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="a0197-637">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="a0197-638">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="a0197-638">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="a0197-639">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="a0197-639">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="a0197-640">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="a0197-640">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="a0197-641">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0197-641">Test the PutTodoItem method</span></span>

<span data-ttu-id="a0197-642">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="a0197-642">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="a0197-643">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="a0197-643">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="a0197-644">Appelez obtenir pour vous assurer qu’il y a un élément dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="a0197-644">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="a0197-645">Mettez à jour l’élément de tâche dont l’ID est égal à 1 et affectez-lui le nom « flux de poisson » :</span><span class="sxs-lookup"><span data-stu-id="a0197-645">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="a0197-646">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="a0197-646">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="a0197-648">Méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0197-648">The DeleteTodoItem method</span></span>

<span data-ttu-id="a0197-649">Examinez la méthode `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="a0197-649">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="a0197-650">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0197-650">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="a0197-651">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="a0197-651">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="a0197-652">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="a0197-652">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="a0197-653">Définissez l’URI de l’objet à supprimer (par exemple `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="a0197-653">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="a0197-654">Sélectionnez **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-654">Select **Send** .</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="a0197-655">Empêcher la post-validation</span><span class="sxs-lookup"><span data-stu-id="a0197-655">Prevent over-posting</span></span>

<span data-ttu-id="a0197-656">Actuellement, l’exemple d’application expose l' `TodoItem` objet entier.</span><span class="sxs-lookup"><span data-stu-id="a0197-656">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="a0197-657">En général, les applications de production limitent les données entrées et retournées à l’aide d’un sous-ensemble du modèle.</span><span class="sxs-lookup"><span data-stu-id="a0197-657">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="a0197-658">Il existe plusieurs raisons à cela, et la sécurité est essentielle.</span><span class="sxs-lookup"><span data-stu-id="a0197-658">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="a0197-659">Le sous-ensemble d’un modèle est généralement appelé un objet Transfert de données (DTO), un modèle d’entrée ou un modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="a0197-659">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="a0197-660">Le **DTO** est utilisé dans cet article.</span><span class="sxs-lookup"><span data-stu-id="a0197-660">**DTO** is used in this article.</span></span>

<span data-ttu-id="a0197-661">Un DTO peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="a0197-661">A DTO may be used to:</span></span>

* <span data-ttu-id="a0197-662">Empêcher la survalidation.</span><span class="sxs-lookup"><span data-stu-id="a0197-662">Prevent over-posting.</span></span>
* <span data-ttu-id="a0197-663">Masquer les propriétés que les clients ne sont pas censés afficher.</span><span class="sxs-lookup"><span data-stu-id="a0197-663">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="a0197-664">Omettez certaines propriétés afin de réduire la taille de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="a0197-664">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="a0197-665">Aplatir les graphiques d’objets qui contiennent des objets imbriqués.</span><span class="sxs-lookup"><span data-stu-id="a0197-665">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="a0197-666">Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.</span><span class="sxs-lookup"><span data-stu-id="a0197-666">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="a0197-667">Pour illustrer l’approche DTO, mettez à jour la `TodoItem` classe pour inclure un champ secret :</span><span class="sxs-lookup"><span data-stu-id="a0197-667">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/:::no-loc(Models):::/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="a0197-668">Le champ secret doit être masqué à partir de cette application, mais une application administrative peut choisir de l’exposer.</span><span class="sxs-lookup"><span data-stu-id="a0197-668">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="a0197-669">Vérifiez que vous pouvez poster et recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="a0197-669">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="a0197-670">Créer un modèle DTO :</span><span class="sxs-lookup"><span data-stu-id="a0197-670">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/:::no-loc(Models):::/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="a0197-671">Mettez à jour le `TodoItemsController` pour utiliser `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="a0197-671">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="a0197-672">Vérifiez que vous ne pouvez pas poster ou recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="a0197-672">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="a0197-673">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="a0197-673">Call the web API with JavaScript</span></span>

<span data-ttu-id="a0197-674">Consultez [Didacticiel : appeler une API web ASP.net core avec JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="a0197-674">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a0197-675">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="a0197-675">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a0197-676">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="a0197-676">Create a web API project.</span></span>
> * <span data-ttu-id="a0197-677">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="a0197-677">Add a model class and a database context.</span></span>
> * <span data-ttu-id="a0197-678">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="a0197-678">Add a controller.</span></span>
> * <span data-ttu-id="a0197-679">Ajouter les méthodes CRUD</span><span class="sxs-lookup"><span data-stu-id="a0197-679">Add CRUD methods.</span></span>
> * <span data-ttu-id="a0197-680">Configurer le routage et les chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="a0197-680">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="a0197-681">Spécifier des valeurs de retour</span><span class="sxs-lookup"><span data-stu-id="a0197-681">Specify return values.</span></span>
> * <span data-ttu-id="a0197-682">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="a0197-682">Call the web API with Postman.</span></span>
> * <span data-ttu-id="a0197-683">Appeler l’API web avec JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a0197-683">Call the web API with JavaScript.</span></span>

<span data-ttu-id="a0197-684">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="a0197-684">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="a0197-685">Vue d’ensemble 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-685">Overview 2.1</span></span>

<span data-ttu-id="a0197-686">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="a0197-686">This tutorial creates the following API:</span></span>

|<span data-ttu-id="a0197-687">API</span><span class="sxs-lookup"><span data-stu-id="a0197-687">API</span></span> | <span data-ttu-id="a0197-688">Description</span><span class="sxs-lookup"><span data-stu-id="a0197-688">Description</span></span> | <span data-ttu-id="a0197-689">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="a0197-689">Request body</span></span> | <span data-ttu-id="a0197-690">Response body</span><span class="sxs-lookup"><span data-stu-id="a0197-690">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="a0197-691">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="a0197-691">GET /api/TodoItems</span></span> | <span data-ttu-id="a0197-692">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="a0197-692">Get all to-do items</span></span> | <span data-ttu-id="a0197-693">None</span><span class="sxs-lookup"><span data-stu-id="a0197-693">None</span></span> | <span data-ttu-id="a0197-694">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="a0197-694">Array of to-do items</span></span>|
|<span data-ttu-id="a0197-695">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="a0197-695">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="a0197-696">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="a0197-696">Get an item by ID</span></span> | <span data-ttu-id="a0197-697">None</span><span class="sxs-lookup"><span data-stu-id="a0197-697">None</span></span> | <span data-ttu-id="a0197-698">Tâche</span><span class="sxs-lookup"><span data-stu-id="a0197-698">To-do item</span></span>|
|<span data-ttu-id="a0197-699">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="a0197-699">POST /api/TodoItems</span></span> | <span data-ttu-id="a0197-700">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="a0197-700">Add a new item</span></span> | <span data-ttu-id="a0197-701">Tâche</span><span class="sxs-lookup"><span data-stu-id="a0197-701">To-do item</span></span> | <span data-ttu-id="a0197-702">Tâche</span><span class="sxs-lookup"><span data-stu-id="a0197-702">To-do item</span></span> |
|<span data-ttu-id="a0197-703">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="a0197-703">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="a0197-704">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="a0197-704">Update an existing item &nbsp;</span></span> | <span data-ttu-id="a0197-705">Tâche</span><span class="sxs-lookup"><span data-stu-id="a0197-705">To-do item</span></span> | <span data-ttu-id="a0197-706">None</span><span class="sxs-lookup"><span data-stu-id="a0197-706">None</span></span> |
|<span data-ttu-id="a0197-707">SUPPRIMER/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="a0197-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="a0197-708">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="a0197-708">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="a0197-709">None</span><span class="sxs-lookup"><span data-stu-id="a0197-709">None</span></span> | <span data-ttu-id="a0197-710">None</span><span class="sxs-lookup"><span data-stu-id="a0197-710">None</span></span>|

<span data-ttu-id="a0197-711">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-711">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="a0197-717">Conditions préalables 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-717">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-718">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0197-719">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0197-719">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0197-720">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-720">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="a0197-721">Créer un projet Web 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-721">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-722">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-722">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0197-723">Dans le menu **fichier** , sélectionnez **nouveau** > **projet** .</span><span class="sxs-lookup"><span data-stu-id="a0197-723">From the **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="a0197-724">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="a0197-724">Select the **ASP.NET Core Web Application** template and click **Next** .</span></span>
* <span data-ttu-id="a0197-725">Nommez le projet *TodoApi* et cliquez sur **Créer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-725">Name the project *TodoApi* and click **Create** .</span></span>
* <span data-ttu-id="a0197-726">Dans la boîte de dialogue **Créer une application web ASP.NET Core** , vérifiez que **.NET Core** et **ASP.NET Core 2.2** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="a0197-726">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="a0197-727">Sélectionnez le modèle **API** et cliquez sur **Créer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-727">Select the **API** template and click **Create** .</span></span> <span data-ttu-id="a0197-728">Ne sélectionnez **pas\*\*\*\*Activer la prise en charge de Docker** .</span><span class="sxs-lookup"><span data-stu-id="a0197-728">**Don't** select **Enable Docker Support** .</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0197-730">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0197-730">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a0197-731">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a0197-731">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="a0197-732">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="a0197-732">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="a0197-733">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0197-733">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="a0197-734">Ces commandes créent un projet d’API web et ouvrent une nouvelle instance de Visual Studio Code dans le nouveau dossier de projet.</span><span class="sxs-lookup"><span data-stu-id="a0197-734">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="a0197-735">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="a0197-735">When a dialog box asks if you want to add required assets to the project, select **Yes** .</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0197-736">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-736">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a0197-737">Sélectionnez **Fichier** > **Nouvelle solution** .</span><span class="sxs-lookup"><span data-stu-id="a0197-737">Select **File** > **New Solution** .</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="a0197-739">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >  **App**  >  **API**  >  **suivant** .</span><span class="sxs-lookup"><span data-stu-id="a0197-739">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next** .</span></span> <span data-ttu-id="a0197-740">Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >  **App**  >  **API** application console  >  **suivant** .</span><span class="sxs-lookup"><span data-stu-id="a0197-740">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>
  
* <span data-ttu-id="a0197-741">Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 2. x **Target Framework** .</span><span class="sxs-lookup"><span data-stu-id="a0197-741">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework** .</span></span> <span data-ttu-id="a0197-742">Sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="a0197-742">Select **Next** .</span></span>

* <span data-ttu-id="a0197-743">Entrez *TodoApi* comme **Nom du projet** , puis sélectionnez **Créer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-743">Enter *TodoApi* for the **Project Name** and then select **Create** .</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="a0197-745">Tester l’API 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-745">Test the API 2.1</span></span>

<span data-ttu-id="a0197-746">Le modèle de projet crée une API `values`.</span><span class="sxs-lookup"><span data-stu-id="a0197-746">The project template creates a `values` API.</span></span> <span data-ttu-id="a0197-747">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-747">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-748">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-748">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a0197-749">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-749">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="a0197-750">Visual Studio lance un navigateur et accède à `https://localhost:<port>/api/values`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="a0197-750">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="a0197-751">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="a0197-751">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes** .</span></span> <span data-ttu-id="a0197-752">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="a0197-752">In the **Security Warning** dialog that appears next, select **Yes** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0197-753">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0197-753">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a0197-754">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-754">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="a0197-755">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="a0197-755">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0197-756">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-756">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a0197-757">Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-757">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="a0197-758">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="a0197-758">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="a0197-759">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="a0197-759">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="a0197-760">Ajoutez `/api/values` à l’URL (définissez-la sur `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="a0197-760">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="a0197-761">Le code JSON suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="a0197-761">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="a0197-762">Ajouter une classe de modèle 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-762">Add a model class 2.1</span></span>

<span data-ttu-id="a0197-763">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="a0197-763">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="a0197-764">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="a0197-764">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-765">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-765">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0197-766">Dans l’ **Explorateur de solutions** , cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="a0197-766">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="a0197-767">Sélectionnez **Ajouter**  >  **un nouveau dossier** .</span><span class="sxs-lookup"><span data-stu-id="a0197-767">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="a0197-768">Nommez le dossier *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="a0197-768">Name the folder *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="a0197-769">Cliquez avec le bouton droit sur le *:::no-loc(Models):::* dossier et sélectionnez **Ajouter** une  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="a0197-769">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="a0197-770">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="a0197-770">Name the class *TodoItem* and select **Add** .</span></span>

* <span data-ttu-id="a0197-771">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-771">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0197-772">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0197-772">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a0197-773">Ajoutez un dossier nommé *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="a0197-773">Add a folder named *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="a0197-774">Ajoutez une `TodoItem` classe au dossier à l' *:::no-loc(Models):::* aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-774">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0197-775">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-775">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a0197-776">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="a0197-776">Right-click the project.</span></span> <span data-ttu-id="a0197-777">Sélectionnez **Ajouter**  >  **un nouveau dossier** .</span><span class="sxs-lookup"><span data-stu-id="a0197-777">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="a0197-778">Nommez le dossier *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="a0197-778">Name the folder *:::no-loc(Models):::* .</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="a0197-780">Cliquez avec le bouton droit sur le *:::no-loc(Models):::* dossier, puis sélectionnez **Ajouter** > **un nouveau fichier** > **General** > **classe générale vide** .</span><span class="sxs-lookup"><span data-stu-id="a0197-780">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class** .</span></span>

* <span data-ttu-id="a0197-781">Nommez la classe *TodoItem* et cliquez sur **Nouveau** .</span><span class="sxs-lookup"><span data-stu-id="a0197-781">Name the class *TodoItem* , and then click **New** .</span></span>

* <span data-ttu-id="a0197-782">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-782">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/:::no-loc(Models):::/TodoItem.cs)]

<span data-ttu-id="a0197-783">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="a0197-783">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="a0197-784">Les classes de modèle peuvent se trouver n’importe où dans le projet, mais le *:::no-loc(Models):::* dossier est utilisé par Convention.</span><span class="sxs-lookup"><span data-stu-id="a0197-784">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="a0197-785">Ajouter un contexte de base de données 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-785">Add a database context 2.1</span></span>

<span data-ttu-id="a0197-786">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="a0197-786">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="a0197-787">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="a0197-787">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-788">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-788">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0197-789">Cliquez avec le bouton droit sur le *:::no-loc(Models):::* dossier et sélectionnez **Ajouter** une  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="a0197-789">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="a0197-790">Nommez la classe *TodoContext* et cliquez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="a0197-790">Name the class *TodoContext* and click **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a0197-791">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-791">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="a0197-792">Ajoutez une `TodoContext` classe au *:::no-loc(Models):::* dossier.</span><span class="sxs-lookup"><span data-stu-id="a0197-792">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="a0197-793">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-793">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="a0197-794">Inscrire le contexte de base de données 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-794">Register the database context 2.1</span></span>

<span data-ttu-id="a0197-795">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a0197-795">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a0197-796">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="a0197-796">The container provides the service to controllers.</span></span>

<span data-ttu-id="a0197-797">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-797">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="a0197-798">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="a0197-798">The preceding code:</span></span>

* <span data-ttu-id="a0197-799">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="a0197-799">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="a0197-800">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="a0197-800">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="a0197-801">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="a0197-801">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="a0197-802">Ajouter un contrôleur 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-802">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-803">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-803">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0197-804">Cliquez avec le bouton droit sur le dossier *Contrôleurs* .</span><span class="sxs-lookup"><span data-stu-id="a0197-804">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="a0197-805">Sélectionnez **Ajouter** > **un nouvel élément** .</span><span class="sxs-lookup"><span data-stu-id="a0197-805">Select **Add** > **New Item** .</span></span>
* <span data-ttu-id="a0197-806">Dans la boîte de dialogue **Ajouter un nouvel élément** , sélectionnez le modèle **Classe de contrôleur d’API** .</span><span class="sxs-lookup"><span data-stu-id="a0197-806">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="a0197-807">Nommez la classe *TodoController* et sélectionnez **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="a0197-807">Name the class *TodoController* , and select **Add** .</span></span>

  ![Boîte de dialogue Ajouter un nouvel élément avec contrôleur dans la zone de recherche et le contrôleur des API web sélectionné](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a0197-809">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-809">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="a0197-810">Dans le dossier *Contrôleurs* , créez une classe nommée `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="a0197-810">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="a0197-811">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-811">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="a0197-812">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="a0197-812">The preceding code:</span></span>

* <span data-ttu-id="a0197-813">Définit une classe de contrôleur d’API sans méthodes.</span><span class="sxs-lookup"><span data-stu-id="a0197-813">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="a0197-814">Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="a0197-814">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="a0197-815">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="a0197-815">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="a0197-816">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="a0197-816">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="a0197-817">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a0197-817">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="a0197-818">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a0197-818">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="a0197-819">Ajoute un élément nommé `Item1` à la base de données si celle-ci est vide.</span><span class="sxs-lookup"><span data-stu-id="a0197-819">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="a0197-820">Ce code se trouvant dans le constructeur, il s’exécute chaque fois qu’une nouvelle requête HTTP existe.</span><span class="sxs-lookup"><span data-stu-id="a0197-820">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="a0197-821">Si vous supprimez tous les éléments, le constructeur recrée `Item1` au prochain appel d’une méthode d’API.</span><span class="sxs-lookup"><span data-stu-id="a0197-821">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="a0197-822">Ainsi, il peut vous sembler à tort que la suppression n’a pas fonctionné.</span><span class="sxs-lookup"><span data-stu-id="a0197-822">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="a0197-823">Ajouter des méthodes de récupération 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-823">Add Get methods 2.1</span></span>

<span data-ttu-id="a0197-824">Pour fournir une API qui récupère les tâches, ajoutez les méthodes suivantes à la classe `TodoController` :</span><span class="sxs-lookup"><span data-stu-id="a0197-824">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="a0197-825">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="a0197-825">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="a0197-826">Arrêtez l’application si elle est toujours en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="a0197-826">Stop the app if it's still running.</span></span> <span data-ttu-id="a0197-827">Ensuite, réexécutez-la pour inclure les dernières modifications.</span><span class="sxs-lookup"><span data-stu-id="a0197-827">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="a0197-828">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur.</span><span class="sxs-lookup"><span data-stu-id="a0197-828">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="a0197-829">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a0197-829">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="a0197-830">La réponse HTTP suivante est générée par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="a0197-830">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="a0197-831">Routage et chemins d’URL 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-831">Routing and URL paths 2.1</span></span>

<span data-ttu-id="a0197-832">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="a0197-832">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="a0197-833">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="a0197-833">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="a0197-834">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="a0197-834">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="a0197-835">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="a0197-835">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="a0197-836">Pour cet exemple, le nom de classe du contrôleur étant **Todo** Controller, le nom du contrôleur est « todo ».</span><span class="sxs-lookup"><span data-stu-id="a0197-836">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="a0197-837">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="a0197-837">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="a0197-838">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="a0197-838">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="a0197-839">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="a0197-839">This sample doesn't use a template.</span></span> <span data-ttu-id="a0197-840">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="a0197-840">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="a0197-841">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="a0197-841">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="a0197-842">Quand `GetTodoItem` est appelée, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son paramètre `id`.</span><span class="sxs-lookup"><span data-stu-id="a0197-842">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="a0197-843">Valeurs de retour 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-843">Return values 2.1</span></span>

<span data-ttu-id="a0197-844">Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="a0197-844">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="a0197-845">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="a0197-845">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="a0197-846">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="a0197-846">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="a0197-847">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="a0197-847">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="a0197-848">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0197-848">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="a0197-849">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="a0197-849">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="a0197-850">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> code d’erreur 404.</span><span class="sxs-lookup"><span data-stu-id="a0197-850">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="a0197-851">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="a0197-851">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="a0197-852">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="a0197-852">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="a0197-853">Tester la méthode GetTodoItems 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-853">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="a0197-854">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="a0197-854">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="a0197-855">Installer [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="a0197-855">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="a0197-856">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="a0197-856">Start the web app.</span></span>
* <span data-ttu-id="a0197-857">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="a0197-857">Start Postman.</span></span>
* <span data-ttu-id="a0197-858">Désactivez la **vérification du certificat SSL** .</span><span class="sxs-lookup"><span data-stu-id="a0197-858">Disable **SSL certificate verification** .</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0197-859">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0197-859">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0197-860">À partir de **Fichier** > **Paramètres** (onglet **Général** ), désactivez **Vérification du certificat SSL** .</span><span class="sxs-lookup"><span data-stu-id="a0197-860">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a0197-861">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a0197-861">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="a0197-862">Dans **Postman**  >  **Préférences** de publication (onglet **général** ), désactivez la vérification de **certificat SSL** .</span><span class="sxs-lookup"><span data-stu-id="a0197-862">From **Postman** > **Preferences** ( **General** tab), disable **SSL certificate verification** .</span></span> <span data-ttu-id="a0197-863">Vous pouvez également sélectionner la clé et sélectionner **Paramètres** , puis désactiver la vérification du certificat SSL.</span><span class="sxs-lookup"><span data-stu-id="a0197-863">Alternatively, select the wrench and select **Settings** , then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="a0197-864">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a0197-864">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="a0197-865">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="a0197-865">Create a new request.</span></span>
  * <span data-ttu-id="a0197-866">Définissez la méthode HTTP sur **GET** .</span><span class="sxs-lookup"><span data-stu-id="a0197-866">Set the HTTP method to **GET** .</span></span>
  * <span data-ttu-id="a0197-867">Définissez l’URI de la demande sur `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="a0197-867">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="a0197-868">Par exemple : `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="a0197-868">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="a0197-869">Définissez l’ **affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="a0197-869">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="a0197-870">Sélectionnez **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-870">Select **Send** .</span></span>

![Postman avec requête Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="a0197-872">Ajouter une méthode de création 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-872">Add a Create method 2.1</span></span>

<span data-ttu-id="a0197-873">Ajoutez la méthode `PostTodoItem` suivante à *Controllers/TodoController.cs* :</span><span class="sxs-lookup"><span data-stu-id="a0197-873">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs* :</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="a0197-874">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="a0197-874">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="a0197-875">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0197-875">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="a0197-876">La méthode `CreatedAtAction` :</span><span class="sxs-lookup"><span data-stu-id="a0197-876">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="a0197-877">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="a0197-877">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="a0197-878">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="a0197-878">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="a0197-879">Ajoute un en-tête `Location` à la réponse.</span><span class="sxs-lookup"><span data-stu-id="a0197-879">Adds a `Location` header to the response.</span></span> <span data-ttu-id="a0197-880">L’en-tête `Location` spécifie l’URI de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="a0197-880">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="a0197-881">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="a0197-881">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="a0197-882">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="a0197-882">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="a0197-883">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="a0197-883">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="a0197-884">Tester la méthode PostTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-884">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="a0197-885">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="a0197-885">Build the project.</span></span>
* <span data-ttu-id="a0197-886">Dans Postman, définissez la méthode HTTP sur `POST`.</span><span class="sxs-lookup"><span data-stu-id="a0197-886">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="a0197-887">Définissez l’URI sur `https://localhost:<port>/api/TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="a0197-887">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="a0197-888">Par exemple : `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="a0197-888">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="a0197-889">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="a0197-889">Select the **Body** tab.</span></span>
* <span data-ttu-id="a0197-890">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="a0197-890">Select the **raw** radio button.</span></span>
* <span data-ttu-id="a0197-891">Définissez le type sur **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="a0197-891">Set the type to **JSON (application/json)** .</span></span>
* <span data-ttu-id="a0197-892">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="a0197-892">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="a0197-893">Sélectionnez **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-893">Select **Send** .</span></span>

  ![Postman avec requête de création](first-web-api/_static/create.png)

  <span data-ttu-id="a0197-895">Si vous obtenez une erreur 405 Méthode non autorisée, il est probable que le projet n’ait pas été compilé après l’ajout de la méthode `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="a0197-895">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="a0197-896">Tester l’URI d’en-tête d’emplacement 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-896">Test the location header URI 2.1</span></span>

* <span data-ttu-id="a0197-897">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="a0197-897">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="a0197-898">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="a0197-898">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="a0197-900">Définissez la méthode sur GET.</span><span class="sxs-lookup"><span data-stu-id="a0197-900">Set the method to GET.</span></span>
* <span data-ttu-id="a0197-901">Définissez l’URI sur `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="a0197-901">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="a0197-902">Par exemple : `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="a0197-902">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="a0197-903">Sélectionnez **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-903">Select **Send** .</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="a0197-904">Ajouter une méthode PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-904">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="a0197-905">Ajoutez la méthode `PutTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="a0197-905">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="a0197-906">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="a0197-906">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="a0197-907">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="a0197-907">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="a0197-908">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="a0197-908">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="a0197-909">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="a0197-909">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="a0197-910">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="a0197-910">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="a0197-911">Tester la méthode PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-911">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="a0197-912">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="a0197-912">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="a0197-913">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="a0197-913">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="a0197-914">Appelez obtenir pour vous assurer qu’il y a un élément dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="a0197-914">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="a0197-915">Mettez à jour l’élément de tâche dont l’ID est égal à 1 et affectez-lui le nom « flux de poisson » :</span><span class="sxs-lookup"><span data-stu-id="a0197-915">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="a0197-916">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="a0197-916">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="a0197-918">Ajouter une méthode DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-918">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="a0197-919">Ajoutez la méthode `DeleteTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="a0197-919">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="a0197-920">La réponse `DeleteTodoItem` est [204 (Pas de contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="a0197-920">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="a0197-921">Tester la méthode DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-921">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="a0197-922">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="a0197-922">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="a0197-923">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="a0197-923">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="a0197-924">Définissez l’URI de l’objet à supprimer (par exemple, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="a0197-924">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="a0197-925">Sélectionnez **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="a0197-925">Select **Send** .</span></span>

<span data-ttu-id="a0197-926">L’exemple d’application vous permet de supprimer tous les éléments.</span><span class="sxs-lookup"><span data-stu-id="a0197-926">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="a0197-927">Toutefois, quand le dernier élément est supprimé, un autre est créé par le constructeur de classe de modèle au prochain appel de l’API.</span><span class="sxs-lookup"><span data-stu-id="a0197-927">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="a0197-928">Appeler l’API Web avec JavaScript 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-928">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="a0197-929">Dans cette section, une page HTML qui utilise JavaScript pour appeler l’API web est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="a0197-929">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="a0197-930">jQuery lance la demande.</span><span class="sxs-lookup"><span data-stu-id="a0197-930">jQuery initiates the request.</span></span> <span data-ttu-id="a0197-931">JavaScript met à jour la page avec les détails de la réponse de l’API Web.</span><span class="sxs-lookup"><span data-stu-id="a0197-931">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="a0197-932">Configurez l’application pour [traiter les fichiers statiques](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) et [activer le mappage de fichier par défaut](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) en mettant à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-932">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="a0197-933">Créez un dossier *wwwroot* dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="a0197-933">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="a0197-934">Ajoutez un fichier HTML nommé *index.html* au répertoire *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="a0197-934">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="a0197-935">Remplacez son contenu par le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-935">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="a0197-936">Ajoutez un fichier JavaScript nommé *site.js* au répertoire *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="a0197-936">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="a0197-937">Remplacez le contenu par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a0197-937">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="a0197-938">Vous devrez peut-être changer les paramètres de lancement du projet ASP.NET Core pour tester la page HTML localement :</span><span class="sxs-lookup"><span data-stu-id="a0197-938">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="a0197-939">Ouvrez *Properties\launchSettings.json* .</span><span class="sxs-lookup"><span data-stu-id="a0197-939">Open *Properties\launchSettings.json* .</span></span>
* <span data-ttu-id="a0197-940">Supprimez la `launchUrl` propriété pour forcer l’ouverture de l’application à *index.html* &mdash; fichier par défaut du projet.</span><span class="sxs-lookup"><span data-stu-id="a0197-940">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="a0197-941">Cet exemple appelle toutes les méthodes CRUD de l’API web.</span><span class="sxs-lookup"><span data-stu-id="a0197-941">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="a0197-942">Les explications suivantes traitent des appels à l’API.</span><span class="sxs-lookup"><span data-stu-id="a0197-942">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="a0197-943">Obtenir la liste des éléments de tâche 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-943">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="a0197-944">jQuery envoie une requête HTTP obtenir à l’API Web, qui retourne JSON représentant un tableau d’éléments de tâche.</span><span class="sxs-lookup"><span data-stu-id="a0197-944">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="a0197-945">La fonction de rappel `success` est appelée si la requête réussit.</span><span class="sxs-lookup"><span data-stu-id="a0197-945">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="a0197-946">Dans le rappel, le DOM est mis à jour avec les informations des tâches.</span><span class="sxs-lookup"><span data-stu-id="a0197-946">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="a0197-947">Ajouter un élément de tâche 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-947">Add a to-do item 2.1</span></span>

<span data-ttu-id="a0197-948">jQuery envoie une requête HTTP POSTÉRIEURe avec l’élément de tâche dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="a0197-948">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="a0197-949">Les options `accepts` et `contentType` sont définies avec la valeur `application/json` pour spécifier le type de média qui est reçu et envoyé.</span><span class="sxs-lookup"><span data-stu-id="a0197-949">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="a0197-950">La tâche est convertie au format JSON à l’aide de [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="a0197-950">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="a0197-951">Quand l’API retourne un code d’état de réussite, la fonction `getData` est appelée pour mettre à jour la table HTML.</span><span class="sxs-lookup"><span data-stu-id="a0197-951">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="a0197-952">Mettre à jour un élément de tâche 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-952">Update a to-do item 2.1</span></span>

<span data-ttu-id="a0197-953">La mise à jour d’une tâche est similaire à l’ajout d’une tâche.</span><span class="sxs-lookup"><span data-stu-id="a0197-953">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="a0197-954">L’identificateur unique de la tâche est ajouté à l’`url` et le `type` est `PUT`.</span><span class="sxs-lookup"><span data-stu-id="a0197-954">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="a0197-955">Supprimer un élément de tâche 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-955">Delete a to-do item 2.1</span></span>

<span data-ttu-id="a0197-956">Pour supprimer une tâche, vous devez définir le `type` sur l’appel AJAX avec la valeur `DELETE` et spécifier l’identificateur unique de l’élément dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="a0197-956">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="a0197-957">Ajouter la prise en charge de l’authentification à une API Web 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-957">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="a0197-958">Ressources supplémentaires 2,1</span><span class="sxs-lookup"><span data-stu-id="a0197-958">Additional resources 2.1</span></span>

<span data-ttu-id="a0197-959">[Affichez ou téléchargez l’exemple de code de ce tutoriel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="a0197-959">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="a0197-960">Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="a0197-960">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="a0197-961">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0197-961">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="a0197-962">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="a0197-962">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
