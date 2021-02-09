---
title: 'Partie 2 : ajouter un contrôleur à une application ASP.NET Core MVC'
author: rick-anderson
description: Partie 2 de la série de didacticiels sur ASP.NET Core MVC.
ms.author: riande
ms.date: 01/23/2021
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
uid: tutorials/first-mvc-app/adding-controller
ms.custom: contperf-fy21q3
ms.openlocfilehash: 47bb9b96bd5565a3a67f3cbdf9a4b6bc1f987447
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975259"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="88380-103">Partie 2 : ajouter un contrôleur à une application ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="88380-103">Part 2, add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="88380-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="88380-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="88380-105">Le modèle d’architecture MVC (Model-View-Controller) sépare une application en trois composants principaux : **M** odèle, **V** ue et **C** ontrôleur.</span><span class="sxs-lookup"><span data-stu-id="88380-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="88380-106">Le modèle MVC vous permet de créer des applications qui sont plus faciles à tester et à mettre à jour que les applications monolithiques traditionnelles.</span><span class="sxs-lookup"><span data-stu-id="88380-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span>

<span data-ttu-id="88380-107">Les applications basées sur MVC contiennent :</span><span class="sxs-lookup"><span data-stu-id="88380-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="88380-108">Des **M** odèles : des classes qui représentent les données de l’application.</span><span class="sxs-lookup"><span data-stu-id="88380-108">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="88380-109">Les classes du modèle utilisent une logique de validation pour appliquer des règles d’entreprise à ces données.</span><span class="sxs-lookup"><span data-stu-id="88380-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="88380-110">En règle générale, les objets du modèle récupèrent et stockent l’état du modèle dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="88380-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="88380-111">Dans ce didacticiel, un modèle `Movie` récupère les données des films dans une base de données, les fournit à la vue ou les met à jour.</span><span class="sxs-lookup"><span data-stu-id="88380-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="88380-112">Les données mises à jour sont écrites dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="88380-112">Updated data is written to a database.</span></span>
* <span data-ttu-id="88380-113">**V** ues : les vues sont les composants qui affichent l’interface utilisateur de l’application.</span><span class="sxs-lookup"><span data-stu-id="88380-113">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="88380-114">En règle générale, cette interface utilisateur affiche les données du modèle.</span><span class="sxs-lookup"><span data-stu-id="88380-114">Generally, this UI displays the model data.</span></span>
* <span data-ttu-id="88380-115">**C** Ontrôleurs : classes qui :</span><span class="sxs-lookup"><span data-stu-id="88380-115">**C** ontrollers: Classes that:</span></span>
  * <span data-ttu-id="88380-116">Gérer les requêtes de navigateur.</span><span class="sxs-lookup"><span data-stu-id="88380-116">Handle browser requests.</span></span>
  * <span data-ttu-id="88380-117">Récupérer les données du modèle.</span><span class="sxs-lookup"><span data-stu-id="88380-117">Retrieve model data.</span></span>
  * <span data-ttu-id="88380-118">Appelez les modèles de vue qui retournent une réponse.</span><span class="sxs-lookup"><span data-stu-id="88380-118">Call view templates that return a response.</span></span>

<span data-ttu-id="88380-119">Dans une application MVC, la vue affiche uniquement des informations.</span><span class="sxs-lookup"><span data-stu-id="88380-119">In an MVC app, the view only displays information.</span></span> <span data-ttu-id="88380-120">Le contrôleur gère et répond aux entrées et aux interactions de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="88380-120">The controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="88380-121">Par exemple, le contrôleur gère les segments d’URL et les valeurs de chaîne de requête, et transmet ces valeurs au modèle.</span><span class="sxs-lookup"><span data-stu-id="88380-121">For example, the controller handles URL segments and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="88380-122">Le modèle peut utiliser ces valeurs pour interroger la base de données.</span><span class="sxs-lookup"><span data-stu-id="88380-122">The model might use these values to query the database.</span></span> <span data-ttu-id="88380-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="88380-123">For example:</span></span>

* <span data-ttu-id="88380-124">`Https://localhost:5001/Home/Privacy`: spécifie le `Home`  contrôleur et l' `Privacy` action.</span><span class="sxs-lookup"><span data-stu-id="88380-124">`Https://localhost:5001/Home/Privacy`: specifies the `Home`  controller  and the `Privacy` action.</span></span>
* <span data-ttu-id="88380-125">`Https://localhost:5001/Movies/Edit/5`: demande de modifier le film avec ID = 5 à l’aide du `Movies` contrôleur et de l' `Edit` action, qui sont détaillées plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="88380-125">`Https://localhost:5001/Movies/Edit/5`: is a request to edit the movie with ID=5 using the `Movies` controller and the `Edit` action, which are detailed later in the tutorial.</span></span>

<span data-ttu-id="88380-126">Les données d’itinéraire sont expliquées plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="88380-126">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="88380-127">Le modèle architectural MVC sépare une application en trois groupes principaux de composants : les modèles, les vues et les contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="88380-127">The MVC architectural pattern separates an app into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="88380-128">Ce modèle permet de séparer les préoccupations : la logique de l’interface utilisateur appartient à la vue.</span><span class="sxs-lookup"><span data-stu-id="88380-128">This pattern helps to achieve separation of concerns: The UI logic belongs in the view.</span></span> <span data-ttu-id="88380-129">La logique d’entrée appartient au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="88380-129">Input logic belongs in the controller.</span></span> <span data-ttu-id="88380-130">La logique métier appartient au modèle.</span><span class="sxs-lookup"><span data-stu-id="88380-130">Business logic belongs in the model.</span></span> <span data-ttu-id="88380-131">Cette séparation permet de gérer la complexité lors de la création d’une application, car elle permet de travailler sur un aspect de l’implémentation à la fois sans affecter le code d’un autre.</span><span class="sxs-lookup"><span data-stu-id="88380-131">This separation helps manage complexity when building an app, because it enables work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="88380-132">Par exemple, vous pouvez travailler sur le code des vues de façon indépendante du code de la logique métier.</span><span class="sxs-lookup"><span data-stu-id="88380-132">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="88380-133">Ces concepts sont présentés et présentés dans cette série de didacticiels lors de la création d’une application de film.</span><span class="sxs-lookup"><span data-stu-id="88380-133">These concepts are introduced and demonstrated in this tutorial series while building a movie app.</span></span> <span data-ttu-id="88380-134">Le projet MVC contient des dossiers pour les *contrôleurs* et pour les *vues*.</span><span class="sxs-lookup"><span data-stu-id="88380-134">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="88380-135">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="88380-135">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="88380-136">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="88380-136">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="88380-137">Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur **contrôleurs > ajouter un contrôleur de >**.</span><span class="sxs-lookup"><span data-stu-id="88380-137">In the **Solution Explorer**, right-click **Controllers > Add > Controller**.</span></span>

![Explorateur de solutions, cliquez avec le bouton droit sur contrôleurs > ajouter un contrôleur de >](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)

<span data-ttu-id="88380-139">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **contrôleur MVC-vide**.</span><span class="sxs-lookup"><span data-stu-id="88380-139">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**.</span></span>

![Ajouter un contrôleur MVC et le nommer](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

<span data-ttu-id="88380-141">Dans la **boîte de dialogue Ajouter un nouvel élément-MvcMovie**, entrez **HelloWorldController.cs** , puis sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="88380-141">In the **Add New Item - MvcMovie dialog**, enter **HelloWorldController.cs** and select **Add**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="88380-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="88380-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="88380-143">Sélectionnez l’icône **EXPLORATEUR** et cliquez en maintenant enfoncée la touche Ctrl (ou cliquez avec le bouton droit) sur **Contrôleurs > Nouveau fichier** et nommez le nouveau fichier *HelloWorldController.cs*.</span><span class="sxs-lookup"><span data-stu-id="88380-143">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

![Menu contextuel](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="88380-145">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="88380-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="88380-146">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Contrôleurs > Ajouter > Nouveau fichier**.</span><span class="sxs-lookup"><span data-stu-id="88380-146">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![Menu contextuel](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="88380-148">Sélectionnez **ASP.net Core** et **classe de contrôleur**.</span><span class="sxs-lookup"><span data-stu-id="88380-148">Select **ASP.NET Core** and **Controller Class**.</span></span>

<span data-ttu-id="88380-149">Nommez le contrôleur **HelloWorldController**.</span><span class="sxs-lookup"><span data-stu-id="88380-149">Name the controller **HelloWorldController**.</span></span>

![Ajouter un contrôleur MVC et le nommer](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="88380-151">Remplacez le contenu de *Controllers/HelloWorldController.cs* par ceci :</span><span class="sxs-lookup"><span data-stu-id="88380-151">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="88380-152">Chaque méthode `public` d’un contrôleur peut être appelée en tant que point de terminaison HTTP.</span><span class="sxs-lookup"><span data-stu-id="88380-152">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="88380-153">Dans l’exemple ci-dessus, les deux méthodes retournent une chaîne.</span><span class="sxs-lookup"><span data-stu-id="88380-153">In the sample above, both methods return a string.</span></span> <span data-ttu-id="88380-154">Notez les commentaires qui précèdent chaque méthode.</span><span class="sxs-lookup"><span data-stu-id="88380-154">Note the comments preceding each method.</span></span>

<span data-ttu-id="88380-155">Un point de terminaison HTTP :</span><span class="sxs-lookup"><span data-stu-id="88380-155">An HTTP endpoint:</span></span>

* <span data-ttu-id="88380-156">Est une URL qui peut être ciblée dans l’application Web, telle que `https://localhost:5001/HelloWorld` .</span><span class="sxs-lookup"><span data-stu-id="88380-156">Is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`.</span></span>
* <span data-ttu-id="88380-157">Cumul</span><span class="sxs-lookup"><span data-stu-id="88380-157">Combines:</span></span>
  * <span data-ttu-id="88380-158">Protocole utilisé : `HTTPS` .</span><span class="sxs-lookup"><span data-stu-id="88380-158">The protocol used: `HTTPS`.</span></span>
  * <span data-ttu-id="88380-159">L’emplacement réseau du serveur Web, y compris le port TCP : `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="88380-159">The network location of the web server, including the TCP port: `localhost:5001`.</span></span>
  * <span data-ttu-id="88380-160">URI cible : `HelloWorld` .</span><span class="sxs-lookup"><span data-stu-id="88380-160">The target URI: `HelloWorld`.</span></span>

<span data-ttu-id="88380-161">Le premier commentaire indique qu’il s’agit d’une méthode [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) qui est appelée en ajoutant `/HelloWorld/` à l’URL de base.</span><span class="sxs-lookup"><span data-stu-id="88380-161">The first comment states this is an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span>

<span data-ttu-id="88380-162">Le deuxième commentaire indique une méthode [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) qui est appelée en ajoutant `/HelloWorld/Welcome/` à l’URL.</span><span class="sxs-lookup"><span data-stu-id="88380-162">The second comment specifies an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="88380-163">Plus loin dans ce didacticiel, le moteur de génération de modèles automatique est utilisé pour générer des `HTTP POST` méthodes, qui mettent à jour les données.</span><span class="sxs-lookup"><span data-stu-id="88380-163">Later on in the tutorial, the scaffolding engine is used to generate `HTTP POST` methods, which update data.</span></span>

<span data-ttu-id="88380-164">Exécutez l’application sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="88380-164">Run the app without the debugger.</span></span>

<span data-ttu-id="88380-165">Ajoutez « HelloWorld » au chemin d’accès dans la barre d’adresses.</span><span class="sxs-lookup"><span data-stu-id="88380-165">Append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="88380-166">La méthode `Index` retourne une chaîne.</span><span class="sxs-lookup"><span data-stu-id="88380-166">The `Index` method returns a string.</span></span>

![Fenêtre du navigateur présentant une réponse de l’application : mon action par défaut](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="88380-168">MVC appelle les classes de contrôleur et les méthodes d’action qu’ils contiennent, en fonction de l’URL entrante.</span><span class="sxs-lookup"><span data-stu-id="88380-168">MVC invokes controller classes, and the action methods within them, depending on the incoming URL.</span></span> <span data-ttu-id="88380-169">La [logique de routage d’URL](xref:mvc/controllers/routing) par défaut utilisée par MVC utilise un format similaire à celui-ci pour déterminer le code à appeler :</span><span class="sxs-lookup"><span data-stu-id="88380-169">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC, uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="88380-170">Le format de routage est défini dans la méthode `Configure` du fichier *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="88380-170">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="88380-171">Quand vous naviguez jusqu’à l’application et que vous ne fournissez aucun segment d’URL, sa valeur par défaut est le contrôleur « Home » et la méthode « Index » spécifiée dans la ligne du modèle mise en surbrillance ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="88380-171">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>  <span data-ttu-id="88380-172">Dans les segments d’URL précédents :</span><span class="sxs-lookup"><span data-stu-id="88380-172">In the preceding URL segments:</span></span>

* <span data-ttu-id="88380-173">Le premier segment d’URL détermine la classe du contrôleur à exécuter.</span><span class="sxs-lookup"><span data-stu-id="88380-173">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="88380-174">`localhost:5001/HelloWorld` mappe donc à la classe de contrôleur **HelloWorld**.</span><span class="sxs-lookup"><span data-stu-id="88380-174">So `localhost:5001/HelloWorld` maps to the **HelloWorld** Controller class.</span></span>
* <span data-ttu-id="88380-175">La seconde partie du segment d’URL détermine la méthode d’action sur la classe.</span><span class="sxs-lookup"><span data-stu-id="88380-175">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="88380-176">`localhost:5001/HelloWorld/Index`Par conséquent, la `Index` méthode de la `HelloWorldController` classe s’exécute.</span><span class="sxs-lookup"><span data-stu-id="88380-176">So `localhost:5001/HelloWorld/Index` causes the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="88380-177">Notez que vous n’avez eu qu’à accéder à `localhost:5001/HelloWorld` pour que la méthode `Index` soit appelée par défaut.</span><span class="sxs-lookup"><span data-stu-id="88380-177">Notice that you only had to browse to `localhost:5001/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="88380-178">`Index` méthode par défaut qui sera appelée sur un contrôleur si un nom de méthode n’est pas explicitement spécifié.</span><span class="sxs-lookup"><span data-stu-id="88380-178">`Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span>
* <span data-ttu-id="88380-179">La troisième partie du segment d’URL (`id`) concerne les données de routage.</span><span class="sxs-lookup"><span data-stu-id="88380-179">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="88380-180">Les données d’itinéraire sont expliquées plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="88380-180">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="88380-181">Accédez à : `https://localhost:{PORT}/HelloWorld/Welcome` .</span><span class="sxs-lookup"><span data-stu-id="88380-181">Browse to: `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="88380-182">Remplacez `{PORT}` par votre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="88380-182">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="88380-183">La méthode `Welcome` s’exécute et retourne la chaîne `This is the Welcome action method...`.</span><span class="sxs-lookup"><span data-stu-id="88380-183">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="88380-184">Pour cette URL, le contrôleur est `HelloWorld`, et `Welcome` est la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="88380-184">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="88380-185">Vous n’avez pas encore utilisé la partie `[Parameters]` de l’URL.</span><span class="sxs-lookup"><span data-stu-id="88380-185">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![Fenêtre de navigateur montrant une réponse de la méthode d’action « This is the Welcome action »](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="88380-187">Modifiez le code pour passer des informations sur les paramètres de l’URL au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="88380-187">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="88380-188">Par exemple : `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="88380-188">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span>

<span data-ttu-id="88380-189">Modifiez la méthode `Welcome` en y incluant les deux paramètres, comme indiqué dans le code suivant.</span><span class="sxs-lookup"><span data-stu-id="88380-189">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="88380-190">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="88380-190">The preceding code:</span></span>

* <span data-ttu-id="88380-191">Utilise la fonctionnalité de paramètre facultatif de C# pour indiquer que le paramètre `numTimes` a 1 comme valeur par défaut si aucune valeur n’est passée pour ce paramètre.</span><span class="sxs-lookup"><span data-stu-id="88380-191">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span>
* <span data-ttu-id="88380-192">Utilise `HtmlEncoder.Default.Encode` pour protéger l’application contre les entrées malveillantes, par exemple à l’aide de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="88380-192">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input, such as through JavaScript.</span></span>
* <span data-ttu-id="88380-193">Utilise des [chaînes interpolées](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) dans `$"Hello {name}, NumTimes is: {numTimes}"`.</span><span class="sxs-lookup"><span data-stu-id="88380-193">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span>

<span data-ttu-id="88380-194">Exécutez l’application et accédez à : `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4` .</span><span class="sxs-lookup"><span data-stu-id="88380-194">Run the app and browse to: `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="88380-195">Remplacez `{PORT}` par votre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="88380-195">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="88380-196">Essayez différentes valeurs pour `name` et `numtimes` dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="88380-196">Try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="88380-197">Le système de [liaison de modèle](xref:mvc/models/model-binding) MVC mappe automatiquement les paramètres nommés de la chaîne de requête aux paramètres de la méthode.</span><span class="sxs-lookup"><span data-stu-id="88380-197">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string to parameters in the method.</span></span> <span data-ttu-id="88380-198">Pour plus d’informations, consultez [Liaison de données](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="88380-198">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Fenêtre de navigateur présentant une réponse de l’application Hello Rick, NumTimes est \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="88380-200">Dans l’image précédente :</span><span class="sxs-lookup"><span data-stu-id="88380-200">In the previous image:</span></span>

* <span data-ttu-id="88380-201">Le segment d’URL `Parameters` n’est pas utilisé.</span><span class="sxs-lookup"><span data-stu-id="88380-201">The URL segment `Parameters` isn't used.</span></span>
* <span data-ttu-id="88380-202">Les `name` `numTimes` paramètres et sont passés dans la [chaîne de requête](https://wikipedia.org/wiki/Query_string).</span><span class="sxs-lookup"><span data-stu-id="88380-202">The `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span>
* <span data-ttu-id="88380-203">Le `?` point d’interrogation dans l’URL ci-dessus est un séparateur, et la chaîne de requête suit.</span><span class="sxs-lookup"><span data-stu-id="88380-203">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span>
* <span data-ttu-id="88380-204">Le `&` caractère sépare les paires champ-valeur.</span><span class="sxs-lookup"><span data-stu-id="88380-204">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="88380-205">Remplacez la méthode `Welcome` par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="88380-205">Replace the `Welcome` method with the following code:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="88380-206">Exécutez l’application et entrez l’URL suivante : `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="88380-206">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="88380-207">Dans l’URL précédente :</span><span class="sxs-lookup"><span data-stu-id="88380-207">In the preceding URL:</span></span>

* <span data-ttu-id="88380-208">Le troisième segment d’URL correspondait au paramètre d’itinéraire `id` .</span><span class="sxs-lookup"><span data-stu-id="88380-208">The third URL segment matched the route parameter `id`.</span></span> 
* <span data-ttu-id="88380-209">La méthode `Welcome` contient un paramètre `id` qui correspondait au modèle d’URL de la méthode `MapControllerRoute`.</span><span class="sxs-lookup"><span data-stu-id="88380-209">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="88380-210">Le `?` de fin (dans `id?`) indique que le paramètre `id` est facultatif.</span><span class="sxs-lookup"><span data-stu-id="88380-210">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?name=snippet_route&highlight=5)]

<span data-ttu-id="88380-211">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="88380-211">In the preceding example:</span></span>

* <span data-ttu-id="88380-212">Le troisième segment d’URL correspondait au paramètre d’itinéraire `id` .</span><span class="sxs-lookup"><span data-stu-id="88380-212">The third URL segment matched the route parameter `id`.</span></span>
* <span data-ttu-id="88380-213">La méthode `Welcome` contient un paramètre `id` qui correspondait au modèle d’URL de la méthode `MapControllerRoute`.</span><span class="sxs-lookup"><span data-stu-id="88380-213">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="88380-214">Le `?` de fin (dans `id?`) indique que le paramètre `id` est facultatif.</span><span class="sxs-lookup"><span data-stu-id="88380-214">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="88380-215">[Précédent : prise en main](start-mvc.md) 
>  [Suivant : ajouter une vue](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="88380-215">[Previous: Get Started](start-mvc.md)
[Next: Add a View](adding-view.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="88380-216">Le modèle d’architecture MVC (Model-View-Controller) sépare une application en trois composants principaux : **M** odèle, **V** ue et **C** ontrôleur.</span><span class="sxs-lookup"><span data-stu-id="88380-216">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="88380-217">Le modèle MVC vous permet de créer des applications qui sont plus faciles à tester et à mettre à jour que les applications monolithiques traditionnelles.</span><span class="sxs-lookup"><span data-stu-id="88380-217">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="88380-218">Les applications basées sur MVC contiennent :</span><span class="sxs-lookup"><span data-stu-id="88380-218">MVC-based apps contain:</span></span>

* <span data-ttu-id="88380-219">Des **M** odèles : des classes qui représentent les données de l’application.</span><span class="sxs-lookup"><span data-stu-id="88380-219">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="88380-220">Les classes du modèle utilisent une logique de validation pour appliquer des règles d’entreprise à ces données.</span><span class="sxs-lookup"><span data-stu-id="88380-220">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="88380-221">En règle générale, les objets du modèle récupèrent et stockent l’état du modèle dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="88380-221">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="88380-222">Dans ce didacticiel, un modèle `Movie` récupère les données des films dans une base de données, les fournit à la vue ou les met à jour.</span><span class="sxs-lookup"><span data-stu-id="88380-222">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="88380-223">Les données mises à jour sont écrites dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="88380-223">Updated data is written to a database.</span></span>

* <span data-ttu-id="88380-224">**V** ues : les vues sont les composants qui affichent l’interface utilisateur de l’application.</span><span class="sxs-lookup"><span data-stu-id="88380-224">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="88380-225">En règle générale, cette interface utilisateur affiche les données du modèle.</span><span class="sxs-lookup"><span data-stu-id="88380-225">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="88380-226">**C** ontrôleurs : les classes qui gèrent les demandes du navigateur.</span><span class="sxs-lookup"><span data-stu-id="88380-226">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="88380-227">Ils récupèrent les données du modèle et appellent les modèles de vue qui retournent une réponse.</span><span class="sxs-lookup"><span data-stu-id="88380-227">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="88380-228">Dans une application MVC, la vue affiche seulement les informations ; le contrôleur gère et répond aux entrées et aux interactions de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="88380-228">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="88380-229">Par exemple, le contrôleur gère les valeurs des données de routage et des chaînes de requête, et passe ces valeurs au modèle.</span><span class="sxs-lookup"><span data-stu-id="88380-229">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="88380-230">Le modèle peut utiliser ces valeurs pour interroger la base de données.</span><span class="sxs-lookup"><span data-stu-id="88380-230">The model might use these values to query the database.</span></span> <span data-ttu-id="88380-231">Par exemple, `https://localhost:5001/Home/About` a comme données de routage `Home` (le contrôleur) et `About` (la méthode d’action à appeler sur le contrôleur Home).</span><span class="sxs-lookup"><span data-stu-id="88380-231">For example, `https://localhost:5001/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="88380-232">`https://localhost:5001/Movies/Edit/5` est une demande de modification du film avec l’ID 5 à l’aide du contrôleur movie.</span><span class="sxs-lookup"><span data-stu-id="88380-232">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="88380-233">Les données d’itinéraire sont expliquées plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="88380-233">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="88380-234">Le modèle MVC vous permet de créer des applications qui séparent les différents aspects de l’application (logique d’entrée, logique métier et logique de l’interface utilisateur), tout en assurant un couplage faible entre ces éléments.</span><span class="sxs-lookup"><span data-stu-id="88380-234">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="88380-235">Le modèle spécifie l’emplacement de chaque type de logique dans l’application.</span><span class="sxs-lookup"><span data-stu-id="88380-235">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="88380-236">La logique de l’interface utilisateur appartient à la vue.</span><span class="sxs-lookup"><span data-stu-id="88380-236">The UI logic belongs in the view.</span></span> <span data-ttu-id="88380-237">La logique d’entrée appartient au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="88380-237">Input logic belongs in the controller.</span></span> <span data-ttu-id="88380-238">La logique métier appartient au modèle.</span><span class="sxs-lookup"><span data-stu-id="88380-238">Business logic belongs in the model.</span></span> <span data-ttu-id="88380-239">Cette séparation vous aide à gérer la complexité quand vous créez une application, car elle vous permet de travailler sur un aspect de l’implémentation à la fois, sans impacter le code d’un autre aspect.</span><span class="sxs-lookup"><span data-stu-id="88380-239">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="88380-240">Par exemple, vous pouvez travailler sur le code des vues de façon indépendante du code de la logique métier.</span><span class="sxs-lookup"><span data-stu-id="88380-240">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="88380-241">Nous présentons ces concepts dans cette série de didacticiels et nous vous montrons comment les utiliser pour créer une application de gestion de films.</span><span class="sxs-lookup"><span data-stu-id="88380-241">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="88380-242">Le projet MVC contient des dossiers pour les *contrôleurs* et pour les *vues*.</span><span class="sxs-lookup"><span data-stu-id="88380-242">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="88380-243">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="88380-243">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="88380-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="88380-244">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="88380-245">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur **contrôleurs > ajouter un contrôleur de >**</span><span class="sxs-lookup"><span data-stu-id="88380-245">In **Solution Explorer**, right-click **Controllers > Add > Controller**</span></span>

  ![Menu contextuel](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)

* <span data-ttu-id="88380-247">Dans la boîte de dialogue **Ajouter un modèle automatique**, sélectionnez **Contrôleur MVC - vide**</span><span class="sxs-lookup"><span data-stu-id="88380-247">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![Ajouter un contrôleur MVC et le nommer](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* <span data-ttu-id="88380-249">Dans la **boîte de dialogue Ajouter un contrôleur MVC vide**, entrez **HelloWorldController** et sélectionnez **AJOUTER**.</span><span class="sxs-lookup"><span data-stu-id="88380-249">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="88380-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="88380-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="88380-251">Sélectionnez l’icône **EXPLORATEUR** et cliquez en maintenant enfoncée la touche Ctrl (ou cliquez avec le bouton droit) sur **Contrôleurs > Nouveau fichier** et nommez le nouveau fichier *HelloWorldController.cs*.</span><span class="sxs-lookup"><span data-stu-id="88380-251">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![Menu contextuel](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="88380-253">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="88380-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="88380-254">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Contrôleurs > Ajouter > Nouveau fichier**.</span><span class="sxs-lookup"><span data-stu-id="88380-254">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![Menu contextuel](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="88380-256">Sélectionnez **ASP.NET Core** et **Classe de contrôleur MVC**.</span><span class="sxs-lookup"><span data-stu-id="88380-256">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="88380-257">Nommez le contrôleur **HelloWorldController**.</span><span class="sxs-lookup"><span data-stu-id="88380-257">Name the controller **HelloWorldController**.</span></span>

![Ajouter un contrôleur MVC et le nommer](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="88380-259">Remplacez le contenu de *Controllers/HelloWorldController.cs* par ceci :</span><span class="sxs-lookup"><span data-stu-id="88380-259">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="88380-260">Chaque méthode `public` d’un contrôleur peut être appelée en tant que point de terminaison HTTP.</span><span class="sxs-lookup"><span data-stu-id="88380-260">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="88380-261">Dans l’exemple ci-dessus, les deux méthodes retournent une chaîne.</span><span class="sxs-lookup"><span data-stu-id="88380-261">In the sample above, both methods return a string.</span></span> <span data-ttu-id="88380-262">Notez les commentaires qui précèdent chaque méthode.</span><span class="sxs-lookup"><span data-stu-id="88380-262">Note the comments preceding each method.</span></span>

<span data-ttu-id="88380-263">Un point de terminaison HTTP est une URL qui peut être ciblée dans l’application web, comme `https://localhost:5001/HelloWorld`, et qui combine le protocole utilisé : `HTTPS`, l’emplacement réseau du serveur web (avec le port TCP) : `localhost:5001` et l’URI cible `HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="88380-263">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="88380-264">Le premier commentaire indique qu’il s’agit d’une méthode [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) qui est appelée en ajoutant `/HelloWorld/` à l’URL de base.</span><span class="sxs-lookup"><span data-stu-id="88380-264">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="88380-265">Le deuxième commentaire indique une méthode [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) qui est appelée en ajoutant `/HelloWorld/Welcome/` à l’URL.</span><span class="sxs-lookup"><span data-stu-id="88380-265">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="88380-266">Plus loin dans ce didacticiel, le moteur de génération de modèles automatique est utilisé pour générer des méthodes `HTTP POST` qui mettent à jour des données.</span><span class="sxs-lookup"><span data-stu-id="88380-266">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="88380-267">Exécutez l’application en mode de non-débogage et ajoutez « HelloWorld » au chemin dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="88380-267">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="88380-268">La méthode `Index` retourne une chaîne.</span><span class="sxs-lookup"><span data-stu-id="88380-268">The `Index` method returns a string.</span></span>

![Fenêtre de navigateur montrant une réponse de l’application à l’action This is my default](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="88380-270">MVC appelle les classes du contrôleur (et les méthodes d’action au sein de celles-ci) en fonction de l’URL entrante.</span><span class="sxs-lookup"><span data-stu-id="88380-270">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="88380-271">La [logique de routage d’URL](xref:mvc/controllers/routing) par défaut utilisée par le modèle MVC utilise un format comme celui-ci pour déterminer le code à appeler :</span><span class="sxs-lookup"><span data-stu-id="88380-271">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="88380-272">Le format de routage est défini dans la méthode `Configure` du fichier *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="88380-272">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="88380-273">Quand vous naviguez jusqu’à l’application et que vous ne fournissez aucun segment d’URL, sa valeur par défaut est le contrôleur « Home » et la méthode « Index » spécifiée dans la ligne du modèle mise en surbrillance ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="88380-273">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="88380-274">Le premier segment d’URL détermine la classe du contrôleur à exécuter.</span><span class="sxs-lookup"><span data-stu-id="88380-274">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="88380-275">Ainsi, `localhost:{PORT}/HelloWorld` est mappé à la classe `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="88380-275">So `localhost:{PORT}/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="88380-276">La seconde partie du segment d’URL détermine la méthode d’action sur la classe.</span><span class="sxs-lookup"><span data-stu-id="88380-276">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="88380-277">Ainsi, `localhost:{PORT}/HelloWorld/Index` provoque l’exécution de la méthode `Index` de la classe `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="88380-277">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="88380-278">Notez que vous n’avez eu qu’à accéder à `localhost:{PORT}/HelloWorld` pour que la méthode `Index` soit appelée par défaut.</span><span class="sxs-lookup"><span data-stu-id="88380-278">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="88380-279">La raison en est que `Index` est la méthode par défaut qui est appelée sur un contrôleur si un nom de méthode n’est pas explicitement spécifié.</span><span class="sxs-lookup"><span data-stu-id="88380-279">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="88380-280">La troisième partie du segment d’URL (`id`) concerne les données de routage.</span><span class="sxs-lookup"><span data-stu-id="88380-280">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="88380-281">Les données d’itinéraire sont expliquées plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="88380-281">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="88380-282">Accédez à `https://localhost:{PORT}/HelloWorld/Welcome`.</span><span class="sxs-lookup"><span data-stu-id="88380-282">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="88380-283">La méthode `Welcome` s’exécute et retourne la chaîne `This is the Welcome action method...`.</span><span class="sxs-lookup"><span data-stu-id="88380-283">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="88380-284">Pour cette URL, le contrôleur est `HelloWorld`, et `Welcome` est la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="88380-284">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="88380-285">Vous n’avez pas encore utilisé la partie `[Parameters]` de l’URL.</span><span class="sxs-lookup"><span data-stu-id="88380-285">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![Fenêtre de navigateur montrant une réponse de la méthode d’action « This is the Welcome action »](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="88380-287">Modifiez le code pour passer des informations sur les paramètres de l’URL au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="88380-287">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="88380-288">Par exemple : `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="88380-288">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="88380-289">Modifiez la méthode `Welcome` en y incluant les deux paramètres, comme indiqué dans le code suivant.</span><span class="sxs-lookup"><span data-stu-id="88380-289">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="88380-290">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="88380-290">The preceding code:</span></span>

* <span data-ttu-id="88380-291">Utilise la fonctionnalité de paramètre facultatif de C# pour indiquer que le paramètre `numTimes` a 1 comme valeur par défaut si aucune valeur n’est passée pour ce paramètre.</span><span class="sxs-lookup"><span data-stu-id="88380-291">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="88380-292">Utilise `HtmlEncoder.Default.Encode` pour protéger l’application des entrées malveillantes (notamment en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="88380-292">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="88380-293">Utilise des [chaînes interpolées](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) dans `$"Hello {name}, NumTimes is: {numTimes}"`.</span><span class="sxs-lookup"><span data-stu-id="88380-293">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="88380-294">Exécutez l’application et accédez à :</span><span class="sxs-lookup"><span data-stu-id="88380-294">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="88380-295">(Remplacez `{PORT}` par votre numéro de port.) Vous pouvez essayer différentes valeurs pour `name` et `numtimes` dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="88380-295">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="88380-296">Le système de [liaison de données](xref:mvc/models/model-binding) du modèle MVC mappe automatiquement les paramètres nommés provenant de la chaîne de requête dans la barre d’adresse aux paramètres de votre méthode.</span><span class="sxs-lookup"><span data-stu-id="88380-296">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="88380-297">Pour plus d’informations, consultez [Liaison de données](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="88380-297">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Fenêtre de navigateur présentant une réponse de l’application Hello Rick, NumTimes est \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="88380-299">Dans l’image ci-dessus, le segment d’URL ( `Parameters` ) n’est pas utilisé, les `name` `numTimes` paramètres et sont passés dans la [chaîne de requête](https://wikipedia.org/wiki/Query_string).</span><span class="sxs-lookup"><span data-stu-id="88380-299">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="88380-300">Le `?` point d’interrogation dans l’URL ci-dessus est un séparateur, et la chaîne de requête suit.</span><span class="sxs-lookup"><span data-stu-id="88380-300">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="88380-301">Le `&` caractère sépare les paires champ-valeur.</span><span class="sxs-lookup"><span data-stu-id="88380-301">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="88380-302">Remplacez la méthode `Welcome` par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="88380-302">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="88380-303">Exécutez l’application et entrez l’URL suivante : `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="88380-303">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="88380-304">Cette fois, le troisième segment de l’URL correspondait au paramètre de routage `id`.</span><span class="sxs-lookup"><span data-stu-id="88380-304">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="88380-305">La méthode `Welcome` contient un paramètre `id` qui correspondait au modèle d’URL de la méthode `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="88380-305">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="88380-306">Le `?` de fin (dans `id?`) indique que le paramètre `id` est facultatif.</span><span class="sxs-lookup"><span data-stu-id="88380-306">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="88380-307">Dans ces exemples, le contrôleur a fait la partie « VC » du modèle MVC, autrement dit, la vue et le contrôleur fonctionnent.</span><span class="sxs-lookup"><span data-stu-id="88380-307">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="88380-308">Le contrôleur retourne directement du HTML.</span><span class="sxs-lookup"><span data-stu-id="88380-308">The controller is returning HTML directly.</span></span> <span data-ttu-id="88380-309">En règle générale, vous ne souhaitez pas que les contrôleurs retournent directement du HTML, car le codage et la maintenance deviennent dans ce cas très laborieux.</span><span class="sxs-lookup"><span data-stu-id="88380-309">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="88380-310">Au lieu de cela, vous utilisez généralement un Razor fichier de modèle de vue distinct pour faciliter la génération de la réponse html.</span><span class="sxs-lookup"><span data-stu-id="88380-310">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="88380-311">Vous faites cela dans le didacticiel suivant.</span><span class="sxs-lookup"><span data-stu-id="88380-311">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="88380-312">[Précédent](start-mvc.md) 
>  [Suivant](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="88380-312">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end
