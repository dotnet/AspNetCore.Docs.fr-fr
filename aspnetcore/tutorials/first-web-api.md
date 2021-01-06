---
title: 'Tutoriel : Création d’une API web avec ASP.NET Core'
author: rick-anderson
description: Apprendre à créer une API web avec ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: ccbfc27eb89e23938a69f0ab4cb306d6a4136889
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96175050"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="100ea-103">Tutoriel : Création d’une API web avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="100ea-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="100ea-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)et [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="100ea-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="100ea-105">Ce tutoriel décrit les principes fondamentaux liés à la génération d’une API web avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="100ea-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="100ea-106">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="100ea-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="100ea-107">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="100ea-107">Create a web API project.</span></span>
> * <span data-ttu-id="100ea-108">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="100ea-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="100ea-109">Générer automatiquement des modèles pour un contrôleur avec des méthodes CRUD.</span><span class="sxs-lookup"><span data-stu-id="100ea-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="100ea-110">Configurer le routage, les chemins d’URL et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="100ea-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="100ea-111">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="100ea-111">Call the web API with Postman.</span></span>

<span data-ttu-id="100ea-112">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="100ea-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="100ea-113">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="100ea-113">Overview</span></span>

<span data-ttu-id="100ea-114">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="100ea-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="100ea-115">API</span><span class="sxs-lookup"><span data-stu-id="100ea-115">API</span></span> | <span data-ttu-id="100ea-116">Description</span><span class="sxs-lookup"><span data-stu-id="100ea-116">Description</span></span> | <span data-ttu-id="100ea-117">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="100ea-117">Request body</span></span> | <span data-ttu-id="100ea-118">Response body</span><span class="sxs-lookup"><span data-stu-id="100ea-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="100ea-119">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="100ea-119">Get all to-do items</span></span> | <span data-ttu-id="100ea-120">None</span><span class="sxs-lookup"><span data-stu-id="100ea-120">None</span></span> | <span data-ttu-id="100ea-121">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="100ea-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="100ea-122">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="100ea-122">Get an item by ID</span></span> | <span data-ttu-id="100ea-123">None</span><span class="sxs-lookup"><span data-stu-id="100ea-123">None</span></span> | <span data-ttu-id="100ea-124">Tâche</span><span class="sxs-lookup"><span data-stu-id="100ea-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="100ea-125">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="100ea-125">Add a new item</span></span> | <span data-ttu-id="100ea-126">Tâche</span><span class="sxs-lookup"><span data-stu-id="100ea-126">To-do item</span></span> | <span data-ttu-id="100ea-127">Tâche</span><span class="sxs-lookup"><span data-stu-id="100ea-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="100ea-128">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="100ea-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="100ea-129">Tâche</span><span class="sxs-lookup"><span data-stu-id="100ea-129">To-do item</span></span> | <span data-ttu-id="100ea-130">None</span><span class="sxs-lookup"><span data-stu-id="100ea-130">None</span></span> |
|<span data-ttu-id="100ea-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="100ea-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="100ea-132">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="100ea-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="100ea-133">None</span><span class="sxs-lookup"><span data-stu-id="100ea-133">None</span></span> | <span data-ttu-id="100ea-134">None</span><span class="sxs-lookup"><span data-stu-id="100ea-134">None</span></span>|

<span data-ttu-id="100ea-135">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-135">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="100ea-141">Prérequis</span><span class="sxs-lookup"><span data-stu-id="100ea-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="100ea-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="100ea-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="100ea-144">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="100ea-145">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="100ea-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="100ea-147">Dans le menu **fichier** , sélectionnez **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="100ea-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="100ea-148">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="100ea-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="100ea-149">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="100ea-150">Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 5,0** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="100ea-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="100ea-151">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-151">Select the **API** template and click **Create**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="100ea-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="100ea-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="100ea-154">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="100ea-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="100ea-155">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="100ea-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="100ea-156">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="100ea-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="100ea-157">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="100ea-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="100ea-158">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="100ea-158">The preceding commands:</span></span>

  * <span data-ttu-id="100ea-159">Créent un projet API Web et l’ouvrent dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="100ea-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="100ea-160">Ajoutent les packages NuGet qui sont requis dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="100ea-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="100ea-161">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="100ea-162">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="100ea-162">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="100ea-164">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >    >    >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="100ea-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="100ea-165">Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >    >  **API** application console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="100ea-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![sélection du modèle d’API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="100ea-167">Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 5. x **Target Framework**.</span><span class="sxs-lookup"><span data-stu-id="100ea-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="100ea-168">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="100ea-168">Select **Next**.</span></span>

* <span data-ttu-id="100ea-169">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="100ea-171">Ouvrez un terminal de commande dans le dossier de projet, puis exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="100ea-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="100ea-172">Tester le projet</span><span class="sxs-lookup"><span data-stu-id="100ea-172">Test the project</span></span>

<span data-ttu-id="100ea-173">Le modèle de projet crée une `WeatherForecast` API avec prise en charge de [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="100ea-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="100ea-175">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="100ea-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="100ea-176">Lancements de Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="100ea-176">Visual Studio launches:</span></span>

* <span data-ttu-id="100ea-177">Serveur Web IIS Express.</span><span class="sxs-lookup"><span data-stu-id="100ea-177">The IIS Express web server.</span></span>
* <span data-ttu-id="100ea-178">Le navigateur par défaut et navigue vers `https://localhost:<port>/swagger/index.html` , où `<port>` est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="100ea-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="100ea-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="100ea-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="100ea-180">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="100ea-181">Dans un navigateur, accédez à l’URL suivante : [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="100ea-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="100ea-182">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="100ea-183">Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="100ea-184">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="100ea-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="100ea-185">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="100ea-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="100ea-186">Ajoutez `/swagger` à l’URL (définissez-la sur `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="100ea-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="100ea-187">La page Swagger `/swagger/index.html` s’affiche.</span><span class="sxs-lookup"><span data-stu-id="100ea-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="100ea-188">Sélectionnez **faire**  >  **essayer** l'  >  **exécution**.</span><span class="sxs-lookup"><span data-stu-id="100ea-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="100ea-189">La page affiche :</span><span class="sxs-lookup"><span data-stu-id="100ea-189">The page displays:</span></span>

* <span data-ttu-id="100ea-190">Commande de [boucle](https://curl.haxx.se/) pour tester l’API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="100ea-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="100ea-191">URL pour tester l’API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="100ea-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="100ea-192">Code, corps et en-têtes de la réponse.</span><span class="sxs-lookup"><span data-stu-id="100ea-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="100ea-193">Zone de liste déroulante avec les types de média et l’exemple de valeur et de schéma.</span><span class="sxs-lookup"><span data-stu-id="100ea-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="100ea-194">Swagger est utilisé pour générer une documentation utile et des pages d’aide pour les API Web.</span><span class="sxs-lookup"><span data-stu-id="100ea-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="100ea-195">Ce didacticiel se concentre sur la création d’une API Web.</span><span class="sxs-lookup"><span data-stu-id="100ea-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="100ea-196">Pour plus d’informations sur Swagger, consultez <xref:tutorials/web-api-help-pages-using-swagger> .</span><span class="sxs-lookup"><span data-stu-id="100ea-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="100ea-197">Copiez et collez l’URL de la **requête** dans le navigateur :  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="100ea-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="100ea-198">Un code JSON similaire au suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="100ea-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="100ea-199">Mettre à jour launchUrl</span><span class="sxs-lookup"><span data-stu-id="100ea-199">Update the launchUrl</span></span>

<span data-ttu-id="100ea-200">Dans *Properties\launchSettings.js*, mettez à jour `launchUrl` de `"swagger"` vers `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="100ea-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="100ea-201">Comme Swagger a été supprimé, le balisage précédent modifie l’URL qui est lancée vers la méthode d’extraction du contrôleur ajoutée dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="100ea-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="100ea-202">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="100ea-202">Add a model class</span></span>

<span data-ttu-id="100ea-203">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="100ea-204">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="100ea-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="100ea-206">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="100ea-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="100ea-207">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="100ea-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="100ea-208">Nommez le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="100ea-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="100ea-209">Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="100ea-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="100ea-210">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="100ea-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="100ea-211">Remplacez le code du modèle par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="100ea-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="100ea-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="100ea-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="100ea-213">Ajoutez un dossier nommé *Models* .</span><span class="sxs-lookup"><span data-stu-id="100ea-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="100ea-214">Ajoutez une `TodoItem` classe au dossier à l' *Models* aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="100ea-215">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="100ea-216">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="100ea-216">Right-click the project.</span></span> <span data-ttu-id="100ea-217">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="100ea-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="100ea-218">Nommez le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="100ea-218">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="100ea-220">Cliquez avec le bouton droit sur le *Models* dossier, puis sélectionnez **Ajouter** > **un nouveau fichier** >  > **classe générale vide**.</span><span class="sxs-lookup"><span data-stu-id="100ea-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="100ea-221">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="100ea-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="100ea-222">Remplacez le code du modèle par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="100ea-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="100ea-223">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="100ea-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="100ea-224">Les classes de modèle peuvent se trouver n’importe où dans le projet, mais le *Models* dossier est utilisé par Convention.</span><span class="sxs-lookup"><span data-stu-id="100ea-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="100ea-225">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="100ea-225">Add a database context</span></span>

<span data-ttu-id="100ea-226">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="100ea-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="100ea-227">Cette classe est créée en dérivant de la classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="100ea-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="100ea-229">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="100ea-229">Add NuGet packages</span></span>

* <span data-ttu-id="100ea-230">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution**.</span><span class="sxs-lookup"><span data-stu-id="100ea-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="100ea-231">Sélectionnez l’onglet **Parcourir**, puis entrez **Microsoft.EntityFrameworkCore.SqlServer** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="100ea-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="100ea-232">Dans le volet gauche, sélectionnez **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="100ea-232">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="100ea-233">Cochez la case **Projet** dans le volet droit, puis sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="100ea-234">Utilisez les instructions précédentes pour ajouter le package NuGet **Microsoft. EntityFrameworkCore. InMemory** .</span><span class="sxs-lookup"><span data-stu-id="100ea-234">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="100ea-235">![Gestionnaire de package NuGet](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="100ea-235">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="100ea-236">Ajouter le contexte de base de données TodoContext</span><span class="sxs-lookup"><span data-stu-id="100ea-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="100ea-237">Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="100ea-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="100ea-238">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="100ea-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="100ea-239">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="100ea-240">Ajoutez une `TodoContext` classe au *Models* dossier.</span><span class="sxs-lookup"><span data-stu-id="100ea-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="100ea-241">Entrez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="100ea-242">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="100ea-242">Register the database context</span></span>

<span data-ttu-id="100ea-243">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="100ea-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="100ea-244">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="100ea-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="100ea-245">Mettez à jour *Startup.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="100ea-246">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="100ea-246">The preceding code:</span></span>

* <span data-ttu-id="100ea-247">Supprime les appels Swagger.</span><span class="sxs-lookup"><span data-stu-id="100ea-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="100ea-248">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="100ea-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="100ea-249">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="100ea-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="100ea-250">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="100ea-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="100ea-251">Générer automatiquement des modèles pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="100ea-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="100ea-253">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="100ea-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="100ea-254">Sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="100ea-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="100ea-255">Sélectionnez **Contrôleur d’API avec actions, utilisant Entity Framework**, puis **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="100ea-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="100ea-256">Dans la boîte de dialogue **Contrôleur d’API avec actions, utilisant Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="100ea-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="100ea-257">Sélectionnez **TodoItem (TodoApi. Models )** dans la **classe de modèle**.</span><span class="sxs-lookup"><span data-stu-id="100ea-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="100ea-258">Sélectionnez **TodoContext (TodoApi. Models )** dans la **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="100ea-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="100ea-259">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="100ea-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="100ea-260">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="100ea-261">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="100ea-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="100ea-262">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="100ea-262">The preceding commands:</span></span>

* <span data-ttu-id="100ea-263">Ajoutent les packages NuGet nécessaires à la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="100ea-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="100ea-264">Installent le moteur de génération de modèles automatique (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="100ea-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="100ea-265">Génèrent automatiquement des modèles pour `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="100ea-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="100ea-266">Le code généré :</span><span class="sxs-lookup"><span data-stu-id="100ea-266">The generated code:</span></span>

* <span data-ttu-id="100ea-267">Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="100ea-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="100ea-268">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="100ea-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="100ea-269">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="100ea-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="100ea-270">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="100ea-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="100ea-271">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="100ea-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="100ea-272">Les modèles de ASP.NET Core pour :</span><span class="sxs-lookup"><span data-stu-id="100ea-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="100ea-273">Les contrôleurs avec des vues sont inclus `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="100ea-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="100ea-274">Les contrôleurs d’API n’incluent pas `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="100ea-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="100ea-275">Lorsque le `[action]` jeton n’est pas dans le modèle de routage, le nom de l' [action](xref:mvc/controllers/routing#action) est exclu de l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="100ea-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="100ea-276">Autrement dit, le nom de la méthode associée à l’action n’est pas utilisé dans l’itinéraire correspondant.</span><span class="sxs-lookup"><span data-stu-id="100ea-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="100ea-277">Mettre à jour la méthode de création de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="100ea-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="100ea-278">Remplacez l’instruction return dans `PostTodoItem` pour utiliser l’opérateur [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="100ea-278">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="100ea-279">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="100ea-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="100ea-280">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="100ea-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="100ea-281">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="100ea-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="100ea-282">La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> :</span><span class="sxs-lookup"><span data-stu-id="100ea-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="100ea-283">Retourne un [code d’état HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="100ea-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="100ea-284">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="100ea-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="100ea-285">Ajoute un en-tête [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à la réponse.</span><span class="sxs-lookup"><span data-stu-id="100ea-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="100ea-286">L’en-tête `Location` spécifie l’[URI](https://developer.mozilla.org/docs/Glossary/URI) de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="100ea-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="100ea-287">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="100ea-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="100ea-288">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="100ea-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="100ea-289">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="100ea-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="100ea-290">Installer Postman</span><span class="sxs-lookup"><span data-stu-id="100ea-290">Install Postman</span></span>

<span data-ttu-id="100ea-291">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="100ea-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="100ea-292">Installer le [poste](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="100ea-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="100ea-293">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="100ea-293">Start the web app.</span></span>
* <span data-ttu-id="100ea-294">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="100ea-294">Start Postman.</span></span>
* <span data-ttu-id="100ea-295">Désactivez la **vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="100ea-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="100ea-296">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="100ea-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="100ea-297">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="100ea-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="100ea-298">Tester PostTodoItem avec Postman</span><span class="sxs-lookup"><span data-stu-id="100ea-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="100ea-299">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="100ea-299">Create a new request.</span></span>
* <span data-ttu-id="100ea-300">Affectez `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="100ea-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="100ea-301">Définissez l’URI sur `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="100ea-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="100ea-302">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="100ea-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="100ea-303">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="100ea-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="100ea-304">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="100ea-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="100ea-305">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="100ea-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="100ea-306">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="100ea-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="100ea-307">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-307">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="100ea-309">Tester l’URI de l’en-tête d’emplacement</span><span class="sxs-lookup"><span data-stu-id="100ea-309">Test the location header URI</span></span>

<span data-ttu-id="100ea-310">L’URI d’en-tête d’emplacement peut être testé dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="100ea-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="100ea-311">Copiez et collez l’URI d’en-tête d’emplacement dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="100ea-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="100ea-312">Pour effectuer un test dans le poster :</span><span class="sxs-lookup"><span data-stu-id="100ea-312">To test in Postman:</span></span>

* <span data-ttu-id="100ea-313">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="100ea-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="100ea-314">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="100ea-314">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="100ea-316">Affectez `GET` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="100ea-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="100ea-317">Définissez l’URI sur `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="100ea-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="100ea-318">Par exemple : `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="100ea-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="100ea-319">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="100ea-320">Examiner les méthodes GET</span><span class="sxs-lookup"><span data-stu-id="100ea-320">Examine the GET methods</span></span>

<span data-ttu-id="100ea-321">Deux points de terminaison d’extraction sont implémentés :</span><span class="sxs-lookup"><span data-stu-id="100ea-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="100ea-322">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur ou de Postman.</span><span class="sxs-lookup"><span data-stu-id="100ea-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="100ea-323">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="100ea-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="100ea-324">Une réponse semblable à la suivante est produite par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="100ea-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="100ea-325">Tester Get avec Postman</span><span class="sxs-lookup"><span data-stu-id="100ea-325">Test Get with Postman</span></span>

* <span data-ttu-id="100ea-326">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="100ea-326">Create a new request.</span></span>
* <span data-ttu-id="100ea-327">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="100ea-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="100ea-328">Définissez l’URI de la demande sur `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="100ea-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="100ea-329">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="100ea-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="100ea-330">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="100ea-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="100ea-331">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-331">Select **Send**.</span></span>

<span data-ttu-id="100ea-332">Cette application utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="100ea-332">This app uses an in-memory database.</span></span> <span data-ttu-id="100ea-333">Si l’application est arrêtée et démarrée, la requête GET précédente ne retourne aucune donnée.</span><span class="sxs-lookup"><span data-stu-id="100ea-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="100ea-334">Si aucune donnée n’est retournée, publiez ([POST](#post)) les données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="100ea-335">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="100ea-335">Routing and URL paths</span></span>

<span data-ttu-id="100ea-336">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="100ea-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="100ea-337">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="100ea-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="100ea-338">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="100ea-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="100ea-339">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="100ea-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="100ea-340">Pour cet exemple, le nom de la classe du contrôleur étant **TodoItems** Controller, le nom du contrôleur est « TodoItems ».</span><span class="sxs-lookup"><span data-stu-id="100ea-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="100ea-341">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="100ea-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="100ea-342">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="100ea-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="100ea-343">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="100ea-343">This sample doesn't use a template.</span></span> <span data-ttu-id="100ea-344">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="100ea-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="100ea-345">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="100ea-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="100ea-346">Lorsque `GetTodoItem` est appelé, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son `id` paramètre.</span><span class="sxs-lookup"><span data-stu-id="100ea-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="100ea-347">Valeurs retournées</span><span class="sxs-lookup"><span data-stu-id="100ea-347">Return values</span></span>

<span data-ttu-id="100ea-348">Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="100ea-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="100ea-349">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="100ea-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="100ea-350">Le code de réponse pour ce type de retour est [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), en supposant qu’il n’existe aucune exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="100ea-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="100ea-351">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="100ea-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="100ea-352">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="100ea-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="100ea-353">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="100ea-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="100ea-354">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un code d’erreur d' [état 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .</span><span class="sxs-lookup"><span data-stu-id="100ea-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="100ea-355">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="100ea-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="100ea-356">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="100ea-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="100ea-357">Méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="100ea-357">The PutTodoItem method</span></span>

<span data-ttu-id="100ea-358">Examinez la méthode `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="100ea-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="100ea-359">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="100ea-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="100ea-360">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="100ea-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="100ea-361">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="100ea-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="100ea-362">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="100ea-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="100ea-363">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="100ea-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="100ea-364">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="100ea-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="100ea-365">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="100ea-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="100ea-366">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="100ea-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="100ea-367">Appelez obtenir pour vous assurer qu’il y a un élément dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="100ea-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="100ea-368">Mettez à jour l’élément de tâche dont l’ID est égal à 1 et affectez-lui comme nom `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="100ea-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="100ea-369">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="100ea-369">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="100ea-371">Méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="100ea-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="100ea-372">Examinez la méthode `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="100ea-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="100ea-373">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="100ea-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="100ea-374">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="100ea-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="100ea-375">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="100ea-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="100ea-376">Définissez l’URI de l’objet à supprimer (par exemple `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="100ea-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="100ea-377">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="100ea-378">Empêcher la post-validation</span><span class="sxs-lookup"><span data-stu-id="100ea-378">Prevent over-posting</span></span>

<span data-ttu-id="100ea-379">Actuellement, l’exemple d’application expose l' `TodoItem` objet entier.</span><span class="sxs-lookup"><span data-stu-id="100ea-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="100ea-380">En général, les applications de production limitent les données entrées et retournées à l’aide d’un sous-ensemble du modèle.</span><span class="sxs-lookup"><span data-stu-id="100ea-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="100ea-381">Il existe plusieurs raisons à cela, et la sécurité est essentielle.</span><span class="sxs-lookup"><span data-stu-id="100ea-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="100ea-382">Le sous-ensemble d’un modèle est généralement appelé un objet Transfert de données (DTO), un modèle d’entrée ou un modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="100ea-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="100ea-383">Le **DTO** est utilisé dans cet article.</span><span class="sxs-lookup"><span data-stu-id="100ea-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="100ea-384">Un DTO peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="100ea-384">A DTO may be used to:</span></span>

* <span data-ttu-id="100ea-385">Empêcher la survalidation.</span><span class="sxs-lookup"><span data-stu-id="100ea-385">Prevent over-posting.</span></span>
* <span data-ttu-id="100ea-386">Masquer les propriétés que les clients ne sont pas censés afficher.</span><span class="sxs-lookup"><span data-stu-id="100ea-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="100ea-387">Omettez certaines propriétés afin de réduire la taille de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="100ea-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="100ea-388">Aplatir les graphiques d’objets qui contiennent des objets imbriqués.</span><span class="sxs-lookup"><span data-stu-id="100ea-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="100ea-389">Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.</span><span class="sxs-lookup"><span data-stu-id="100ea-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="100ea-390">Pour illustrer l’approche DTO, mettez à jour la `TodoItem` classe pour inclure un champ secret :</span><span class="sxs-lookup"><span data-stu-id="100ea-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="100ea-391">Le champ secret doit être masqué à partir de cette application, mais une application administrative peut choisir de l’exposer.</span><span class="sxs-lookup"><span data-stu-id="100ea-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="100ea-392">Vérifiez que vous pouvez poster et recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="100ea-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="100ea-393">Créer un modèle DTO :</span><span class="sxs-lookup"><span data-stu-id="100ea-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="100ea-394">Mettez à jour le `TodoItemsController` pour utiliser `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="100ea-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="100ea-395">Vérifiez que vous ne pouvez pas poster ou recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="100ea-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="100ea-396">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="100ea-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="100ea-397">Consultez [Didacticiel : appeler une API web ASP.net core avec JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="100ea-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="100ea-398">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="100ea-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="100ea-399">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="100ea-399">Create a web API project.</span></span>
> * <span data-ttu-id="100ea-400">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="100ea-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="100ea-401">Générer automatiquement des modèles pour un contrôleur avec des méthodes CRUD.</span><span class="sxs-lookup"><span data-stu-id="100ea-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="100ea-402">Configurer le routage, les chemins d’URL et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="100ea-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="100ea-403">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="100ea-403">Call the web API with Postman.</span></span>

<span data-ttu-id="100ea-404">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="100ea-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="100ea-405">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="100ea-405">Overview</span></span>

<span data-ttu-id="100ea-406">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="100ea-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="100ea-407">API</span><span class="sxs-lookup"><span data-stu-id="100ea-407">API</span></span> | <span data-ttu-id="100ea-408">Description</span><span class="sxs-lookup"><span data-stu-id="100ea-408">Description</span></span> | <span data-ttu-id="100ea-409">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="100ea-409">Request body</span></span> | <span data-ttu-id="100ea-410">Response body</span><span class="sxs-lookup"><span data-stu-id="100ea-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="100ea-411">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="100ea-411">Get all to-do items</span></span> | <span data-ttu-id="100ea-412">None</span><span class="sxs-lookup"><span data-stu-id="100ea-412">None</span></span> | <span data-ttu-id="100ea-413">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="100ea-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="100ea-414">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="100ea-414">Get an item by ID</span></span> | <span data-ttu-id="100ea-415">None</span><span class="sxs-lookup"><span data-stu-id="100ea-415">None</span></span> | <span data-ttu-id="100ea-416">Tâche</span><span class="sxs-lookup"><span data-stu-id="100ea-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="100ea-417">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="100ea-417">Add a new item</span></span> | <span data-ttu-id="100ea-418">Tâche</span><span class="sxs-lookup"><span data-stu-id="100ea-418">To-do item</span></span> | <span data-ttu-id="100ea-419">Tâche</span><span class="sxs-lookup"><span data-stu-id="100ea-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="100ea-420">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="100ea-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="100ea-421">Tâche</span><span class="sxs-lookup"><span data-stu-id="100ea-421">To-do item</span></span> | <span data-ttu-id="100ea-422">None</span><span class="sxs-lookup"><span data-stu-id="100ea-422">None</span></span> |
|<span data-ttu-id="100ea-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="100ea-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="100ea-424">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="100ea-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="100ea-425">None</span><span class="sxs-lookup"><span data-stu-id="100ea-425">None</span></span> | <span data-ttu-id="100ea-426">None</span><span class="sxs-lookup"><span data-stu-id="100ea-426">None</span></span>|

<span data-ttu-id="100ea-427">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-427">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="100ea-433">Prérequis</span><span class="sxs-lookup"><span data-stu-id="100ea-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="100ea-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="100ea-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="100ea-436">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="100ea-437">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="100ea-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="100ea-439">Dans le menu **fichier** , sélectionnez **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="100ea-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="100ea-440">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="100ea-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="100ea-441">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="100ea-442">Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 3,1** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="100ea-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="100ea-443">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-443">Select the **API** template and click **Create**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="100ea-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="100ea-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="100ea-446">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="100ea-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="100ea-447">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="100ea-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="100ea-448">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="100ea-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="100ea-449">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="100ea-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="100ea-450">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="100ea-450">The preceding commands:</span></span>

  * <span data-ttu-id="100ea-451">Créent un projet API Web et l’ouvrent dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="100ea-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="100ea-452">Ajoutent les packages NuGet qui sont requis dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="100ea-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="100ea-453">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="100ea-454">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="100ea-454">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="100ea-456">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >    >    >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="100ea-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="100ea-457">Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >    >  **API** application console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="100ea-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![sélection du modèle d’API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="100ea-459">Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 3. x **Target Framework**.</span><span class="sxs-lookup"><span data-stu-id="100ea-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="100ea-460">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="100ea-460">Select **Next**.</span></span>

* <span data-ttu-id="100ea-461">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="100ea-463">Ouvrez un terminal de commande dans le dossier de projet, puis exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="100ea-463">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="100ea-464">Tester l’API</span><span class="sxs-lookup"><span data-stu-id="100ea-464">Test the API</span></span>

<span data-ttu-id="100ea-465">Le modèle de projet crée une API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="100ea-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="100ea-466">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="100ea-468">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="100ea-469">Visual Studio lance un navigateur et accède à `https://localhost:<port>/WeatherForecast`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="100ea-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="100ea-470">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="100ea-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="100ea-471">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="100ea-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="100ea-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="100ea-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="100ea-473">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="100ea-474">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="100ea-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="100ea-475">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="100ea-476">Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="100ea-477">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="100ea-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="100ea-478">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="100ea-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="100ea-479">Ajoutez `/WeatherForecast` à l’URL (définissez-la sur `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="100ea-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="100ea-480">Un code JSON similaire au suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="100ea-480">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="100ea-481">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="100ea-481">Add a model class</span></span>

<span data-ttu-id="100ea-482">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="100ea-483">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="100ea-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="100ea-485">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="100ea-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="100ea-486">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="100ea-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="100ea-487">Nommez le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="100ea-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="100ea-488">Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="100ea-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="100ea-489">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="100ea-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="100ea-490">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="100ea-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="100ea-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="100ea-492">Ajoutez un dossier nommé *Models* .</span><span class="sxs-lookup"><span data-stu-id="100ea-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="100ea-493">Ajoutez une `TodoItem` classe au dossier à l' *Models* aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="100ea-494">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="100ea-495">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="100ea-495">Right-click the project.</span></span> <span data-ttu-id="100ea-496">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="100ea-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="100ea-497">Nommez le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="100ea-497">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="100ea-499">Cliquez avec le bouton droit sur le *Models* dossier, puis sélectionnez **Ajouter** > **un nouveau fichier** >  > **classe générale vide**.</span><span class="sxs-lookup"><span data-stu-id="100ea-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="100ea-500">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="100ea-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="100ea-501">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="100ea-502">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="100ea-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="100ea-503">Les classes de modèle peuvent se trouver n’importe où dans le projet, mais le *Models* dossier est utilisé par Convention.</span><span class="sxs-lookup"><span data-stu-id="100ea-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="100ea-504">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="100ea-504">Add a database context</span></span>

<span data-ttu-id="100ea-505">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="100ea-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="100ea-506">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="100ea-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="100ea-508">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="100ea-508">Add NuGet packages</span></span>

* <span data-ttu-id="100ea-509">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution**.</span><span class="sxs-lookup"><span data-stu-id="100ea-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="100ea-510">Sélectionnez l’onglet **Parcourir**, puis entrez **Microsoft.EntityFrameworkCore.SqlServer** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="100ea-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="100ea-511">Dans le volet gauche, sélectionnez **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="100ea-511">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="100ea-512">Cochez la case **Projet** dans le volet droit, puis sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="100ea-513">Utilisez les instructions précédentes pour ajouter le package NuGet **Microsoft. EntityFrameworkCore. InMemory** .</span><span class="sxs-lookup"><span data-stu-id="100ea-513">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Gestionnaire de package NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="100ea-515">Ajouter le contexte de base de données TodoContext</span><span class="sxs-lookup"><span data-stu-id="100ea-515">Add the TodoContext database context</span></span>

* <span data-ttu-id="100ea-516">Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="100ea-516">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="100ea-517">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="100ea-517">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="100ea-518">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-518">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="100ea-519">Ajoutez une `TodoContext` classe au *Models* dossier.</span><span class="sxs-lookup"><span data-stu-id="100ea-519">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="100ea-520">Entrez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-520">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="100ea-521">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="100ea-521">Register the database context</span></span>

<span data-ttu-id="100ea-522">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="100ea-522">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="100ea-523">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="100ea-523">The container provides the service to controllers.</span></span>

<span data-ttu-id="100ea-524">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-524">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="100ea-525">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="100ea-525">The preceding code:</span></span>

* <span data-ttu-id="100ea-526">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="100ea-526">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="100ea-527">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="100ea-527">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="100ea-528">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="100ea-528">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="100ea-529">Générer automatiquement des modèles pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="100ea-529">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-530">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-530">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="100ea-531">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="100ea-531">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="100ea-532">Sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="100ea-532">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="100ea-533">Sélectionnez **Contrôleur d’API avec actions, utilisant Entity Framework**, puis **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="100ea-533">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="100ea-534">Dans la boîte de dialogue **Contrôleur d’API avec actions, utilisant Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="100ea-534">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="100ea-535">Sélectionnez **TodoItem (TodoApi. Models )** dans la **classe de modèle**.</span><span class="sxs-lookup"><span data-stu-id="100ea-535">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="100ea-536">Sélectionnez **TodoContext (TodoApi. Models )** dans la **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="100ea-536">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="100ea-537">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="100ea-537">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="100ea-538">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-538">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="100ea-539">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="100ea-539">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="100ea-540">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="100ea-540">The preceding commands:</span></span>

* <span data-ttu-id="100ea-541">Ajoutent les packages NuGet nécessaires à la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="100ea-541">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="100ea-542">Installent le moteur de génération de modèles automatique (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="100ea-542">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="100ea-543">Génèrent automatiquement des modèles pour `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="100ea-543">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="100ea-544">Le code généré :</span><span class="sxs-lookup"><span data-stu-id="100ea-544">The generated code:</span></span>

* <span data-ttu-id="100ea-545">Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="100ea-545">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="100ea-546">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="100ea-546">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="100ea-547">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="100ea-547">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="100ea-548">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="100ea-548">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="100ea-549">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="100ea-549">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="100ea-550">Les modèles de ASP.NET Core pour :</span><span class="sxs-lookup"><span data-stu-id="100ea-550">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="100ea-551">Les contrôleurs avec des vues sont inclus `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="100ea-551">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="100ea-552">Les contrôleurs d’API n’incluent pas `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="100ea-552">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="100ea-553">Lorsque le `[action]` jeton n’est pas dans le modèle de routage, le nom de l' [action](xref:mvc/controllers/routing#action) est exclu de l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="100ea-553">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="100ea-554">Autrement dit, le nom de la méthode associée à l’action n’est pas utilisé dans l’itinéraire correspondant.</span><span class="sxs-lookup"><span data-stu-id="100ea-554">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="100ea-555">Examiner la méthode de création de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="100ea-555">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="100ea-556">Remplacez l’instruction return dans `PostTodoItem` pour utiliser l’opérateur [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="100ea-556">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="100ea-557">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="100ea-557">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="100ea-558">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="100ea-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="100ea-559">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="100ea-559">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="100ea-560">La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> :</span><span class="sxs-lookup"><span data-stu-id="100ea-560">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="100ea-561">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="100ea-561">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="100ea-562">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="100ea-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="100ea-563">Ajoute un en-tête [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à la réponse.</span><span class="sxs-lookup"><span data-stu-id="100ea-563">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="100ea-564">L’en-tête `Location` spécifie l’[URI](https://developer.mozilla.org/docs/Glossary/URI) de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="100ea-564">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="100ea-565">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="100ea-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="100ea-566">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="100ea-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="100ea-567">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="100ea-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="100ea-568">Installer Postman</span><span class="sxs-lookup"><span data-stu-id="100ea-568">Install Postman</span></span>

<span data-ttu-id="100ea-569">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="100ea-569">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="100ea-570">Installer le [poste](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="100ea-570">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="100ea-571">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="100ea-571">Start the web app.</span></span>
* <span data-ttu-id="100ea-572">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="100ea-572">Start Postman.</span></span>
* <span data-ttu-id="100ea-573">Désactivez la **vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="100ea-573">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="100ea-574">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="100ea-574">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="100ea-575">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="100ea-575">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="100ea-576">Tester PostTodoItem avec Postman</span><span class="sxs-lookup"><span data-stu-id="100ea-576">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="100ea-577">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="100ea-577">Create a new request.</span></span>
* <span data-ttu-id="100ea-578">Affectez `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="100ea-578">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="100ea-579">Définissez l’URI sur `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="100ea-579">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="100ea-580">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="100ea-580">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="100ea-581">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="100ea-581">Select the **Body** tab.</span></span>
* <span data-ttu-id="100ea-582">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="100ea-582">Select the **raw** radio button.</span></span>
* <span data-ttu-id="100ea-583">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="100ea-583">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="100ea-584">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="100ea-584">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="100ea-585">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-585">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="100ea-587">Tester l’URI d’en-tête d’emplacement avec le poster</span><span class="sxs-lookup"><span data-stu-id="100ea-587">Test the location header URI with Postman</span></span>

* <span data-ttu-id="100ea-588">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="100ea-588">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="100ea-589">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="100ea-589">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="100ea-591">Affectez `GET` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="100ea-591">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="100ea-592">Définissez l’URI sur `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="100ea-592">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="100ea-593">Par exemple : `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="100ea-593">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="100ea-594">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-594">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="100ea-595">Examiner les méthodes GET</span><span class="sxs-lookup"><span data-stu-id="100ea-595">Examine the GET methods</span></span>

<span data-ttu-id="100ea-596">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="100ea-596">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="100ea-597">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur ou de Postman.</span><span class="sxs-lookup"><span data-stu-id="100ea-597">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="100ea-598">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="100ea-598">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="100ea-599">Une réponse semblable à la suivante est produite par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="100ea-599">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="100ea-600">Tester Get avec Postman</span><span class="sxs-lookup"><span data-stu-id="100ea-600">Test Get with Postman</span></span>

* <span data-ttu-id="100ea-601">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="100ea-601">Create a new request.</span></span>
* <span data-ttu-id="100ea-602">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="100ea-602">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="100ea-603">Définissez l’URI de la demande sur `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="100ea-603">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="100ea-604">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="100ea-604">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="100ea-605">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="100ea-605">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="100ea-606">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-606">Select **Send**.</span></span>

<span data-ttu-id="100ea-607">Cette application utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="100ea-607">This app uses an in-memory database.</span></span> <span data-ttu-id="100ea-608">Si l’application est arrêtée et démarrée, la requête GET précédente ne retourne aucune donnée.</span><span class="sxs-lookup"><span data-stu-id="100ea-608">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="100ea-609">Si aucune donnée n’est retournée, publiez ([POST](#post)) les données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-609">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="100ea-610">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="100ea-610">Routing and URL paths</span></span>

<span data-ttu-id="100ea-611">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="100ea-611">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="100ea-612">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="100ea-612">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="100ea-613">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="100ea-613">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="100ea-614">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="100ea-614">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="100ea-615">Pour cet exemple, le nom de la classe du contrôleur étant **TodoItems** Controller, le nom du contrôleur est « TodoItems ».</span><span class="sxs-lookup"><span data-stu-id="100ea-615">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="100ea-616">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="100ea-616">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="100ea-617">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="100ea-617">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="100ea-618">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="100ea-618">This sample doesn't use a template.</span></span> <span data-ttu-id="100ea-619">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="100ea-619">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="100ea-620">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="100ea-620">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="100ea-621">Lorsque `GetTodoItem` est appelé, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son `id` paramètre.</span><span class="sxs-lookup"><span data-stu-id="100ea-621">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="100ea-622">Valeurs retournées</span><span class="sxs-lookup"><span data-stu-id="100ea-622">Return values</span></span> 

<span data-ttu-id="100ea-623">Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="100ea-623">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="100ea-624">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="100ea-624">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="100ea-625">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="100ea-625">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="100ea-626">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="100ea-626">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="100ea-627">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="100ea-627">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="100ea-628">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="100ea-628">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="100ea-629">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> code d’erreur 404.</span><span class="sxs-lookup"><span data-stu-id="100ea-629">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="100ea-630">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="100ea-630">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="100ea-631">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="100ea-631">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="100ea-632">Méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="100ea-632">The PutTodoItem method</span></span>

<span data-ttu-id="100ea-633">Examinez la méthode `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="100ea-633">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="100ea-634">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="100ea-634">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="100ea-635">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="100ea-635">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="100ea-636">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="100ea-636">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="100ea-637">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="100ea-637">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="100ea-638">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="100ea-638">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="100ea-639">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="100ea-639">Test the PutTodoItem method</span></span>

<span data-ttu-id="100ea-640">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="100ea-640">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="100ea-641">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="100ea-641">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="100ea-642">Appelez obtenir pour vous assurer qu’il y a un élément dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="100ea-642">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="100ea-643">Mettez à jour l’élément de tâche dont l’ID est égal à 1 et affectez-lui le nom « flux de poisson » :</span><span class="sxs-lookup"><span data-stu-id="100ea-643">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="100ea-644">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="100ea-644">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="100ea-646">Méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="100ea-646">The DeleteTodoItem method</span></span>

<span data-ttu-id="100ea-647">Examinez la méthode `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="100ea-647">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="100ea-648">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="100ea-648">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="100ea-649">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="100ea-649">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="100ea-650">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="100ea-650">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="100ea-651">Définissez l’URI de l’objet à supprimer (par exemple `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="100ea-651">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="100ea-652">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-652">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="100ea-653">Empêcher la post-validation</span><span class="sxs-lookup"><span data-stu-id="100ea-653">Prevent over-posting</span></span>

<span data-ttu-id="100ea-654">Actuellement, l’exemple d’application expose l' `TodoItem` objet entier.</span><span class="sxs-lookup"><span data-stu-id="100ea-654">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="100ea-655">En général, les applications de production limitent les données entrées et retournées à l’aide d’un sous-ensemble du modèle.</span><span class="sxs-lookup"><span data-stu-id="100ea-655">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="100ea-656">Il existe plusieurs raisons à cela, et la sécurité est essentielle.</span><span class="sxs-lookup"><span data-stu-id="100ea-656">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="100ea-657">Le sous-ensemble d’un modèle est généralement appelé un objet Transfert de données (DTO), un modèle d’entrée ou un modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="100ea-657">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="100ea-658">Le **DTO** est utilisé dans cet article.</span><span class="sxs-lookup"><span data-stu-id="100ea-658">**DTO** is used in this article.</span></span>

<span data-ttu-id="100ea-659">Un DTO peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="100ea-659">A DTO may be used to:</span></span>

* <span data-ttu-id="100ea-660">Empêcher la survalidation.</span><span class="sxs-lookup"><span data-stu-id="100ea-660">Prevent over-posting.</span></span>
* <span data-ttu-id="100ea-661">Masquer les propriétés que les clients ne sont pas censés afficher.</span><span class="sxs-lookup"><span data-stu-id="100ea-661">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="100ea-662">Omettez certaines propriétés afin de réduire la taille de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="100ea-662">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="100ea-663">Aplatir les graphiques d’objets qui contiennent des objets imbriqués.</span><span class="sxs-lookup"><span data-stu-id="100ea-663">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="100ea-664">Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.</span><span class="sxs-lookup"><span data-stu-id="100ea-664">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="100ea-665">Pour illustrer l’approche DTO, mettez à jour la `TodoItem` classe pour inclure un champ secret :</span><span class="sxs-lookup"><span data-stu-id="100ea-665">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="100ea-666">Le champ secret doit être masqué à partir de cette application, mais une application administrative peut choisir de l’exposer.</span><span class="sxs-lookup"><span data-stu-id="100ea-666">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="100ea-667">Vérifiez que vous pouvez poster et recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="100ea-667">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="100ea-668">Créer un modèle DTO :</span><span class="sxs-lookup"><span data-stu-id="100ea-668">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="100ea-669">Mettez à jour le `TodoItemsController` pour utiliser `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="100ea-669">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="100ea-670">Vérifiez que vous ne pouvez pas poster ou recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="100ea-670">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="100ea-671">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="100ea-671">Call the web API with JavaScript</span></span>

<span data-ttu-id="100ea-672">Consultez [Didacticiel : appeler une API web ASP.net core avec JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="100ea-672">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="100ea-673">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="100ea-673">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="100ea-674">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="100ea-674">Create a web API project.</span></span>
> * <span data-ttu-id="100ea-675">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="100ea-675">Add a model class and a database context.</span></span>
> * <span data-ttu-id="100ea-676">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="100ea-676">Add a controller.</span></span>
> * <span data-ttu-id="100ea-677">Ajouter les méthodes CRUD</span><span class="sxs-lookup"><span data-stu-id="100ea-677">Add CRUD methods.</span></span>
> * <span data-ttu-id="100ea-678">Configurer le routage et les chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="100ea-678">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="100ea-679">Spécifier des valeurs de retour</span><span class="sxs-lookup"><span data-stu-id="100ea-679">Specify return values.</span></span>
> * <span data-ttu-id="100ea-680">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="100ea-680">Call the web API with Postman.</span></span>
> * <span data-ttu-id="100ea-681">Appeler l’API web avec JavaScript.</span><span class="sxs-lookup"><span data-stu-id="100ea-681">Call the web API with JavaScript.</span></span>

<span data-ttu-id="100ea-682">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="100ea-682">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="100ea-683">Vue d’ensemble 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-683">Overview 2.1</span></span>

<span data-ttu-id="100ea-684">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="100ea-684">This tutorial creates the following API:</span></span>

|<span data-ttu-id="100ea-685">API</span><span class="sxs-lookup"><span data-stu-id="100ea-685">API</span></span> | <span data-ttu-id="100ea-686">Description</span><span class="sxs-lookup"><span data-stu-id="100ea-686">Description</span></span> | <span data-ttu-id="100ea-687">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="100ea-687">Request body</span></span> | <span data-ttu-id="100ea-688">Response body</span><span class="sxs-lookup"><span data-stu-id="100ea-688">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="100ea-689">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="100ea-689">GET /api/TodoItems</span></span> | <span data-ttu-id="100ea-690">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="100ea-690">Get all to-do items</span></span> | <span data-ttu-id="100ea-691">None</span><span class="sxs-lookup"><span data-stu-id="100ea-691">None</span></span> | <span data-ttu-id="100ea-692">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="100ea-692">Array of to-do items</span></span>|
|<span data-ttu-id="100ea-693">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="100ea-693">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="100ea-694">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="100ea-694">Get an item by ID</span></span> | <span data-ttu-id="100ea-695">None</span><span class="sxs-lookup"><span data-stu-id="100ea-695">None</span></span> | <span data-ttu-id="100ea-696">Tâche</span><span class="sxs-lookup"><span data-stu-id="100ea-696">To-do item</span></span>|
|<span data-ttu-id="100ea-697">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="100ea-697">POST /api/TodoItems</span></span> | <span data-ttu-id="100ea-698">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="100ea-698">Add a new item</span></span> | <span data-ttu-id="100ea-699">Tâche</span><span class="sxs-lookup"><span data-stu-id="100ea-699">To-do item</span></span> | <span data-ttu-id="100ea-700">Tâche</span><span class="sxs-lookup"><span data-stu-id="100ea-700">To-do item</span></span> |
|<span data-ttu-id="100ea-701">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="100ea-701">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="100ea-702">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="100ea-702">Update an existing item &nbsp;</span></span> | <span data-ttu-id="100ea-703">Tâche</span><span class="sxs-lookup"><span data-stu-id="100ea-703">To-do item</span></span> | <span data-ttu-id="100ea-704">None</span><span class="sxs-lookup"><span data-stu-id="100ea-704">None</span></span> |
|<span data-ttu-id="100ea-705">SUPPRIMER/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="100ea-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="100ea-706">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="100ea-706">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="100ea-707">None</span><span class="sxs-lookup"><span data-stu-id="100ea-707">None</span></span> | <span data-ttu-id="100ea-708">None</span><span class="sxs-lookup"><span data-stu-id="100ea-708">None</span></span>|

<span data-ttu-id="100ea-709">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-709">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="100ea-715">Conditions préalables 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-715">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-716">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-716">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="100ea-717">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="100ea-717">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="100ea-718">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-718">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="100ea-719">Créer un projet Web 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-719">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-720">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-720">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="100ea-721">Dans le menu **fichier** , sélectionnez **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="100ea-721">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="100ea-722">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="100ea-722">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="100ea-723">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-723">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="100ea-724">Dans la boîte de dialogue **Créer une application web ASP.NET Core**, vérifiez que **.NET Core** et **ASP.NET Core 2.2** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="100ea-724">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="100ea-725">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-725">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="100ea-726">Ne sélectionnez **pas\*\*\*\*Activer la prise en charge de Docker**.</span><span class="sxs-lookup"><span data-stu-id="100ea-726">**Don't** select **Enable Docker Support**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="100ea-728">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="100ea-728">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="100ea-729">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="100ea-729">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="100ea-730">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="100ea-730">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="100ea-731">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="100ea-731">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="100ea-732">Ces commandes créent un projet d’API web et ouvrent une nouvelle instance de Visual Studio Code dans le nouveau dossier de projet.</span><span class="sxs-lookup"><span data-stu-id="100ea-732">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="100ea-733">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="100ea-733">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="100ea-734">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-734">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="100ea-735">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="100ea-735">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="100ea-737">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >    >    >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="100ea-737">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="100ea-738">Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >    >  **API** application console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="100ea-738">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="100ea-739">Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 2. x **Target Framework**.</span><span class="sxs-lookup"><span data-stu-id="100ea-739">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="100ea-740">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="100ea-740">Select **Next**.</span></span>

* <span data-ttu-id="100ea-741">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-741">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="100ea-743">Tester l’API 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-743">Test the API 2.1</span></span>

<span data-ttu-id="100ea-744">Le modèle de projet crée une API `values`.</span><span class="sxs-lookup"><span data-stu-id="100ea-744">The project template creates a `values` API.</span></span> <span data-ttu-id="100ea-745">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-745">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-746">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-746">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="100ea-747">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-747">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="100ea-748">Visual Studio lance un navigateur et accède à `https://localhost:<port>/api/values`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="100ea-748">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="100ea-749">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="100ea-749">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="100ea-750">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="100ea-750">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="100ea-751">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="100ea-751">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="100ea-752">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-752">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="100ea-753">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="100ea-753">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="100ea-754">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-754">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="100ea-755">Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-755">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="100ea-756">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="100ea-756">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="100ea-757">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="100ea-757">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="100ea-758">Ajoutez `/api/values` à l’URL (définissez-la sur `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="100ea-758">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="100ea-759">Le code JSON suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="100ea-759">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="100ea-760">Ajouter une classe de modèle 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-760">Add a model class 2.1</span></span>

<span data-ttu-id="100ea-761">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="100ea-761">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="100ea-762">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="100ea-762">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-763">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-763">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="100ea-764">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="100ea-764">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="100ea-765">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="100ea-765">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="100ea-766">Nommez le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="100ea-766">Name the folder *Models*.</span></span>

* <span data-ttu-id="100ea-767">Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="100ea-767">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="100ea-768">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="100ea-768">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="100ea-769">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-769">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="100ea-770">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="100ea-770">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="100ea-771">Ajoutez un dossier nommé *Models* .</span><span class="sxs-lookup"><span data-stu-id="100ea-771">Add a folder named *Models*.</span></span>

* <span data-ttu-id="100ea-772">Ajoutez une `TodoItem` classe au dossier à l' *Models* aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-772">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="100ea-773">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-773">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="100ea-774">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="100ea-774">Right-click the project.</span></span> <span data-ttu-id="100ea-775">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="100ea-775">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="100ea-776">Nommez le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="100ea-776">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="100ea-778">Cliquez avec le bouton droit sur le *Models* dossier, puis sélectionnez **Ajouter** > **un nouveau fichier** >  > **classe générale vide**.</span><span class="sxs-lookup"><span data-stu-id="100ea-778">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="100ea-779">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="100ea-779">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="100ea-780">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-780">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="100ea-781">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="100ea-781">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="100ea-782">Les classes de modèle peuvent se trouver n’importe où dans le projet, mais le *Models* dossier est utilisé par Convention.</span><span class="sxs-lookup"><span data-stu-id="100ea-782">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="100ea-783">Ajouter un contexte de base de données 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-783">Add a database context 2.1</span></span>

<span data-ttu-id="100ea-784">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="100ea-784">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="100ea-785">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="100ea-785">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-786">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-786">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="100ea-787">Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="100ea-787">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="100ea-788">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="100ea-788">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="100ea-789">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-789">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="100ea-790">Ajoutez une `TodoContext` classe au *Models* dossier.</span><span class="sxs-lookup"><span data-stu-id="100ea-790">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="100ea-791">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-791">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="100ea-792">Inscrire le contexte de base de données 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-792">Register the database context 2.1</span></span>

<span data-ttu-id="100ea-793">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="100ea-793">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="100ea-794">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="100ea-794">The container provides the service to controllers.</span></span>

<span data-ttu-id="100ea-795">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-795">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="100ea-796">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="100ea-796">The preceding code:</span></span>

* <span data-ttu-id="100ea-797">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="100ea-797">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="100ea-798">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="100ea-798">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="100ea-799">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="100ea-799">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="100ea-800">Ajouter un contrôleur 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-800">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-801">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-801">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="100ea-802">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="100ea-802">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="100ea-803">Sélectionnez **Ajouter** > **un nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="100ea-803">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="100ea-804">Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez le modèle **Classe de contrôleur d’API**.</span><span class="sxs-lookup"><span data-stu-id="100ea-804">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="100ea-805">Nommez la classe *TodoController* et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="100ea-805">Name the class *TodoController*, and select **Add**.</span></span>

  ![Boîte de dialogue Ajouter un nouvel élément avec contrôleur dans la zone de recherche et le contrôleur des API web sélectionné](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="100ea-807">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-807">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="100ea-808">Dans le dossier *Contrôleurs*, créez une classe nommée `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="100ea-808">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="100ea-809">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-809">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="100ea-810">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="100ea-810">The preceding code:</span></span>

* <span data-ttu-id="100ea-811">Définit une classe de contrôleur d’API sans méthodes.</span><span class="sxs-lookup"><span data-stu-id="100ea-811">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="100ea-812">Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="100ea-812">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="100ea-813">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="100ea-813">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="100ea-814">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="100ea-814">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="100ea-815">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="100ea-815">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="100ea-816">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="100ea-816">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="100ea-817">Ajoute un élément nommé `Item1` à la base de données si celle-ci est vide.</span><span class="sxs-lookup"><span data-stu-id="100ea-817">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="100ea-818">Ce code se trouvant dans le constructeur, il s’exécute chaque fois qu’une nouvelle requête HTTP existe.</span><span class="sxs-lookup"><span data-stu-id="100ea-818">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="100ea-819">Si vous supprimez tous les éléments, le constructeur recrée `Item1` au prochain appel d’une méthode d’API.</span><span class="sxs-lookup"><span data-stu-id="100ea-819">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="100ea-820">Ainsi, il peut vous sembler à tort que la suppression n’a pas fonctionné.</span><span class="sxs-lookup"><span data-stu-id="100ea-820">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="100ea-821">Ajouter des méthodes de récupération 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-821">Add Get methods 2.1</span></span>

<span data-ttu-id="100ea-822">Pour fournir une API qui récupère les tâches, ajoutez les méthodes suivantes à la classe `TodoController` :</span><span class="sxs-lookup"><span data-stu-id="100ea-822">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="100ea-823">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="100ea-823">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="100ea-824">Arrêtez l’application si elle est toujours en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="100ea-824">Stop the app if it's still running.</span></span> <span data-ttu-id="100ea-825">Ensuite, réexécutez-la pour inclure les dernières modifications.</span><span class="sxs-lookup"><span data-stu-id="100ea-825">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="100ea-826">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur.</span><span class="sxs-lookup"><span data-stu-id="100ea-826">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="100ea-827">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="100ea-827">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="100ea-828">La réponse HTTP suivante est générée par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="100ea-828">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="100ea-829">Routage et chemins d’URL 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-829">Routing and URL paths 2.1</span></span>

<span data-ttu-id="100ea-830">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="100ea-830">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="100ea-831">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="100ea-831">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="100ea-832">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="100ea-832">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="100ea-833">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="100ea-833">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="100ea-834">Pour cet exemple, le nom de classe du contrôleur étant **Todo** Controller, le nom du contrôleur est « todo ».</span><span class="sxs-lookup"><span data-stu-id="100ea-834">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="100ea-835">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="100ea-835">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="100ea-836">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="100ea-836">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="100ea-837">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="100ea-837">This sample doesn't use a template.</span></span> <span data-ttu-id="100ea-838">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="100ea-838">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="100ea-839">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="100ea-839">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="100ea-840">Quand `GetTodoItem` est appelée, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son paramètre `id`.</span><span class="sxs-lookup"><span data-stu-id="100ea-840">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="100ea-841">Valeurs de retour 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-841">Return values 2.1</span></span>

<span data-ttu-id="100ea-842">Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="100ea-842">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="100ea-843">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="100ea-843">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="100ea-844">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="100ea-844">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="100ea-845">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="100ea-845">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="100ea-846">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="100ea-846">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="100ea-847">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="100ea-847">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="100ea-848">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> code d’erreur 404.</span><span class="sxs-lookup"><span data-stu-id="100ea-848">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="100ea-849">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="100ea-849">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="100ea-850">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="100ea-850">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="100ea-851">Tester la méthode GetTodoItems 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-851">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="100ea-852">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="100ea-852">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="100ea-853">Installer [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="100ea-853">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="100ea-854">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="100ea-854">Start the web app.</span></span>
* <span data-ttu-id="100ea-855">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="100ea-855">Start Postman.</span></span>
* <span data-ttu-id="100ea-856">Désactivez la **vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="100ea-856">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="100ea-857">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="100ea-857">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="100ea-858">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="100ea-858">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="100ea-859">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="100ea-859">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="100ea-860">Dans   >  **Préférences** de publication (onglet **général** ), désactivez la vérification de **certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="100ea-860">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="100ea-861">Vous pouvez également sélectionner la clé et sélectionner **Paramètres**, puis désactiver la vérification du certificat SSL.</span><span class="sxs-lookup"><span data-stu-id="100ea-861">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="100ea-862">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="100ea-862">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="100ea-863">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="100ea-863">Create a new request.</span></span>
  * <span data-ttu-id="100ea-864">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="100ea-864">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="100ea-865">Définissez l’URI de la demande sur `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="100ea-865">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="100ea-866">Par exemple : `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="100ea-866">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="100ea-867">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="100ea-867">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="100ea-868">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-868">Select **Send**.</span></span>

![Postman avec requête Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="100ea-870">Ajouter une méthode de création 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-870">Add a Create method 2.1</span></span>

<span data-ttu-id="100ea-871">Ajoutez la méthode `PostTodoItem` suivante à *Controllers/TodoController.cs* :</span><span class="sxs-lookup"><span data-stu-id="100ea-871">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="100ea-872">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="100ea-872">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="100ea-873">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="100ea-873">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="100ea-874">La méthode `CreatedAtAction` :</span><span class="sxs-lookup"><span data-stu-id="100ea-874">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="100ea-875">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="100ea-875">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="100ea-876">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="100ea-876">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="100ea-877">Ajoute un en-tête `Location` à la réponse.</span><span class="sxs-lookup"><span data-stu-id="100ea-877">Adds a `Location` header to the response.</span></span> <span data-ttu-id="100ea-878">L’en-tête `Location` spécifie l’URI de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="100ea-878">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="100ea-879">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="100ea-879">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="100ea-880">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="100ea-880">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="100ea-881">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="100ea-881">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="100ea-882">Tester la méthode PostTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-882">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="100ea-883">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="100ea-883">Build the project.</span></span>
* <span data-ttu-id="100ea-884">Dans Postman, définissez la méthode HTTP sur `POST`.</span><span class="sxs-lookup"><span data-stu-id="100ea-884">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="100ea-885">Définissez l’URI sur `https://localhost:<port>/api/Todo` .</span><span class="sxs-lookup"><span data-stu-id="100ea-885">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="100ea-886">Par exemple : `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="100ea-886">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="100ea-887">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="100ea-887">Select the **Body** tab.</span></span>
* <span data-ttu-id="100ea-888">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="100ea-888">Select the **raw** radio button.</span></span>
* <span data-ttu-id="100ea-889">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="100ea-889">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="100ea-890">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="100ea-890">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="100ea-891">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-891">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/create.png)

  <span data-ttu-id="100ea-893">Si vous obtenez une erreur 405 Méthode non autorisée, il est probable que le projet n’ait pas été compilé après l’ajout de la méthode `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="100ea-893">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="100ea-894">Tester l’URI d’en-tête d’emplacement 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-894">Test the location header URI 2.1</span></span>

* <span data-ttu-id="100ea-895">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="100ea-895">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="100ea-896">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="100ea-896">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="100ea-898">Définissez la méthode sur GET.</span><span class="sxs-lookup"><span data-stu-id="100ea-898">Set the method to GET.</span></span>
* <span data-ttu-id="100ea-899">Définissez l’URI sur `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="100ea-899">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="100ea-900">Par exemple : `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="100ea-900">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="100ea-901">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-901">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="100ea-902">Ajouter une méthode PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-902">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="100ea-903">Ajoutez la méthode `PutTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="100ea-903">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="100ea-904">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="100ea-904">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="100ea-905">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="100ea-905">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="100ea-906">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="100ea-906">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="100ea-907">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="100ea-907">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="100ea-908">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="100ea-908">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="100ea-909">Tester la méthode PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-909">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="100ea-910">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="100ea-910">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="100ea-911">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="100ea-911">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="100ea-912">Appelez obtenir pour vous assurer qu’il y a un élément dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="100ea-912">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="100ea-913">Mettez à jour l’élément de tâche dont l’ID est égal à 1 et affectez-lui le nom « flux de poisson » :</span><span class="sxs-lookup"><span data-stu-id="100ea-913">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="100ea-914">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="100ea-914">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="100ea-916">Ajouter une méthode DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-916">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="100ea-917">Ajoutez la méthode `DeleteTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="100ea-917">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="100ea-918">La réponse `DeleteTodoItem` est [204 (Pas de contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="100ea-918">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="100ea-919">Tester la méthode DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-919">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="100ea-920">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="100ea-920">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="100ea-921">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="100ea-921">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="100ea-922">Définissez l’URI de l’objet à supprimer (par exemple, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="100ea-922">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="100ea-923">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="100ea-923">Select **Send**.</span></span>

<span data-ttu-id="100ea-924">L’exemple d’application vous permet de supprimer tous les éléments.</span><span class="sxs-lookup"><span data-stu-id="100ea-924">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="100ea-925">Toutefois, quand le dernier élément est supprimé, un autre est créé par le constructeur de classe de modèle au prochain appel de l’API.</span><span class="sxs-lookup"><span data-stu-id="100ea-925">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="100ea-926">Appeler l’API Web avec JavaScript 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-926">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="100ea-927">Dans cette section, une page HTML qui utilise JavaScript pour appeler l’API web est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="100ea-927">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="100ea-928">jQuery lance la demande.</span><span class="sxs-lookup"><span data-stu-id="100ea-928">jQuery initiates the request.</span></span> <span data-ttu-id="100ea-929">JavaScript met à jour la page avec les détails de la réponse de l’API Web.</span><span class="sxs-lookup"><span data-stu-id="100ea-929">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="100ea-930">Configurez l’application pour [traiter les fichiers statiques](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) et [activer le mappage de fichier par défaut](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) en mettant à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-930">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="100ea-931">Créez un dossier *wwwroot* dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="100ea-931">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="100ea-932">Ajoutez un fichier HTML nommé *index.html* au répertoire *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="100ea-932">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="100ea-933">Remplacez son contenu par le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-933">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="100ea-934">Ajoutez un fichier JavaScript nommé *site.js* au répertoire *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="100ea-934">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="100ea-935">Remplacez le contenu par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="100ea-935">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="100ea-936">Vous devrez peut-être changer les paramètres de lancement du projet ASP.NET Core pour tester la page HTML localement :</span><span class="sxs-lookup"><span data-stu-id="100ea-936">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="100ea-937">Ouvrez *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="100ea-937">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="100ea-938">Supprimez la `launchUrl` propriété pour forcer l’ouverture de l’application à *index.html* &mdash; fichier par défaut du projet.</span><span class="sxs-lookup"><span data-stu-id="100ea-938">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="100ea-939">Cet exemple appelle toutes les méthodes CRUD de l’API web.</span><span class="sxs-lookup"><span data-stu-id="100ea-939">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="100ea-940">Les explications suivantes traitent des appels à l’API.</span><span class="sxs-lookup"><span data-stu-id="100ea-940">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="100ea-941">Obtenir la liste des éléments de tâche 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-941">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="100ea-942">jQuery envoie une requête HTTP obtenir à l’API Web, qui retourne JSON représentant un tableau d’éléments de tâche.</span><span class="sxs-lookup"><span data-stu-id="100ea-942">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="100ea-943">La fonction de rappel `success` est appelée si la requête réussit.</span><span class="sxs-lookup"><span data-stu-id="100ea-943">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="100ea-944">Dans le rappel, le DOM est mis à jour avec les informations des tâches.</span><span class="sxs-lookup"><span data-stu-id="100ea-944">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="100ea-945">Ajouter un élément de tâche 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-945">Add a to-do item 2.1</span></span>

<span data-ttu-id="100ea-946">jQuery envoie une requête HTTP POSTÉRIEURe avec l’élément de tâche dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="100ea-946">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="100ea-947">Les options `accepts` et `contentType` sont définies avec la valeur `application/json` pour spécifier le type de média qui est reçu et envoyé.</span><span class="sxs-lookup"><span data-stu-id="100ea-947">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="100ea-948">La tâche est convertie au format JSON à l’aide de [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="100ea-948">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="100ea-949">Quand l’API retourne un code d’état de réussite, la fonction `getData` est appelée pour mettre à jour la table HTML.</span><span class="sxs-lookup"><span data-stu-id="100ea-949">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="100ea-950">Mettre à jour un élément de tâche 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-950">Update a to-do item 2.1</span></span>

<span data-ttu-id="100ea-951">La mise à jour d’une tâche est similaire à l’ajout d’une tâche.</span><span class="sxs-lookup"><span data-stu-id="100ea-951">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="100ea-952">L’identificateur unique de la tâche est ajouté à l’`url` et le `type` est `PUT`.</span><span class="sxs-lookup"><span data-stu-id="100ea-952">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="100ea-953">Supprimer un élément de tâche 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-953">Delete a to-do item 2.1</span></span>

<span data-ttu-id="100ea-954">Pour supprimer une tâche, vous devez définir le `type` sur l’appel AJAX avec la valeur `DELETE` et spécifier l’identificateur unique de l’élément dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="100ea-954">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="100ea-955">Ajouter la prise en charge de l’authentification à une API Web 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-955">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="100ea-956">Ressources supplémentaires 2,1</span><span class="sxs-lookup"><span data-stu-id="100ea-956">Additional resources 2.1</span></span>

<span data-ttu-id="100ea-957">[Affichez ou téléchargez l’exemple de code de ce tutoriel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="100ea-957">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="100ea-958">Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="100ea-958">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="100ea-959">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="100ea-959">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="100ea-960">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="100ea-960">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
