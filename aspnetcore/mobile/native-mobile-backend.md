---
title: Créer des services backend pour les applications mobiles natives avec ASP.NET Core
author: ardalis
description: Découvrez comment créer des services backend en utilisant ASP.NET Core MVC pour prendre en charge des applications mobiles natives.
ms.author: riande
ms.date: 12/05/2019
no-loc:
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: 0bbf740cb49b77b476e7e015afee311110bbe5ea
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060987"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="57554-103">Créer des services backend pour les applications mobiles natives avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57554-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="57554-104">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="57554-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="57554-105">Les applications mobiles peuvent communiquer avec les services back-end ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57554-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="57554-106">Pour obtenir des instructions sur la connexion de services web locaux à partir de simulateurs iOS et d’émulateurs Android, consultez [Se connecter à des services web locaux à partir de simulateurs iOS et d’émulateurs Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span><span class="sxs-lookup"><span data-stu-id="57554-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="57554-107">Afficher ou télécharger l’exemple de code de services backend</span><span class="sxs-lookup"><span data-stu-id="57554-107">View or download sample backend services code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="57554-108">Exemple d’application mobile native</span><span class="sxs-lookup"><span data-stu-id="57554-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="57554-109">Ce didacticiel montre comment créer des services backend en utilisant ASP.NET Core MVC pour prendre en charge des applications mobiles natives.</span><span class="sxs-lookup"><span data-stu-id="57554-109">This tutorial demonstrates how to create backend services using ASP.NET Core MVC to support native mobile apps.</span></span> <span data-ttu-id="57554-110">Il utilise [l’application Xamarin.Forms TodoREST](/xamarin/xamarin-forms/data-cloud/consuming/rest) comme client natif, qui inclut des clients natifs distincts pour les appareils Android, iOS, Windows universel et Windows Phone.</span><span class="sxs-lookup"><span data-stu-id="57554-110">It uses the [Xamarin Forms ToDoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, Windows Universal, and Window Phone devices.</span></span> <span data-ttu-id="57554-111">Vous pouvez suivre le didacticiel lié pour créer l’application native (et installer les outils Xamarin gratuits nécessaires), ainsi que télécharger l’exemple de solution Xamarin.</span><span class="sxs-lookup"><span data-stu-id="57554-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="57554-112">L’exemple Xamarin inclut un projet de services ASP.NET API web 2, que l’application ASP.NET Core de cet article remplace (sans que des modifications soient requises par le client).</span><span class="sxs-lookup"><span data-stu-id="57554-112">The Xamarin sample includes an ASP.NET Web API 2 services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Application TodoREST s’exécutant sur un smartphone Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="57554-114">Fonctionnalités</span><span class="sxs-lookup"><span data-stu-id="57554-114">Features</span></span>

<span data-ttu-id="57554-115">L’application TodoREST prend en charge l’affichage, l’ajout, la suppression et la mise à jour d’éléments de tâche à effectuer.</span><span class="sxs-lookup"><span data-stu-id="57554-115">The ToDoRest app supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="57554-116">Chaque élément a un ID, un nom, des notes et une propriété qui indique si elle est déjà effectuée.</span><span class="sxs-lookup"><span data-stu-id="57554-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="57554-117">La vue principale des éléments, reproduite ci-dessus, montre le nom de chaque élément et indique si la tâche est effectuée avec une marque.</span><span class="sxs-lookup"><span data-stu-id="57554-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="57554-118">Le fait d’appuyer sur l'icône`+` ouvre une boîte de dialogue permettant l’ajout d’un élément :</span><span class="sxs-lookup"><span data-stu-id="57554-118">Tapping the `+` icon opens an add item dialog:</span></span>

![Boîte de dialogue pour ajouter un élément](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="57554-120">Le fait de cliquer sur un élément de l’écran de la liste principale ouvre une boîte de dialogue où les valeurs pour Name, Notes et Done peuvent être modifiées, et où vous pouvez supprimer l’élément :</span><span class="sxs-lookup"><span data-stu-id="57554-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Boîte de dialogue pour modifier un élément](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="57554-122">Cet exemple est configuré par défaut pour utiliser les services backend hébergés sur developer.xamarin.com, qui autorisent des opérations en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="57554-122">This sample is configured by default to use backend services hosted at developer.xamarin.com, which allow read-only operations.</span></span> <span data-ttu-id="57554-123">Pour le tester vous-même par rapport à l’application ASP.NET Core créée dans la section suivante et exécuté sur votre ordinateur, vous devez mettre à jour la constante `RestUrl` de l’application.</span><span class="sxs-lookup"><span data-stu-id="57554-123">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, you'll need to update the app's `RestUrl` constant.</span></span> <span data-ttu-id="57554-124">Accédez au projet `ToDoREST` et ouvrez le fichier *Constants.cs* .</span><span class="sxs-lookup"><span data-stu-id="57554-124">Navigate to the `ToDoREST` project and open the *Constants.cs* file.</span></span> <span data-ttu-id="57554-125">Remplacez `RestUrl` par une URL qui inclut l’adresse IP de votre ordinateur (pas localhost ni 127.0.0.1, car cette adresse est utilisée depuis l’émulateur d’appareil, et non pas depuis votre ordinateur).</span><span class="sxs-lookup"><span data-stu-id="57554-125">Replace the `RestUrl` with a URL that includes your machine's IP address (not localhost or 127.0.0.1, since this address is used from the device emulator, not from your machine).</span></span> <span data-ttu-id="57554-126">Incluez également le numéro de port (5000).</span><span class="sxs-lookup"><span data-stu-id="57554-126">Include the port number as well (5000).</span></span> <span data-ttu-id="57554-127">Pour tester que vos services fonctionnent avec un appareil, vérifiez que vous n’avez pas un pare-feu actif bloquant l’accès à ce port.</span><span class="sxs-lookup"><span data-stu-id="57554-127">In order to test that your services work with a device, ensure you don't have an active firewall blocking access to this port.</span></span>

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="57554-128">Création du projet ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57554-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="57554-129">réez une application web ASP.NET Core dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="57554-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="57554-130">Choisissez le modèle "API web" et Pas d’authentification.</span><span class="sxs-lookup"><span data-stu-id="57554-130">Choose the Web API template and No Authentication.</span></span> <span data-ttu-id="57554-131">Nommez le projet *ToDoApi* .</span><span class="sxs-lookup"><span data-stu-id="57554-131">Name the project *ToDoApi* .</span></span>

![Boîte de dialogue Nouvelle application web ASP.NET avec le modèle de projet API web sélectionné](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="57554-133">L’application doit répondre à toutes les demandes adressées au port 5000.</span><span class="sxs-lookup"><span data-stu-id="57554-133">The application should respond to all requests made to port 5000.</span></span> <span data-ttu-id="57554-134">Pour cela, mettez à jour *Program.cs* en y incluant `.UseUrls("http://*:5000")` :</span><span class="sxs-lookup"><span data-stu-id="57554-134">Update *Program.cs* to include `.UseUrls("http://*:5000")` to achieve this:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> <span data-ttu-id="57554-135">Vérifiez que vous exécutez l’application directement et non pas derrière IIS Express, qui ignore par défaut les demandes non locales.</span><span class="sxs-lookup"><span data-stu-id="57554-135">Make sure you run the application directly, rather than behind IIS Express, which ignores non-local requests by default.</span></span> <span data-ttu-id="57554-136">Exécutez [dotnet run](/dotnet/core/tools/dotnet-run) à partir d’une invite de commandes, ou choisissez le profil du nom d’application dans la liste déroulante Cible de débogage dans la barre d’outils de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="57554-136">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the application name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="57554-137">Ajoutez une classe de modèle pour représenter des éléments de tâche à effectuer.</span><span class="sxs-lookup"><span data-stu-id="57554-137">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="57554-138">Marquez les champs obligatoires avec l' `[Required]` attribut :</span><span class="sxs-lookup"><span data-stu-id="57554-138">Mark required fields with the `[Required]` attribute:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

<span data-ttu-id="57554-139">Les méthodes d’API requièrent un moyen d’utiliser des données.</span><span class="sxs-lookup"><span data-stu-id="57554-139">The API methods require some way to work with data.</span></span> <span data-ttu-id="57554-140">Utilisez la même interface `IToDoRepository` que celle utilisée par l’exemple Xamarin d’origine :</span><span class="sxs-lookup"><span data-stu-id="57554-140">Use the same `IToDoRepository` interface the original Xamarin sample uses:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

<span data-ttu-id="57554-141">Pour cet exemple, l’implémentation utilise simplement une collection privée d’éléments :</span><span class="sxs-lookup"><span data-stu-id="57554-141">For this sample, the implementation just uses a private collection of items:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

<span data-ttu-id="57554-142">Configurez l’implémentation dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="57554-142">Configure the implementation in *Startup.cs* :</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

<span data-ttu-id="57554-143">À ce stade, vous êtes prêt à créer le *ToDoItemsController* .</span><span class="sxs-lookup"><span data-stu-id="57554-143">At this point, you're ready to create the *ToDoItemsController* .</span></span>

> [!TIP]
> <span data-ttu-id="57554-144">Découvrez plus en détail comment créer des API web dans [Créer votre première API web avec ASP.NET Core MVC et Visual Studio](../tutorials/first-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="57554-144">Learn more about creating web APIs in [Build your first Web API with ASP.NET Core MVC and Visual Studio](../tutorials/first-web-api.md).</span></span>

## <a name="creating-the-controller"></a><span data-ttu-id="57554-145">Création du contrôleur</span><span class="sxs-lookup"><span data-stu-id="57554-145">Creating the Controller</span></span>

<span data-ttu-id="57554-146">Ajoutez un nouveau contrôleur au projet, *ToDoItemsController* .</span><span class="sxs-lookup"><span data-stu-id="57554-146">Add a new controller to the project, *ToDoItemsController* .</span></span> <span data-ttu-id="57554-147">Il doit hériter de Microsoft.AspNetCore.Mvc.Controller.</span><span class="sxs-lookup"><span data-stu-id="57554-147">It should inherit from Microsoft.AspNetCore.Mvc.Controller.</span></span> <span data-ttu-id="57554-148">Ajoutez un attribut `Route` pour indiquer que le contrôleur gère les demandes effectuées via des chemins commençant par `api/todoitems`.</span><span class="sxs-lookup"><span data-stu-id="57554-148">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="57554-149">Le jeton `[controller]` de la route est remplacé par le nom du contrôleur (en omettant le suffixe `Controller`) et est particulièrement pratique pour les routes globales.</span><span class="sxs-lookup"><span data-stu-id="57554-149">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="57554-150">Découvrez plus d’informations sur le [routage](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="57554-150">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="57554-151">Le contrôleur nécessite un `IToDoRepository` pour fonctionner ; demandez une instance de ce type via le constructeur du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="57554-151">The controller requires an `IToDoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="57554-152">À l’exécution, cette instance est fournie via la prise en charge par l’infrastructure de [l’injection de dépendances](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="57554-152">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

<span data-ttu-id="57554-153">Cette API prend en charge quatre verbes HTTP différents pour effectuer des opérations CRUD (création, lecture, mise à jour, suppression) sur la source de données.</span><span class="sxs-lookup"><span data-stu-id="57554-153">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="57554-154">La plus simple d’entre elles est l’opération de lecture, qui correspond à une requête HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="57554-154">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="57554-155">Lecture d’éléments</span><span class="sxs-lookup"><span data-stu-id="57554-155">Reading Items</span></span>

<span data-ttu-id="57554-156">Demander une liste d’éléments se fait via une requête GET à la méthode `List`.</span><span class="sxs-lookup"><span data-stu-id="57554-156">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="57554-157">L’attribut `[HttpGet]` sur la méthode `List` indique que cette action doit gérer seulement les requêtes GET.</span><span class="sxs-lookup"><span data-stu-id="57554-157">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="57554-158">La route pour cette action est la route spécifiée sur le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="57554-158">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="57554-159">Le nom de l’action ne doit pas nécessairement constituer une partie de la route.</span><span class="sxs-lookup"><span data-stu-id="57554-159">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="57554-160">Il vous suffit de faire en sorte que chaque action ait une route unique et non ambiguë.</span><span class="sxs-lookup"><span data-stu-id="57554-160">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="57554-161">Des attributs de routage peuvent être appliqués aux niveaux du contrôleur et des méthodes pour créer des routes spécifiques.</span><span class="sxs-lookup"><span data-stu-id="57554-161">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

<span data-ttu-id="57554-162">La méthode `List` retourne un code de réponse 200 OK et tous les éléments de tâche à effectuer, sérialisés au format JSON.</span><span class="sxs-lookup"><span data-stu-id="57554-162">The `List` method returns a 200 OK response code and all of the ToDo items, serialized as JSON.</span></span>

<span data-ttu-id="57554-163">Vous pouvez tester votre nouvelle méthode d’API via différents outils, comme [Postman](https://www.getpostman.com/docs/), qui est montré ici :</span><span class="sxs-lookup"><span data-stu-id="57554-163">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Console de Postman montrant une requête GET pour les éléments de tâche à effectuer (todoitems) et le corps de la réponse montrant le JSON pour les trois éléments retournés](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="57554-165">Création d’éléments</span><span class="sxs-lookup"><span data-stu-id="57554-165">Creating Items</span></span>

<span data-ttu-id="57554-166">Par convention, la création d’éléments de données est mappée au verbe HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="57554-166">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="57554-167">`Create`Un `[HttpPost]` attribut est appliqué à la méthode et accepte une `ToDoItem` instance.</span><span class="sxs-lookup"><span data-stu-id="57554-167">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `ToDoItem` instance.</span></span> <span data-ttu-id="57554-168">Étant donné que l' `item` argument est passé dans le corps de la publication, ce paramètre spécifie l' `[FromBody]` attribut.</span><span class="sxs-lookup"><span data-stu-id="57554-168">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="57554-169">À l’intérieur de la méthode, la validité et l’existence préalable de l’élément dans le magasin de données sont vérifiées et, si aucun problème ne se produit, il est ajouté via le référentiel.</span><span class="sxs-lookup"><span data-stu-id="57554-169">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="57554-170">La vérification `ModelState.IsValid` effectue la [validation du modèle](../mvc/models/validation.md) et doit être effectuée dans chaque méthode d’API qui accepte une entrée utilisateur.</span><span class="sxs-lookup"><span data-stu-id="57554-170">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

<span data-ttu-id="57554-171">L’exemple utilise une énumération contenant les codes d’erreur qui sont passés au client mobile :</span><span class="sxs-lookup"><span data-stu-id="57554-171">The sample uses an enum containing error codes that are passed to the mobile client:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

<span data-ttu-id="57554-172">Testez en ajoutant de nouveaux éléments avec Postman, en choisissant le verbe POST qui fournit le nouvel objet au format JSON dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="57554-172">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="57554-173">Vous devez également ajouter un en-tête de requête spécifiant un `Content-Type` de `application/json`.</span><span class="sxs-lookup"><span data-stu-id="57554-173">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Console de Postman montrant un POST et la réponse](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="57554-175">La méthode retourne l’élément qui vient d’être créé dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="57554-175">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="57554-176">Mise à jour d’éléments</span><span class="sxs-lookup"><span data-stu-id="57554-176">Updating Items</span></span>

<span data-ttu-id="57554-177">La modification des enregistrements est effectuée via des requêtes HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="57554-177">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="57554-178">Outre cette modification, la méthode `Edit` est presque identique à `Create`.</span><span class="sxs-lookup"><span data-stu-id="57554-178">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="57554-179">Notez que si l’enregistrement n’est pas trouvé, l’action `Edit` retourne une réponse `NotFound` (404).</span><span class="sxs-lookup"><span data-stu-id="57554-179">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

<span data-ttu-id="57554-180">Pour tester avec Postman, changez le verbe en PUT.</span><span class="sxs-lookup"><span data-stu-id="57554-180">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="57554-181">Spécifiez les données de l’objet mis à jour dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="57554-181">Specify the updated object data in the Body of the request.</span></span>

![Console de Postman montrant un PUT et la réponse](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="57554-183">Cette méthode retourne une réponse `NoContent` (204) en cas de réussite, pour des raisons de cohérence avec l’API préexistante.</span><span class="sxs-lookup"><span data-stu-id="57554-183">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="57554-184">Suppression d’éléments</span><span class="sxs-lookup"><span data-stu-id="57554-184">Deleting Items</span></span>

<span data-ttu-id="57554-185">La suppression d’enregistrements est effectuée via des requêtes DELETE adressées au service, en passant l’ID de l’élément à supprimer.</span><span class="sxs-lookup"><span data-stu-id="57554-185">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="57554-186">Comme pour les mises à jour, les requêtes pour des éléments qui n’existent pas reçoivent des réponses `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="57554-186">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="57554-187">Sinon, une requête qui réussit obtient une réponse `NoContent` (204).</span><span class="sxs-lookup"><span data-stu-id="57554-187">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

<span data-ttu-id="57554-188">Notez que quand vous testez les fonctionnalités de suppression, rien n’est obligatoire dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="57554-188">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Console de Postman montrant un DELETE et la réponse](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a><span data-ttu-id="57554-190">Conventions des API web courantes</span><span class="sxs-lookup"><span data-stu-id="57554-190">Common Web API Conventions</span></span>

<span data-ttu-id="57554-191">Quand vous développez des services backend pour votre application, vous souhaitez obtenir un ensemble cohérent de conventions ou de stratégies pour gérer les problèmes transversaux.</span><span class="sxs-lookup"><span data-stu-id="57554-191">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="57554-192">Par exemple, dans le service montré ci-dessus, les requêtes pour des enregistrements spécifiques qui n’ont pas été trouvés ont reçu une réponse `NotFound` et non pas une réponse `BadRequest`.</span><span class="sxs-lookup"><span data-stu-id="57554-192">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="57554-193">De même, les commandes envoyées à ce service qui ont passé des types liés au modèle ont toujours vérifié `ModelState.IsValid` et retourné un `BadRequest` pour les types de modèle non valide.</span><span class="sxs-lookup"><span data-stu-id="57554-193">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="57554-194">Une fois que vous avez identifié une stratégie commune pour vos API, vous pouvez en général l’encapsuler dans un [filtre](../mvc/controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="57554-194">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="57554-195">Découvrez plus d’informations sur [la façon d’encapsuler des stratégies d’API courantes dans les applications ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span><span class="sxs-lookup"><span data-stu-id="57554-195">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="57554-196">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="57554-196">Additional resources</span></span>

* [<span data-ttu-id="57554-197">Authentification et autorisation</span><span class="sxs-lookup"><span data-stu-id="57554-197">Authentication and Authorization</span></span>](/xamarin/xamarin-forms/enterprise-application-patterns/authentication-and-authorization)
