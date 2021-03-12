---
title: 'Tutoriel : Création d’une API web avec ASP.NET Core'
author: rick-anderson
description: Apprendre à créer une API web avec ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 02/04/2021
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
ms.openlocfilehash: 789cd1a867bc8c17401bbac5c02951b4bd2999b6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587656"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="f225a-103">Tutoriel : Création d’une API web avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f225a-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="f225a-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)et [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="f225a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="f225a-105">Ce tutoriel décrit les principes fondamentaux liés à la génération d’une API web avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f225a-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f225a-106">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="f225a-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f225a-107">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="f225a-107">Create a web API project.</span></span>
> * <span data-ttu-id="f225a-108">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="f225a-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="f225a-109">Générer automatiquement des modèles pour un contrôleur avec des méthodes CRUD.</span><span class="sxs-lookup"><span data-stu-id="f225a-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="f225a-110">Configurer le routage, les chemins d’URL et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="f225a-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="f225a-111">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="f225a-111">Call the web API with Postman.</span></span>

<span data-ttu-id="f225a-112">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="f225a-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="f225a-113">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="f225a-113">Overview</span></span>

<span data-ttu-id="f225a-114">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="f225a-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="f225a-115">API</span><span class="sxs-lookup"><span data-stu-id="f225a-115">API</span></span> | <span data-ttu-id="f225a-116">Description</span><span class="sxs-lookup"><span data-stu-id="f225a-116">Description</span></span> | <span data-ttu-id="f225a-117">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="f225a-117">Request body</span></span> | <span data-ttu-id="f225a-118">Response body</span><span class="sxs-lookup"><span data-stu-id="f225a-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="f225a-119">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="f225a-119">Get all to-do items</span></span> | <span data-ttu-id="f225a-120">Aucun</span><span class="sxs-lookup"><span data-stu-id="f225a-120">None</span></span> | <span data-ttu-id="f225a-121">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="f225a-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="f225a-122">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="f225a-122">Get an item by ID</span></span> | <span data-ttu-id="f225a-123">Aucun</span><span class="sxs-lookup"><span data-stu-id="f225a-123">None</span></span> | <span data-ttu-id="f225a-124">Tâche</span><span class="sxs-lookup"><span data-stu-id="f225a-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="f225a-125">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="f225a-125">Add a new item</span></span> | <span data-ttu-id="f225a-126">Tâche</span><span class="sxs-lookup"><span data-stu-id="f225a-126">To-do item</span></span> | <span data-ttu-id="f225a-127">Tâche</span><span class="sxs-lookup"><span data-stu-id="f225a-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="f225a-128">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f225a-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="f225a-129">Tâche</span><span class="sxs-lookup"><span data-stu-id="f225a-129">To-do item</span></span> | <span data-ttu-id="f225a-130">Aucun</span><span class="sxs-lookup"><span data-stu-id="f225a-130">None</span></span> |
|<span data-ttu-id="f225a-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f225a-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="f225a-132">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="f225a-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="f225a-133">None</span><span class="sxs-lookup"><span data-stu-id="f225a-133">None</span></span> | <span data-ttu-id="f225a-134">None</span><span class="sxs-lookup"><span data-stu-id="f225a-134">None</span></span>|

<span data-ttu-id="f225a-135">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-135">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="f225a-141">Prérequis</span><span class="sxs-lookup"><span data-stu-id="f225a-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f225a-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f225a-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f225a-144">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="f225a-145">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="f225a-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f225a-147">Dans le menu **fichier** , sélectionnez **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="f225a-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f225a-148">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f225a-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="f225a-149">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="f225a-150">Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 5,0** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="f225a-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="f225a-151">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-151">Select the **API** template and click **Create**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f225a-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f225a-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f225a-154">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f225a-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f225a-155">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="f225a-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="f225a-156">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f225a-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="f225a-157">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f225a-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="f225a-158">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="f225a-158">The preceding commands:</span></span>

  * <span data-ttu-id="f225a-159">Créent un projet API Web et l’ouvrent dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f225a-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="f225a-160">Ajoutent les packages NuGet qui sont requis dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="f225a-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f225a-161">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f225a-162">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="f225a-162">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="f225a-164">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >    >    >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f225a-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="f225a-165">Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >    >  **API** application console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f225a-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![sélection du modèle d’API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="f225a-167">Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 5. x **Target Framework**.</span><span class="sxs-lookup"><span data-stu-id="f225a-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="f225a-168">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f225a-168">Select **Next**.</span></span>

* <span data-ttu-id="f225a-169">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="f225a-171">Ouvrez un terminal de commande dans le dossier du projet et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f225a-171">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="f225a-172">Tester le projet</span><span class="sxs-lookup"><span data-stu-id="f225a-172">Test the project</span></span>

<span data-ttu-id="f225a-173">Le modèle de projet crée une `WeatherForecast` API avec prise en charge de [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="f225a-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f225a-175">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="f225a-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="f225a-176">Lancements de Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="f225a-176">Visual Studio launches:</span></span>

* <span data-ttu-id="f225a-177">Serveur Web IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f225a-177">The IIS Express web server.</span></span>
* <span data-ttu-id="f225a-178">Le navigateur par défaut et navigue vers `https://localhost:<port>/swagger/index.html` , où `<port>` est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="f225a-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f225a-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f225a-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="f225a-180">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f225a-181">Dans un navigateur, accédez à l’URL suivante : [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="f225a-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f225a-182">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f225a-183">Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="f225a-184">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="f225a-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="f225a-185">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="f225a-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="f225a-186">Ajoutez `/swagger` à l’URL (définissez-la sur `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="f225a-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="f225a-187">La page Swagger `/swagger/index.html` s’affiche.</span><span class="sxs-lookup"><span data-stu-id="f225a-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="f225a-188">Sélectionnez **faire**  >  **essayer** l'  >  **exécution**.</span><span class="sxs-lookup"><span data-stu-id="f225a-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="f225a-189">La page affiche :</span><span class="sxs-lookup"><span data-stu-id="f225a-189">The page displays:</span></span>

* <span data-ttu-id="f225a-190">Commande de [boucle](https://curl.haxx.se/) pour tester l’API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="f225a-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="f225a-191">URL pour tester l’API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="f225a-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="f225a-192">Code, corps et en-têtes de la réponse.</span><span class="sxs-lookup"><span data-stu-id="f225a-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="f225a-193">Zone de liste déroulante avec les types de média et l’exemple de valeur et de schéma.</span><span class="sxs-lookup"><span data-stu-id="f225a-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="f225a-194">Swagger est utilisé pour générer une documentation utile et des pages d’aide pour les API Web.</span><span class="sxs-lookup"><span data-stu-id="f225a-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="f225a-195">Ce didacticiel se concentre sur la création d’une API Web.</span><span class="sxs-lookup"><span data-stu-id="f225a-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="f225a-196">Pour plus d’informations sur Swagger, consultez <xref:tutorials/web-api-help-pages-using-swagger> .</span><span class="sxs-lookup"><span data-stu-id="f225a-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="f225a-197">Copiez et collez l’URL de la **requête** dans le navigateur :  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="f225a-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="f225a-198">Un code JSON similaire au suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="f225a-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="f225a-199">Mettre à jour launchUrl</span><span class="sxs-lookup"><span data-stu-id="f225a-199">Update the launchUrl</span></span>

<span data-ttu-id="f225a-200">Dans *Properties\launchSettings.js*, mettez à jour `launchUrl` de `"swagger"` vers `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="f225a-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="f225a-201">Comme Swagger a été supprimé, le balisage précédent modifie l’URL qui est lancée vers la méthode d’extraction du contrôleur ajoutée dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="f225a-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="f225a-202">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="f225a-202">Add a model class</span></span>

<span data-ttu-id="f225a-203">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="f225a-204">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="f225a-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f225a-206">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="f225a-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="f225a-207">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f225a-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f225a-208">Nommez le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="f225a-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="f225a-209">Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="f225a-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f225a-210">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f225a-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="f225a-211">Remplacez le code du modèle par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="f225a-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f225a-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f225a-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f225a-213">Ajoutez un dossier nommé *Models* .</span><span class="sxs-lookup"><span data-stu-id="f225a-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="f225a-214">Ajoutez une `TodoItem` classe au dossier à l' *Models* aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f225a-215">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f225a-216">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="f225a-216">Right-click the project.</span></span> <span data-ttu-id="f225a-217">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f225a-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f225a-218">Nommez le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="f225a-218">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="f225a-220">Cliquez avec le bouton droit sur le *Models* dossier, puis sélectionnez **Ajouter** > **un nouveau fichier** >  > **classe générale vide**.</span><span class="sxs-lookup"><span data-stu-id="f225a-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="f225a-221">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="f225a-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="f225a-222">Remplacez le code du modèle par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="f225a-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="f225a-223">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="f225a-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="f225a-224">Les classes de modèle peuvent se trouver n’importe où dans le projet, mais le *Models* dossier est utilisé par Convention.</span><span class="sxs-lookup"><span data-stu-id="f225a-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="f225a-225">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="f225a-225">Add a database context</span></span>

<span data-ttu-id="f225a-226">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="f225a-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="f225a-227">Cette classe est créée en dérivant de la classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="f225a-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="f225a-229">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="f225a-229">Add NuGet packages</span></span>

* <span data-ttu-id="f225a-230">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution**.</span><span class="sxs-lookup"><span data-stu-id="f225a-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="f225a-231">Sélectionnez l’onglet **Parcourir** , puis entrez `Microsoft.EntityFrameworkCore.InMemory` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f225a-231">Select the **Browse** tab, and then enter `Microsoft.EntityFrameworkCore.InMemory` in the search box.</span></span>
* <span data-ttu-id="f225a-232">Sélectionnez `Microsoft.EntityFrameworkCore.InMemory` dans le volet gauche.</span><span class="sxs-lookup"><span data-stu-id="f225a-232">Select `Microsoft.EntityFrameworkCore.InMemory` in the left pane.</span></span>
* <span data-ttu-id="f225a-233">Cochez la case **Projet** dans le volet droit, puis sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![Gestionnaire de package NuGet](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="f225a-235">Ajouter le contexte de base de données TodoContext</span><span class="sxs-lookup"><span data-stu-id="f225a-235">Add the TodoContext database context</span></span>

* <span data-ttu-id="f225a-236">Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="f225a-236">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f225a-237">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f225a-237">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f225a-238">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f225a-239">Ajoutez une `TodoContext` classe au *Models* dossier.</span><span class="sxs-lookup"><span data-stu-id="f225a-239">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="f225a-240">Entrez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-240">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="f225a-241">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="f225a-241">Register the database context</span></span>

<span data-ttu-id="f225a-242">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f225a-242">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f225a-243">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="f225a-243">The container provides the service to controllers.</span></span>

<span data-ttu-id="f225a-244">Mettez à jour *Startup.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-244">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="f225a-245">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="f225a-245">The preceding code:</span></span>

* <span data-ttu-id="f225a-246">Supprime les appels Swagger.</span><span class="sxs-lookup"><span data-stu-id="f225a-246">Removes the Swagger calls.</span></span>
* <span data-ttu-id="f225a-247">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="f225a-247">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="f225a-248">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="f225a-248">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="f225a-249">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="f225a-249">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="f225a-250">Générer automatiquement des modèles pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="f225a-250">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f225a-252">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="f225a-252">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="f225a-253">Sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="f225a-253">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f225a-254">Sélectionnez **Contrôleur d’API avec actions, utilisant Entity Framework**, puis **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f225a-254">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="f225a-255">Dans la boîte de dialogue **Contrôleur d’API avec actions, utilisant Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="f225a-255">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="f225a-256">Sélectionnez **TodoItem (TodoApi. Models )** dans la **classe de modèle**.</span><span class="sxs-lookup"><span data-stu-id="f225a-256">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="f225a-257">Sélectionnez **TodoContext (TodoApi. Models )** dans la **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="f225a-257">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="f225a-258">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f225a-258">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f225a-259">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-259">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f225a-260">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f225a-260">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="f225a-261">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="f225a-261">The preceding commands:</span></span>

* <span data-ttu-id="f225a-262">Ajoutent les packages NuGet nécessaires à la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="f225a-262">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="f225a-263">Installent le moteur de génération de modèles automatique (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="f225a-263">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="f225a-264">Génèrent automatiquement des modèles pour `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="f225a-264">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="f225a-265">Le code généré :</span><span class="sxs-lookup"><span data-stu-id="f225a-265">The generated code:</span></span>

* <span data-ttu-id="f225a-266">Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="f225a-266">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="f225a-267">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="f225a-267">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="f225a-268">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="f225a-268">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="f225a-269">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f225a-269">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="f225a-270">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f225a-270">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="f225a-271">Les modèles de ASP.NET Core pour :</span><span class="sxs-lookup"><span data-stu-id="f225a-271">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="f225a-272">Les contrôleurs avec des vues sont inclus `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="f225a-272">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="f225a-273">Les contrôleurs d’API n’incluent pas `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="f225a-273">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="f225a-274">Lorsque le `[action]` jeton n’est pas dans le modèle de routage, le nom de l' [action](xref:mvc/controllers/routing#action) est exclu de l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="f225a-274">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="f225a-275">Autrement dit, le nom de la méthode associée à l’action n’est pas utilisé dans l’itinéraire correspondant.</span><span class="sxs-lookup"><span data-stu-id="f225a-275">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="f225a-276">Mettre à jour la méthode de création de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="f225a-276">Update the PostTodoItem create method</span></span>

<span data-ttu-id="f225a-277">Mettez à jour l’instruction return dans la `PostTodoItem` pour utiliser l’opérateur [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="f225a-277">Update the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="f225a-278">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="f225a-278">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="f225a-279">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="f225a-279">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="f225a-280">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f225a-280">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f225a-281">La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> :</span><span class="sxs-lookup"><span data-stu-id="f225a-281">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="f225a-282">Retourne un [code d’état HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="f225a-282">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="f225a-283">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="f225a-283">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="f225a-284">Ajoute un en-tête [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à la réponse.</span><span class="sxs-lookup"><span data-stu-id="f225a-284">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="f225a-285">L’en-tête `Location` spécifie l’[URI](https://developer.mozilla.org/docs/Glossary/URI) de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="f225a-285">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="f225a-286">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f225a-286">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="f225a-287">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="f225a-287">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="f225a-288">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="f225a-288">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="f225a-289">Installer Postman</span><span class="sxs-lookup"><span data-stu-id="f225a-289">Install Postman</span></span>

<span data-ttu-id="f225a-290">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="f225a-290">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="f225a-291">Installer le [poste](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="f225a-291">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="f225a-292">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="f225a-292">Start the web app.</span></span>
* <span data-ttu-id="f225a-293">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="f225a-293">Start Postman.</span></span>
* <span data-ttu-id="f225a-294">Désactivez la **vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="f225a-294">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="f225a-295">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="f225a-295">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="f225a-296">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f225a-296">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="f225a-297">Tester PostTodoItem avec Postman</span><span class="sxs-lookup"><span data-stu-id="f225a-297">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="f225a-298">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="f225a-298">Create a new request.</span></span>
* <span data-ttu-id="f225a-299">Affectez `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="f225a-299">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="f225a-300">Définissez l’URI sur `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="f225a-300">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="f225a-301">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="f225a-301">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="f225a-302">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="f225a-302">Select the **Body** tab.</span></span>
* <span data-ttu-id="f225a-303">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="f225a-303">Select the **raw** radio button.</span></span>
* <span data-ttu-id="f225a-304">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="f225a-304">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="f225a-305">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="f225a-305">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="f225a-306">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-306">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="f225a-308">Tester l’URI de l’en-tête d’emplacement</span><span class="sxs-lookup"><span data-stu-id="f225a-308">Test the location header URI</span></span>

<span data-ttu-id="f225a-309">L’URI d’en-tête d’emplacement peut être testé dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="f225a-309">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="f225a-310">Copiez et collez l’URI d’en-tête d’emplacement dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="f225a-310">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="f225a-311">Pour effectuer un test dans le poster :</span><span class="sxs-lookup"><span data-stu-id="f225a-311">To test in Postman:</span></span>

* <span data-ttu-id="f225a-312">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="f225a-312">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="f225a-313">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="f225a-313">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="f225a-315">Affectez `GET` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="f225a-315">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="f225a-316">Définissez l’URI sur `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="f225a-316">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="f225a-317">Par exemple : `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="f225a-317">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="f225a-318">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-318">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="f225a-319">Examiner les méthodes GET</span><span class="sxs-lookup"><span data-stu-id="f225a-319">Examine the GET methods</span></span>

<span data-ttu-id="f225a-320">Deux points de terminaison d’extraction sont implémentés :</span><span class="sxs-lookup"><span data-stu-id="f225a-320">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="f225a-321">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur ou de Postman.</span><span class="sxs-lookup"><span data-stu-id="f225a-321">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="f225a-322">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f225a-322">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="f225a-323">Une réponse semblable à la suivante est produite par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="f225a-323">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="f225a-324">Tester Get avec Postman</span><span class="sxs-lookup"><span data-stu-id="f225a-324">Test Get with Postman</span></span>

* <span data-ttu-id="f225a-325">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="f225a-325">Create a new request.</span></span>
* <span data-ttu-id="f225a-326">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="f225a-326">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="f225a-327">Définissez l’URI de la demande sur `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="f225a-327">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="f225a-328">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="f225a-328">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="f225a-329">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="f225a-329">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="f225a-330">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-330">Select **Send**.</span></span>

<span data-ttu-id="f225a-331">Cette application utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="f225a-331">This app uses an in-memory database.</span></span> <span data-ttu-id="f225a-332">Si l’application est arrêtée et démarrée, la requête GET précédente ne retourne aucune donnée.</span><span class="sxs-lookup"><span data-stu-id="f225a-332">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="f225a-333">Si aucune donnée n’est retournée, publiez ([POST](#post)) les données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-333">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="f225a-334">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="f225a-334">Routing and URL paths</span></span>

<span data-ttu-id="f225a-335">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="f225a-335">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="f225a-336">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="f225a-336">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="f225a-337">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="f225a-337">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="f225a-338">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="f225a-338">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="f225a-339">Pour cet exemple, le nom de la classe du contrôleur étant **TodoItems** Controller, le nom du contrôleur est « TodoItems ».</span><span class="sxs-lookup"><span data-stu-id="f225a-339">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="f225a-340">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="f225a-340">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="f225a-341">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="f225a-341">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="f225a-342">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="f225a-342">This sample doesn't use a template.</span></span> <span data-ttu-id="f225a-343">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f225a-343">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f225a-344">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="f225a-344">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="f225a-345">Lorsque `GetTodoItem` est appelé, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son `id` paramètre.</span><span class="sxs-lookup"><span data-stu-id="f225a-345">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="f225a-346">Valeurs de retour</span><span class="sxs-lookup"><span data-stu-id="f225a-346">Return values</span></span>

<span data-ttu-id="f225a-347">Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="f225a-347">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="f225a-348">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="f225a-348">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="f225a-349">Le code de réponse pour ce type de retour est [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), en supposant qu’il n’existe aucune exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="f225a-349">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="f225a-350">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="f225a-350">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="f225a-351">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="f225a-351">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="f225a-352">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="f225a-352">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="f225a-353">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un code d’erreur d' [état 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .</span><span class="sxs-lookup"><span data-stu-id="f225a-353">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="f225a-354">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="f225a-354">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="f225a-355">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="f225a-355">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="f225a-356">Méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="f225a-356">The PutTodoItem method</span></span>

<span data-ttu-id="f225a-357">Examinez la méthode `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="f225a-357">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="f225a-358">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="f225a-358">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="f225a-359">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f225a-359">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="f225a-360">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="f225a-360">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="f225a-361">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="f225a-361">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="f225a-362">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="f225a-362">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="f225a-363">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="f225a-363">Test the PutTodoItem method</span></span>

<span data-ttu-id="f225a-364">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="f225a-364">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="f225a-365">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="f225a-365">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="f225a-366">Appelez obtenir pour vous assurer qu’il y a un élément dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="f225a-366">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="f225a-367">Mettez à jour l’élément de tâche dont l’ID est égal à 1 et affectez-lui comme nom `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="f225a-367">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="f225a-368">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="f225a-368">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="f225a-370">Méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="f225a-370">The DeleteTodoItem method</span></span>

<span data-ttu-id="f225a-371">Examinez la méthode `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="f225a-371">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="f225a-372">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="f225a-372">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="f225a-373">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="f225a-373">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="f225a-374">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="f225a-374">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="f225a-375">Définissez l’URI de l’objet à supprimer (par exemple `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="f225a-375">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="f225a-376">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-376">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="f225a-377">Empêcher la post-validation</span><span class="sxs-lookup"><span data-stu-id="f225a-377">Prevent over-posting</span></span>

<span data-ttu-id="f225a-378">Actuellement, l’exemple d’application expose l' `TodoItem` objet entier.</span><span class="sxs-lookup"><span data-stu-id="f225a-378">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="f225a-379">En général, les applications de production limitent les données entrées et retournées à l’aide d’un sous-ensemble du modèle.</span><span class="sxs-lookup"><span data-stu-id="f225a-379">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="f225a-380">Il existe plusieurs raisons à cela, et la sécurité est essentielle.</span><span class="sxs-lookup"><span data-stu-id="f225a-380">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="f225a-381">Le sous-ensemble d’un modèle est généralement appelé un objet Transfert de données (DTO), un modèle d’entrée ou un modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="f225a-381">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="f225a-382">Le **DTO** est utilisé dans cet article.</span><span class="sxs-lookup"><span data-stu-id="f225a-382">**DTO** is used in this article.</span></span>

<span data-ttu-id="f225a-383">Un DTO peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="f225a-383">A DTO may be used to:</span></span>

* <span data-ttu-id="f225a-384">Empêcher la survalidation.</span><span class="sxs-lookup"><span data-stu-id="f225a-384">Prevent over-posting.</span></span>
* <span data-ttu-id="f225a-385">Masquer les propriétés que les clients ne sont pas censés afficher.</span><span class="sxs-lookup"><span data-stu-id="f225a-385">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="f225a-386">Omettez certaines propriétés afin de réduire la taille de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="f225a-386">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="f225a-387">Aplatir les graphiques d’objets qui contiennent des objets imbriqués.</span><span class="sxs-lookup"><span data-stu-id="f225a-387">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="f225a-388">Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.</span><span class="sxs-lookup"><span data-stu-id="f225a-388">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="f225a-389">Pour illustrer l’approche DTO, mettez à jour la `TodoItem` classe pour inclure un champ secret :</span><span class="sxs-lookup"><span data-stu-id="f225a-389">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="f225a-390">Le champ secret doit être masqué à partir de cette application, mais une application administrative peut choisir de l’exposer.</span><span class="sxs-lookup"><span data-stu-id="f225a-390">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="f225a-391">Vérifiez que vous pouvez poster et recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="f225a-391">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="f225a-392">Créer un modèle DTO :</span><span class="sxs-lookup"><span data-stu-id="f225a-392">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="f225a-393">Mettez à jour le `TodoItemsController` pour utiliser `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="f225a-393">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="f225a-394">Vérifiez que vous ne pouvez pas poster ou recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="f225a-394">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="f225a-395">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="f225a-395">Call the web API with JavaScript</span></span>

<span data-ttu-id="f225a-396">Consultez [Didacticiel : appeler une API web ASP.net core avec JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="f225a-396">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="f225a-397">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="f225a-397">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f225a-398">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="f225a-398">Create a web API project.</span></span>
> * <span data-ttu-id="f225a-399">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="f225a-399">Add a model class and a database context.</span></span>
> * <span data-ttu-id="f225a-400">Générer automatiquement des modèles pour un contrôleur avec des méthodes CRUD.</span><span class="sxs-lookup"><span data-stu-id="f225a-400">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="f225a-401">Configurer le routage, les chemins d’URL et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="f225a-401">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="f225a-402">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="f225a-402">Call the web API with Postman.</span></span>

<span data-ttu-id="f225a-403">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="f225a-403">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="f225a-404">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="f225a-404">Overview</span></span>

<span data-ttu-id="f225a-405">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="f225a-405">This tutorial creates the following API:</span></span>

|<span data-ttu-id="f225a-406">API</span><span class="sxs-lookup"><span data-stu-id="f225a-406">API</span></span> | <span data-ttu-id="f225a-407">Description</span><span class="sxs-lookup"><span data-stu-id="f225a-407">Description</span></span> | <span data-ttu-id="f225a-408">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="f225a-408">Request body</span></span> | <span data-ttu-id="f225a-409">Response body</span><span class="sxs-lookup"><span data-stu-id="f225a-409">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="f225a-410">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="f225a-410">Get all to-do items</span></span> | <span data-ttu-id="f225a-411">Aucun</span><span class="sxs-lookup"><span data-stu-id="f225a-411">None</span></span> | <span data-ttu-id="f225a-412">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="f225a-412">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="f225a-413">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="f225a-413">Get an item by ID</span></span> | <span data-ttu-id="f225a-414">Aucun</span><span class="sxs-lookup"><span data-stu-id="f225a-414">None</span></span> | <span data-ttu-id="f225a-415">Tâche</span><span class="sxs-lookup"><span data-stu-id="f225a-415">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="f225a-416">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="f225a-416">Add a new item</span></span> | <span data-ttu-id="f225a-417">Tâche</span><span class="sxs-lookup"><span data-stu-id="f225a-417">To-do item</span></span> | <span data-ttu-id="f225a-418">Tâche</span><span class="sxs-lookup"><span data-stu-id="f225a-418">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="f225a-419">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f225a-419">Update an existing item &nbsp;</span></span> | <span data-ttu-id="f225a-420">Tâche</span><span class="sxs-lookup"><span data-stu-id="f225a-420">To-do item</span></span> | <span data-ttu-id="f225a-421">Aucun</span><span class="sxs-lookup"><span data-stu-id="f225a-421">None</span></span> |
|<span data-ttu-id="f225a-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f225a-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="f225a-423">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="f225a-423">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="f225a-424">None</span><span class="sxs-lookup"><span data-stu-id="f225a-424">None</span></span> | <span data-ttu-id="f225a-425">None</span><span class="sxs-lookup"><span data-stu-id="f225a-425">None</span></span>|

<span data-ttu-id="f225a-426">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-426">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="f225a-432">Prérequis</span><span class="sxs-lookup"><span data-stu-id="f225a-432">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f225a-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f225a-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f225a-435">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="f225a-436">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="f225a-436">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-437">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f225a-438">Dans le menu **fichier** , sélectionnez **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="f225a-438">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f225a-439">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f225a-439">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="f225a-440">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-440">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="f225a-441">Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 3,1** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="f225a-441">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="f225a-442">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-442">Select the **API** template and click **Create**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f225a-444">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f225a-444">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f225a-445">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f225a-445">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f225a-446">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="f225a-446">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="f225a-447">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f225a-447">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="f225a-448">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f225a-448">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="f225a-449">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="f225a-449">The preceding commands:</span></span>

  * <span data-ttu-id="f225a-450">Créent un projet API Web et l’ouvrent dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f225a-450">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="f225a-451">Ajoutent les packages NuGet qui sont requis dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="f225a-451">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f225a-452">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-452">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f225a-453">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="f225a-453">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="f225a-455">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >    >    >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f225a-455">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="f225a-456">Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >    >  **API** application console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f225a-456">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![sélection du modèle d’API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="f225a-458">Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 3. x **Target Framework**.</span><span class="sxs-lookup"><span data-stu-id="f225a-458">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="f225a-459">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f225a-459">Select **Next**.</span></span>

* <span data-ttu-id="f225a-460">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-460">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="f225a-462">Ouvrez un terminal de commande dans le dossier du projet et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f225a-462">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="f225a-463">Tester l’API</span><span class="sxs-lookup"><span data-stu-id="f225a-463">Test the API</span></span>

<span data-ttu-id="f225a-464">Le modèle de projet crée une API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="f225a-464">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="f225a-465">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-465">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-466">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-466">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f225a-467">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-467">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f225a-468">Visual Studio lance un navigateur et accède à `https://localhost:<port>/WeatherForecast`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="f225a-468">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="f225a-469">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f225a-469">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="f225a-470">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f225a-470">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f225a-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f225a-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f225a-472">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-472">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f225a-473">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="f225a-473">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f225a-474">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f225a-475">Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-475">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="f225a-476">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="f225a-476">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="f225a-477">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="f225a-477">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="f225a-478">Ajoutez `/WeatherForecast` à l’URL (définissez-la sur `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="f225a-478">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="f225a-479">Un code JSON similaire au suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="f225a-479">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="f225a-480">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="f225a-480">Add a model class</span></span>

<span data-ttu-id="f225a-481">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-481">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="f225a-482">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="f225a-482">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f225a-484">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="f225a-484">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="f225a-485">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f225a-485">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f225a-486">Nommez le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="f225a-486">Name the folder *Models*.</span></span>

* <span data-ttu-id="f225a-487">Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="f225a-487">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f225a-488">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f225a-488">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="f225a-489">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-489">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f225a-490">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f225a-490">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f225a-491">Ajoutez un dossier nommé *Models* .</span><span class="sxs-lookup"><span data-stu-id="f225a-491">Add a folder named *Models*.</span></span>

* <span data-ttu-id="f225a-492">Ajoutez une `TodoItem` classe au dossier à l' *Models* aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-492">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f225a-493">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-493">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f225a-494">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="f225a-494">Right-click the project.</span></span> <span data-ttu-id="f225a-495">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f225a-495">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f225a-496">Nommez le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="f225a-496">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="f225a-498">Cliquez avec le bouton droit sur le *Models* dossier, puis sélectionnez **Ajouter** > **un nouveau fichier** >  > **classe générale vide**.</span><span class="sxs-lookup"><span data-stu-id="f225a-498">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="f225a-499">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="f225a-499">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="f225a-500">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-500">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="f225a-501">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="f225a-501">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="f225a-502">Les classes de modèle peuvent se trouver n’importe où dans le projet, mais le *Models* dossier est utilisé par Convention.</span><span class="sxs-lookup"><span data-stu-id="f225a-502">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="f225a-503">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="f225a-503">Add a database context</span></span>

<span data-ttu-id="f225a-504">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="f225a-504">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="f225a-505">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="f225a-505">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-506">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="f225a-507">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="f225a-507">Add NuGet packages</span></span>

* <span data-ttu-id="f225a-508">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution**.</span><span class="sxs-lookup"><span data-stu-id="f225a-508">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="f225a-509">Sélectionnez l’onglet **Parcourir** , puis entrez **Microsoft. EntityFrameworkCore. InMemory** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f225a-509">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="f225a-510">Dans le volet gauche, sélectionnez **Microsoft. EntityFrameworkCore. InMemory** .</span><span class="sxs-lookup"><span data-stu-id="f225a-510">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="f225a-511">Cochez la case **Projet** dans le volet droit, puis sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-511">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![Gestionnaire de package NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="f225a-513">Ajouter le contexte de base de données TodoContext</span><span class="sxs-lookup"><span data-stu-id="f225a-513">Add the TodoContext database context</span></span>

* <span data-ttu-id="f225a-514">Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="f225a-514">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f225a-515">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f225a-515">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f225a-516">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-516">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f225a-517">Ajoutez une `TodoContext` classe au *Models* dossier.</span><span class="sxs-lookup"><span data-stu-id="f225a-517">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="f225a-518">Entrez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-518">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="f225a-519">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="f225a-519">Register the database context</span></span>

<span data-ttu-id="f225a-520">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f225a-520">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f225a-521">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="f225a-521">The container provides the service to controllers.</span></span>

<span data-ttu-id="f225a-522">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-522">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="f225a-523">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="f225a-523">The preceding code:</span></span>

* <span data-ttu-id="f225a-524">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="f225a-524">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="f225a-525">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="f225a-525">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="f225a-526">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="f225a-526">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="f225a-527">Générer automatiquement des modèles pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="f225a-527">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-528">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-528">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f225a-529">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="f225a-529">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="f225a-530">Sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="f225a-530">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f225a-531">Sélectionnez **Contrôleur d’API avec actions, utilisant Entity Framework**, puis **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f225a-531">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="f225a-532">Dans la boîte de dialogue **Contrôleur d’API avec actions, utilisant Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="f225a-532">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="f225a-533">Sélectionnez **TodoItem (TodoApi. Models )** dans la **classe de modèle**.</span><span class="sxs-lookup"><span data-stu-id="f225a-533">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="f225a-534">Sélectionnez **TodoContext (TodoApi. Models )** dans la **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="f225a-534">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="f225a-535">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f225a-535">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f225a-536">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-536">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f225a-537">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f225a-537">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="f225a-538">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="f225a-538">The preceding commands:</span></span>

* <span data-ttu-id="f225a-539">Ajoutent les packages NuGet nécessaires à la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="f225a-539">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="f225a-540">Installent le moteur de génération de modèles automatique (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="f225a-540">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="f225a-541">Génèrent automatiquement des modèles pour `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="f225a-541">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="f225a-542">Le code généré :</span><span class="sxs-lookup"><span data-stu-id="f225a-542">The generated code:</span></span>

* <span data-ttu-id="f225a-543">Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="f225a-543">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="f225a-544">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="f225a-544">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="f225a-545">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="f225a-545">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="f225a-546">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f225a-546">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="f225a-547">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f225a-547">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="f225a-548">Les modèles de ASP.NET Core pour :</span><span class="sxs-lookup"><span data-stu-id="f225a-548">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="f225a-549">Les contrôleurs avec des vues sont inclus `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="f225a-549">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="f225a-550">Les contrôleurs d’API n’incluent pas `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="f225a-550">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="f225a-551">Lorsque le `[action]` jeton n’est pas dans le modèle de routage, le nom de l' [action](xref:mvc/controllers/routing#action) est exclu de l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="f225a-551">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="f225a-552">Autrement dit, le nom de la méthode associée à l’action n’est pas utilisé dans l’itinéraire correspondant.</span><span class="sxs-lookup"><span data-stu-id="f225a-552">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="f225a-553">Examiner la méthode de création de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="f225a-553">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="f225a-554">Remplacez l’instruction return dans `PostTodoItem` pour utiliser l’opérateur [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="f225a-554">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="f225a-555">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="f225a-555">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="f225a-556">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="f225a-556">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="f225a-557">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f225a-557">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f225a-558">La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> :</span><span class="sxs-lookup"><span data-stu-id="f225a-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="f225a-559">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="f225a-559">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="f225a-560">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="f225a-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="f225a-561">Ajoute un en-tête [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à la réponse.</span><span class="sxs-lookup"><span data-stu-id="f225a-561">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="f225a-562">L’en-tête `Location` spécifie l’[URI](https://developer.mozilla.org/docs/Glossary/URI) de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="f225a-562">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="f225a-563">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f225a-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="f225a-564">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="f225a-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="f225a-565">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="f225a-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="f225a-566">Installer Postman</span><span class="sxs-lookup"><span data-stu-id="f225a-566">Install Postman</span></span>

<span data-ttu-id="f225a-567">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="f225a-567">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="f225a-568">Installer le [poste](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="f225a-568">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="f225a-569">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="f225a-569">Start the web app.</span></span>
* <span data-ttu-id="f225a-570">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="f225a-570">Start Postman.</span></span>
* <span data-ttu-id="f225a-571">Désactivez la **vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="f225a-571">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="f225a-572">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="f225a-572">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="f225a-573">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f225a-573">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="f225a-574">Tester PostTodoItem avec Postman</span><span class="sxs-lookup"><span data-stu-id="f225a-574">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="f225a-575">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="f225a-575">Create a new request.</span></span>
* <span data-ttu-id="f225a-576">Affectez `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="f225a-576">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="f225a-577">Définissez l’URI sur `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="f225a-577">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="f225a-578">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="f225a-578">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="f225a-579">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="f225a-579">Select the **Body** tab.</span></span>
* <span data-ttu-id="f225a-580">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="f225a-580">Select the **raw** radio button.</span></span>
* <span data-ttu-id="f225a-581">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="f225a-581">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="f225a-582">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="f225a-582">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="f225a-583">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-583">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="f225a-585">Tester l’URI d’en-tête d’emplacement avec le poster</span><span class="sxs-lookup"><span data-stu-id="f225a-585">Test the location header URI with Postman</span></span>

* <span data-ttu-id="f225a-586">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="f225a-586">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="f225a-587">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="f225a-587">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="f225a-589">Affectez `GET` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="f225a-589">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="f225a-590">Définissez l’URI sur `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="f225a-590">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="f225a-591">Par exemple : `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="f225a-591">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="f225a-592">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-592">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="f225a-593">Examiner les méthodes GET</span><span class="sxs-lookup"><span data-stu-id="f225a-593">Examine the GET methods</span></span>

<span data-ttu-id="f225a-594">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="f225a-594">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="f225a-595">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur ou de Postman.</span><span class="sxs-lookup"><span data-stu-id="f225a-595">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="f225a-596">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f225a-596">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="f225a-597">Une réponse semblable à la suivante est produite par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="f225a-597">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="f225a-598">Tester Get avec Postman</span><span class="sxs-lookup"><span data-stu-id="f225a-598">Test Get with Postman</span></span>

* <span data-ttu-id="f225a-599">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="f225a-599">Create a new request.</span></span>
* <span data-ttu-id="f225a-600">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="f225a-600">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="f225a-601">Définissez l’URI de la demande sur `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="f225a-601">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="f225a-602">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="f225a-602">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="f225a-603">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="f225a-603">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="f225a-604">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-604">Select **Send**.</span></span>

<span data-ttu-id="f225a-605">Cette application utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="f225a-605">This app uses an in-memory database.</span></span> <span data-ttu-id="f225a-606">Si l’application est arrêtée et démarrée, la requête GET précédente ne retourne aucune donnée.</span><span class="sxs-lookup"><span data-stu-id="f225a-606">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="f225a-607">Si aucune donnée n’est retournée, publiez ([POST](#post)) les données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-607">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="f225a-608">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="f225a-608">Routing and URL paths</span></span>

<span data-ttu-id="f225a-609">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="f225a-609">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="f225a-610">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="f225a-610">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="f225a-611">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="f225a-611">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="f225a-612">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="f225a-612">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="f225a-613">Pour cet exemple, le nom de la classe du contrôleur étant **TodoItems** Controller, le nom du contrôleur est « TodoItems ».</span><span class="sxs-lookup"><span data-stu-id="f225a-613">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="f225a-614">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="f225a-614">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="f225a-615">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="f225a-615">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="f225a-616">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="f225a-616">This sample doesn't use a template.</span></span> <span data-ttu-id="f225a-617">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f225a-617">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f225a-618">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="f225a-618">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="f225a-619">Lorsque `GetTodoItem` est appelé, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son `id` paramètre.</span><span class="sxs-lookup"><span data-stu-id="f225a-619">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="f225a-620">Valeurs de retour</span><span class="sxs-lookup"><span data-stu-id="f225a-620">Return values</span></span> 

<span data-ttu-id="f225a-621">Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="f225a-621">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="f225a-622">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="f225a-622">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="f225a-623">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="f225a-623">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="f225a-624">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="f225a-624">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="f225a-625">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="f225a-625">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="f225a-626">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="f225a-626">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="f225a-627">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> code d’erreur 404.</span><span class="sxs-lookup"><span data-stu-id="f225a-627">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="f225a-628">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="f225a-628">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="f225a-629">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="f225a-629">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="f225a-630">Méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="f225a-630">The PutTodoItem method</span></span>

<span data-ttu-id="f225a-631">Examinez la méthode `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="f225a-631">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="f225a-632">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="f225a-632">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="f225a-633">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f225a-633">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="f225a-634">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="f225a-634">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="f225a-635">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="f225a-635">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="f225a-636">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="f225a-636">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="f225a-637">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="f225a-637">Test the PutTodoItem method</span></span>

<span data-ttu-id="f225a-638">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="f225a-638">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="f225a-639">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="f225a-639">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="f225a-640">Appelez obtenir pour vous assurer qu’il y a un élément dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="f225a-640">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="f225a-641">Mettez à jour l’élément de tâche dont l’ID est égal à 1 et affectez-lui le nom « flux de poisson » :</span><span class="sxs-lookup"><span data-stu-id="f225a-641">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="f225a-642">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="f225a-642">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="f225a-644">Méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="f225a-644">The DeleteTodoItem method</span></span>

<span data-ttu-id="f225a-645">Examinez la méthode `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="f225a-645">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="f225a-646">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="f225a-646">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="f225a-647">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="f225a-647">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="f225a-648">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="f225a-648">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="f225a-649">Définissez l’URI de l’objet à supprimer (par exemple `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="f225a-649">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="f225a-650">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-650">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="f225a-651">Empêcher la post-validation</span><span class="sxs-lookup"><span data-stu-id="f225a-651">Prevent over-posting</span></span>

<span data-ttu-id="f225a-652">Actuellement, l’exemple d’application expose l' `TodoItem` objet entier.</span><span class="sxs-lookup"><span data-stu-id="f225a-652">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="f225a-653">En général, les applications de production limitent les données entrées et retournées à l’aide d’un sous-ensemble du modèle.</span><span class="sxs-lookup"><span data-stu-id="f225a-653">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="f225a-654">Il existe plusieurs raisons à cela, et la sécurité est essentielle.</span><span class="sxs-lookup"><span data-stu-id="f225a-654">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="f225a-655">Le sous-ensemble d’un modèle est généralement appelé un objet Transfert de données (DTO), un modèle d’entrée ou un modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="f225a-655">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="f225a-656">Le **DTO** est utilisé dans cet article.</span><span class="sxs-lookup"><span data-stu-id="f225a-656">**DTO** is used in this article.</span></span>

<span data-ttu-id="f225a-657">Un DTO peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="f225a-657">A DTO may be used to:</span></span>

* <span data-ttu-id="f225a-658">Empêcher la survalidation.</span><span class="sxs-lookup"><span data-stu-id="f225a-658">Prevent over-posting.</span></span>
* <span data-ttu-id="f225a-659">Masquer les propriétés que les clients ne sont pas censés afficher.</span><span class="sxs-lookup"><span data-stu-id="f225a-659">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="f225a-660">Omettez certaines propriétés afin de réduire la taille de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="f225a-660">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="f225a-661">Aplatir les graphiques d’objets qui contiennent des objets imbriqués.</span><span class="sxs-lookup"><span data-stu-id="f225a-661">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="f225a-662">Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.</span><span class="sxs-lookup"><span data-stu-id="f225a-662">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="f225a-663">Pour illustrer l’approche DTO, mettez à jour la `TodoItem` classe pour inclure un champ secret :</span><span class="sxs-lookup"><span data-stu-id="f225a-663">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="f225a-664">Le champ secret doit être masqué à partir de cette application, mais une application administrative peut choisir de l’exposer.</span><span class="sxs-lookup"><span data-stu-id="f225a-664">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="f225a-665">Vérifiez que vous pouvez poster et recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="f225a-665">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="f225a-666">Créer un modèle DTO :</span><span class="sxs-lookup"><span data-stu-id="f225a-666">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="f225a-667">Mettez à jour le `TodoItemsController` pour utiliser `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="f225a-667">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="f225a-668">Vérifiez que vous ne pouvez pas poster ou recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="f225a-668">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="f225a-669">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="f225a-669">Call the web API with JavaScript</span></span>

<span data-ttu-id="f225a-670">Consultez [Didacticiel : appeler une API web ASP.net core avec JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="f225a-670">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f225a-671">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="f225a-671">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f225a-672">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="f225a-672">Create a web API project.</span></span>
> * <span data-ttu-id="f225a-673">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="f225a-673">Add a model class and a database context.</span></span>
> * <span data-ttu-id="f225a-674">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="f225a-674">Add a controller.</span></span>
> * <span data-ttu-id="f225a-675">Ajouter les méthodes CRUD</span><span class="sxs-lookup"><span data-stu-id="f225a-675">Add CRUD methods.</span></span>
> * <span data-ttu-id="f225a-676">Configurer le routage et les chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="f225a-676">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="f225a-677">Spécifier des valeurs de retour</span><span class="sxs-lookup"><span data-stu-id="f225a-677">Specify return values.</span></span>
> * <span data-ttu-id="f225a-678">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="f225a-678">Call the web API with Postman.</span></span>
> * <span data-ttu-id="f225a-679">Appeler l’API web avec JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f225a-679">Call the web API with JavaScript.</span></span>

<span data-ttu-id="f225a-680">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="f225a-680">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="f225a-681">Vue d’ensemble 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-681">Overview 2.1</span></span>

<span data-ttu-id="f225a-682">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="f225a-682">This tutorial creates the following API:</span></span>

|<span data-ttu-id="f225a-683">API</span><span class="sxs-lookup"><span data-stu-id="f225a-683">API</span></span> | <span data-ttu-id="f225a-684">Description</span><span class="sxs-lookup"><span data-stu-id="f225a-684">Description</span></span> | <span data-ttu-id="f225a-685">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="f225a-685">Request body</span></span> | <span data-ttu-id="f225a-686">Response body</span><span class="sxs-lookup"><span data-stu-id="f225a-686">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="f225a-687">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="f225a-687">GET /api/TodoItems</span></span> | <span data-ttu-id="f225a-688">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="f225a-688">Get all to-do items</span></span> | <span data-ttu-id="f225a-689">Aucun</span><span class="sxs-lookup"><span data-stu-id="f225a-689">None</span></span> | <span data-ttu-id="f225a-690">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="f225a-690">Array of to-do items</span></span>|
|<span data-ttu-id="f225a-691">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="f225a-691">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="f225a-692">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="f225a-692">Get an item by ID</span></span> | <span data-ttu-id="f225a-693">Aucun</span><span class="sxs-lookup"><span data-stu-id="f225a-693">None</span></span> | <span data-ttu-id="f225a-694">Tâche</span><span class="sxs-lookup"><span data-stu-id="f225a-694">To-do item</span></span>|
|<span data-ttu-id="f225a-695">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="f225a-695">POST /api/TodoItems</span></span> | <span data-ttu-id="f225a-696">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="f225a-696">Add a new item</span></span> | <span data-ttu-id="f225a-697">Tâche</span><span class="sxs-lookup"><span data-stu-id="f225a-697">To-do item</span></span> | <span data-ttu-id="f225a-698">Tâche</span><span class="sxs-lookup"><span data-stu-id="f225a-698">To-do item</span></span> |
|<span data-ttu-id="f225a-699">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="f225a-699">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="f225a-700">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f225a-700">Update an existing item &nbsp;</span></span> | <span data-ttu-id="f225a-701">Tâche</span><span class="sxs-lookup"><span data-stu-id="f225a-701">To-do item</span></span> | <span data-ttu-id="f225a-702">Aucun</span><span class="sxs-lookup"><span data-stu-id="f225a-702">None</span></span> |
|<span data-ttu-id="f225a-703">SUPPRIMER/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="f225a-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="f225a-704">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="f225a-704">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="f225a-705">None</span><span class="sxs-lookup"><span data-stu-id="f225a-705">None</span></span> | <span data-ttu-id="f225a-706">None</span><span class="sxs-lookup"><span data-stu-id="f225a-706">None</span></span>|

<span data-ttu-id="f225a-707">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-707">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="f225a-713">Conditions préalables 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-713">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-714">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-714">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f225a-715">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f225a-715">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f225a-716">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-716">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="f225a-717">Créer un projet Web 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-717">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-718">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f225a-719">Dans le menu **fichier** , sélectionnez **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="f225a-719">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f225a-720">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f225a-720">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="f225a-721">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-721">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="f225a-722">Dans la boîte de dialogue **Créer une application web ASP.NET Core**, vérifiez que **.NET Core** et **ASP.NET Core 2.2** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="f225a-722">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="f225a-723">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-723">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="f225a-724">Ne sélectionnez **pas\*\*\*\*Activer la prise en charge de Docker**.</span><span class="sxs-lookup"><span data-stu-id="f225a-724">**Don't** select **Enable Docker Support**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f225a-726">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f225a-726">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f225a-727">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f225a-727">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f225a-728">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="f225a-728">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="f225a-729">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f225a-729">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="f225a-730">Ces commandes créent un projet d’API web et ouvrent une nouvelle instance de Visual Studio Code dans le nouveau dossier de projet.</span><span class="sxs-lookup"><span data-stu-id="f225a-730">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="f225a-731">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f225a-731">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f225a-732">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-732">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f225a-733">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="f225a-733">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="f225a-735">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >    >    >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f225a-735">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="f225a-736">Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >    >  **API** application console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f225a-736">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="f225a-737">Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 2. x **Target Framework**.</span><span class="sxs-lookup"><span data-stu-id="f225a-737">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="f225a-738">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f225a-738">Select **Next**.</span></span>

* <span data-ttu-id="f225a-739">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-739">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="f225a-741">Tester l’API 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-741">Test the API 2.1</span></span>

<span data-ttu-id="f225a-742">Le modèle de projet crée une API `values`.</span><span class="sxs-lookup"><span data-stu-id="f225a-742">The project template creates a `values` API.</span></span> <span data-ttu-id="f225a-743">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-743">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-744">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-744">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f225a-745">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-745">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f225a-746">Visual Studio lance un navigateur et accède à `https://localhost:<port>/api/values`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="f225a-746">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="f225a-747">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f225a-747">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="f225a-748">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f225a-748">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f225a-749">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f225a-749">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f225a-750">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-750">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f225a-751">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="f225a-751">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f225a-752">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-752">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f225a-753">Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-753">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="f225a-754">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="f225a-754">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="f225a-755">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="f225a-755">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="f225a-756">Ajoutez `/api/values` à l’URL (définissez-la sur `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="f225a-756">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="f225a-757">Le code JSON suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="f225a-757">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="f225a-758">Ajouter une classe de modèle 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-758">Add a model class 2.1</span></span>

<span data-ttu-id="f225a-759">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="f225a-759">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="f225a-760">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="f225a-760">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-761">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f225a-762">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="f225a-762">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="f225a-763">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f225a-763">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f225a-764">Nommez le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="f225a-764">Name the folder *Models*.</span></span>

* <span data-ttu-id="f225a-765">Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="f225a-765">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f225a-766">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f225a-766">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="f225a-767">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-767">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f225a-768">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f225a-768">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f225a-769">Ajoutez un dossier nommé *Models* .</span><span class="sxs-lookup"><span data-stu-id="f225a-769">Add a folder named *Models*.</span></span>

* <span data-ttu-id="f225a-770">Ajoutez une `TodoItem` classe au dossier à l' *Models* aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-770">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f225a-771">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-771">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f225a-772">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="f225a-772">Right-click the project.</span></span> <span data-ttu-id="f225a-773">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="f225a-773">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f225a-774">Nommez le dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="f225a-774">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="f225a-776">Cliquez avec le bouton droit sur le *Models* dossier, puis sélectionnez **Ajouter** > **un nouveau fichier** >  > **classe générale vide**.</span><span class="sxs-lookup"><span data-stu-id="f225a-776">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="f225a-777">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="f225a-777">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="f225a-778">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-778">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="f225a-779">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="f225a-779">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="f225a-780">Les classes de modèle peuvent se trouver n’importe où dans le projet, mais le *Models* dossier est utilisé par Convention.</span><span class="sxs-lookup"><span data-stu-id="f225a-780">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="f225a-781">Ajouter un contexte de base de données 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-781">Add a database context 2.1</span></span>

<span data-ttu-id="f225a-782">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="f225a-782">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="f225a-783">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="f225a-783">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-784">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-784">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f225a-785">Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="f225a-785">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f225a-786">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f225a-786">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f225a-787">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-787">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f225a-788">Ajoutez une `TodoContext` classe au *Models* dossier.</span><span class="sxs-lookup"><span data-stu-id="f225a-788">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="f225a-789">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-789">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="f225a-790">Inscrire le contexte de base de données 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-790">Register the database context 2.1</span></span>

<span data-ttu-id="f225a-791">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f225a-791">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f225a-792">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="f225a-792">The container provides the service to controllers.</span></span>

<span data-ttu-id="f225a-793">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-793">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="f225a-794">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="f225a-794">The preceding code:</span></span>

* <span data-ttu-id="f225a-795">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="f225a-795">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="f225a-796">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="f225a-796">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="f225a-797">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="f225a-797">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="f225a-798">Ajouter un contrôleur 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-798">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-799">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-799">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f225a-800">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="f225a-800">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="f225a-801">Sélectionnez **Ajouter** > **un nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="f225a-801">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="f225a-802">Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez le modèle **Classe de contrôleur d’API**.</span><span class="sxs-lookup"><span data-stu-id="f225a-802">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="f225a-803">Nommez la classe *TodoController* et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f225a-803">Name the class *TodoController*, and select **Add**.</span></span>

  ![Boîte de dialogue Ajouter un nouvel élément avec contrôleur dans la zone de recherche et le contrôleur des API web sélectionné](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f225a-805">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-805">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f225a-806">Dans le dossier *Contrôleurs*, créez une classe nommée `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="f225a-806">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="f225a-807">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-807">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="f225a-808">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="f225a-808">The preceding code:</span></span>

* <span data-ttu-id="f225a-809">Définit une classe de contrôleur d’API sans méthodes.</span><span class="sxs-lookup"><span data-stu-id="f225a-809">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="f225a-810">Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="f225a-810">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="f225a-811">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="f225a-811">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="f225a-812">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="f225a-812">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="f225a-813">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f225a-813">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="f225a-814">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f225a-814">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="f225a-815">Ajoute un élément nommé `Item1` à la base de données si celle-ci est vide.</span><span class="sxs-lookup"><span data-stu-id="f225a-815">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="f225a-816">Ce code se trouvant dans le constructeur, il s’exécute chaque fois qu’une nouvelle requête HTTP existe.</span><span class="sxs-lookup"><span data-stu-id="f225a-816">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="f225a-817">Si vous supprimez tous les éléments, le constructeur recrée `Item1` au prochain appel d’une méthode d’API.</span><span class="sxs-lookup"><span data-stu-id="f225a-817">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="f225a-818">Ainsi, il peut vous sembler à tort que la suppression n’a pas fonctionné.</span><span class="sxs-lookup"><span data-stu-id="f225a-818">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="f225a-819">Ajouter des méthodes de récupération 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-819">Add Get methods 2.1</span></span>

<span data-ttu-id="f225a-820">Pour fournir une API qui récupère les tâches, ajoutez les méthodes suivantes à la classe `TodoController` :</span><span class="sxs-lookup"><span data-stu-id="f225a-820">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="f225a-821">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="f225a-821">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="f225a-822">Arrêtez l’application si elle est toujours en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="f225a-822">Stop the app if it's still running.</span></span> <span data-ttu-id="f225a-823">Ensuite, réexécutez-la pour inclure les dernières modifications.</span><span class="sxs-lookup"><span data-stu-id="f225a-823">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="f225a-824">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur.</span><span class="sxs-lookup"><span data-stu-id="f225a-824">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="f225a-825">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f225a-825">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="f225a-826">La réponse HTTP suivante est générée par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="f225a-826">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="f225a-827">Routage et chemins d’URL 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-827">Routing and URL paths 2.1</span></span>

<span data-ttu-id="f225a-828">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="f225a-828">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="f225a-829">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="f225a-829">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="f225a-830">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="f225a-830">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="f225a-831">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="f225a-831">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="f225a-832">Pour cet exemple, le nom de classe du contrôleur étant **Todo** Controller, le nom du contrôleur est « todo ».</span><span class="sxs-lookup"><span data-stu-id="f225a-832">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="f225a-833">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="f225a-833">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="f225a-834">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="f225a-834">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="f225a-835">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="f225a-835">This sample doesn't use a template.</span></span> <span data-ttu-id="f225a-836">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f225a-836">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f225a-837">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="f225a-837">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="f225a-838">Quand `GetTodoItem` est appelée, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son paramètre `id`.</span><span class="sxs-lookup"><span data-stu-id="f225a-838">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="f225a-839">Valeurs de retour 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-839">Return values 2.1</span></span>

<span data-ttu-id="f225a-840">Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="f225a-840">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="f225a-841">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="f225a-841">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="f225a-842">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="f225a-842">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="f225a-843">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="f225a-843">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="f225a-844">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="f225a-844">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="f225a-845">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="f225a-845">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="f225a-846">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> code d’erreur 404.</span><span class="sxs-lookup"><span data-stu-id="f225a-846">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="f225a-847">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="f225a-847">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="f225a-848">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="f225a-848">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="f225a-849">Tester la méthode GetTodoItems 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-849">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="f225a-850">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="f225a-850">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="f225a-851">Installer [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="f225a-851">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="f225a-852">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="f225a-852">Start the web app.</span></span>
* <span data-ttu-id="f225a-853">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="f225a-853">Start Postman.</span></span>
* <span data-ttu-id="f225a-854">Désactivez la **vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="f225a-854">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f225a-855">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f225a-855">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f225a-856">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="f225a-856">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f225a-857">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f225a-857">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f225a-858">Dans   >  **Préférences** de publication (onglet **général** ), désactivez la vérification de **certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="f225a-858">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="f225a-859">Vous pouvez également sélectionner la clé et sélectionner **Paramètres**, puis désactiver la vérification du certificat SSL.</span><span class="sxs-lookup"><span data-stu-id="f225a-859">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="f225a-860">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="f225a-860">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="f225a-861">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="f225a-861">Create a new request.</span></span>
  * <span data-ttu-id="f225a-862">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="f225a-862">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="f225a-863">Définissez l’URI de la demande sur `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="f225a-863">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="f225a-864">Par exemple : `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="f225a-864">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="f225a-865">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="f225a-865">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="f225a-866">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-866">Select **Send**.</span></span>

![Postman avec requête Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="f225a-868">Ajouter une méthode de création 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-868">Add a Create method 2.1</span></span>

<span data-ttu-id="f225a-869">Ajoutez la méthode `PostTodoItem` suivante à *Controllers/TodoController.cs* :</span><span class="sxs-lookup"><span data-stu-id="f225a-869">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="f225a-870">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="f225a-870">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="f225a-871">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="f225a-871">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="f225a-872">La méthode `CreatedAtAction` :</span><span class="sxs-lookup"><span data-stu-id="f225a-872">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="f225a-873">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="f225a-873">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="f225a-874">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="f225a-874">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="f225a-875">Ajoute un en-tête `Location` à la réponse.</span><span class="sxs-lookup"><span data-stu-id="f225a-875">Adds a `Location` header to the response.</span></span> <span data-ttu-id="f225a-876">L’en-tête `Location` spécifie l’URI de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="f225a-876">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="f225a-877">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f225a-877">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="f225a-878">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="f225a-878">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="f225a-879">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="f225a-879">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="f225a-880">Tester la méthode PostTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-880">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="f225a-881">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="f225a-881">Build the project.</span></span>
* <span data-ttu-id="f225a-882">Dans Postman, définissez la méthode HTTP sur `POST`.</span><span class="sxs-lookup"><span data-stu-id="f225a-882">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="f225a-883">Définissez l’URI sur `https://localhost:<port>/api/Todo` .</span><span class="sxs-lookup"><span data-stu-id="f225a-883">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="f225a-884">Par exemple : `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="f225a-884">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="f225a-885">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="f225a-885">Select the **Body** tab.</span></span>
* <span data-ttu-id="f225a-886">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="f225a-886">Select the **raw** radio button.</span></span>
* <span data-ttu-id="f225a-887">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="f225a-887">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="f225a-888">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="f225a-888">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="f225a-889">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-889">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/create.png)

  <span data-ttu-id="f225a-891">Si vous obtenez une erreur 405 Méthode non autorisée, il est probable que le projet n’ait pas été compilé après l’ajout de la méthode `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="f225a-891">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="f225a-892">Tester l’URI d’en-tête d’emplacement 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-892">Test the location header URI 2.1</span></span>

* <span data-ttu-id="f225a-893">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="f225a-893">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="f225a-894">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="f225a-894">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="f225a-896">Définissez la méthode sur GET.</span><span class="sxs-lookup"><span data-stu-id="f225a-896">Set the method to GET.</span></span>
* <span data-ttu-id="f225a-897">Définissez l’URI sur `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="f225a-897">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="f225a-898">Par exemple : `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="f225a-898">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="f225a-899">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-899">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="f225a-900">Ajouter une méthode PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-900">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="f225a-901">Ajoutez la méthode `PutTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="f225a-901">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="f225a-902">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="f225a-902">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="f225a-903">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f225a-903">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="f225a-904">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="f225a-904">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="f225a-905">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="f225a-905">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="f225a-906">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="f225a-906">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="f225a-907">Tester la méthode PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-907">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="f225a-908">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="f225a-908">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="f225a-909">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="f225a-909">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="f225a-910">Appelez obtenir pour vous assurer qu’il y a un élément dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="f225a-910">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="f225a-911">Mettez à jour l’élément de tâche dont l’ID est égal à 1 et affectez-lui le nom « flux de poisson » :</span><span class="sxs-lookup"><span data-stu-id="f225a-911">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="f225a-912">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="f225a-912">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="f225a-914">Ajouter une méthode DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-914">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="f225a-915">Ajoutez la méthode `DeleteTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="f225a-915">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="f225a-916">La réponse `DeleteTodoItem` est [204 (Pas de contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f225a-916">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="f225a-917">Tester la méthode DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-917">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="f225a-918">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="f225a-918">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="f225a-919">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="f225a-919">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="f225a-920">Définissez l’URI de l’objet à supprimer (par exemple, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="f225a-920">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="f225a-921">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="f225a-921">Select **Send**.</span></span>

<span data-ttu-id="f225a-922">L’exemple d’application vous permet de supprimer tous les éléments.</span><span class="sxs-lookup"><span data-stu-id="f225a-922">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="f225a-923">Toutefois, quand le dernier élément est supprimé, un autre est créé par le constructeur de classe de modèle au prochain appel de l’API.</span><span class="sxs-lookup"><span data-stu-id="f225a-923">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="f225a-924">Appeler l’API Web avec JavaScript 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-924">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="f225a-925">Dans cette section, une page HTML qui utilise JavaScript pour appeler l’API web est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="f225a-925">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="f225a-926">jQuery lance la demande.</span><span class="sxs-lookup"><span data-stu-id="f225a-926">jQuery initiates the request.</span></span> <span data-ttu-id="f225a-927">JavaScript met à jour la page avec les détails de la réponse de l’API Web.</span><span class="sxs-lookup"><span data-stu-id="f225a-927">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="f225a-928">Configurez l’application pour [traiter les fichiers statiques](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) et [activer le mappage de fichier par défaut](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) en mettant à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-928">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="f225a-929">Créez un dossier *wwwroot* dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="f225a-929">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="f225a-930">Ajoutez un fichier HTML nommé *index.html* au répertoire *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="f225a-930">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="f225a-931">Remplacez son contenu par le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-931">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="f225a-932">Ajoutez un fichier JavaScript nommé *site.js* au répertoire *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="f225a-932">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="f225a-933">Remplacez le contenu par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f225a-933">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="f225a-934">Vous devrez peut-être changer les paramètres de lancement du projet ASP.NET Core pour tester la page HTML localement :</span><span class="sxs-lookup"><span data-stu-id="f225a-934">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="f225a-935">Ouvrez *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f225a-935">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="f225a-936">Supprimez la `launchUrl` propriété pour forcer l’ouverture de l’application à *index.html* &mdash; fichier par défaut du projet.</span><span class="sxs-lookup"><span data-stu-id="f225a-936">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="f225a-937">Cet exemple appelle toutes les méthodes CRUD de l’API web.</span><span class="sxs-lookup"><span data-stu-id="f225a-937">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="f225a-938">Les explications suivantes traitent des appels à l’API.</span><span class="sxs-lookup"><span data-stu-id="f225a-938">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="f225a-939">Obtenir la liste des éléments de tâche 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-939">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="f225a-940">jQuery envoie une requête HTTP obtenir à l’API Web, qui retourne JSON représentant un tableau d’éléments de tâche.</span><span class="sxs-lookup"><span data-stu-id="f225a-940">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="f225a-941">La fonction de rappel `success` est appelée si la requête réussit.</span><span class="sxs-lookup"><span data-stu-id="f225a-941">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="f225a-942">Dans le rappel, le DOM est mis à jour avec les informations des tâches.</span><span class="sxs-lookup"><span data-stu-id="f225a-942">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="f225a-943">Ajouter un élément de tâche 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-943">Add a to-do item 2.1</span></span>

<span data-ttu-id="f225a-944">jQuery envoie une requête HTTP POSTÉRIEURe avec l’élément de tâche dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="f225a-944">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="f225a-945">Les options `accepts` et `contentType` sont définies avec la valeur `application/json` pour spécifier le type de média qui est reçu et envoyé.</span><span class="sxs-lookup"><span data-stu-id="f225a-945">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="f225a-946">La tâche est convertie au format JSON à l’aide de [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="f225a-946">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="f225a-947">Quand l’API retourne un code d’état de réussite, la fonction `getData` est appelée pour mettre à jour la table HTML.</span><span class="sxs-lookup"><span data-stu-id="f225a-947">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="f225a-948">Mettre à jour un élément de tâche 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-948">Update a to-do item 2.1</span></span>

<span data-ttu-id="f225a-949">La mise à jour d’une tâche est similaire à l’ajout d’une tâche.</span><span class="sxs-lookup"><span data-stu-id="f225a-949">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="f225a-950">L’identificateur unique de la tâche est ajouté à l’`url` et le `type` est `PUT`.</span><span class="sxs-lookup"><span data-stu-id="f225a-950">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="f225a-951">Supprimer un élément de tâche 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-951">Delete a to-do item 2.1</span></span>

<span data-ttu-id="f225a-952">Pour supprimer une tâche, vous devez définir le `type` sur l’appel AJAX avec la valeur `DELETE` et spécifier l’identificateur unique de l’élément dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="f225a-952">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="f225a-953">Ajouter la prise en charge de l’authentification à une API Web 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-953">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="f225a-954">Ressources supplémentaires 2,1</span><span class="sxs-lookup"><span data-stu-id="f225a-954">Additional resources 2.1</span></span>

<span data-ttu-id="f225a-955">[Affichez ou téléchargez l’exemple de code de ce tutoriel](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="f225a-955">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="f225a-956">Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f225a-956">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="f225a-957">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="f225a-957">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="f225a-958">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="f225a-958">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
* [<span data-ttu-id="f225a-959">Microsoft Learn : créer une API Web avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f225a-959">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
