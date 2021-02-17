---
title: Créer des services backend pour les applications mobiles natives avec ASP.NET Core
author: rick-anderson
description: Découvrez comment créer des services backend en utilisant ASP.NET Core MVC pour prendre en charge des applications mobiles natives.
ms.author: riande
ms.date: 2/12/2021
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564038"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="1f416-103">Créer des services backend pour les applications mobiles natives avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f416-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="1f416-104">Par [James Montemagno](https://twitter.com/JamesMontemagno)</span><span class="sxs-lookup"><span data-stu-id="1f416-104">By [James Montemagno](https://twitter.com/JamesMontemagno)</span></span>

<span data-ttu-id="1f416-105">Les applications mobiles peuvent communiquer avec les services back-end ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f416-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="1f416-106">Pour obtenir des instructions sur la connexion de services web locaux à partir de simulateurs iOS et d’émulateurs Android, consultez [Se connecter à des services web locaux à partir de simulateurs iOS et d’émulateurs Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span><span class="sxs-lookup"><span data-stu-id="1f416-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="1f416-107">Afficher ou télécharger l’exemple de code de services backend</span><span class="sxs-lookup"><span data-stu-id="1f416-107">View or download sample backend services code</span></span>](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="1f416-108">Exemple d’application mobile native</span><span class="sxs-lookup"><span data-stu-id="1f416-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="1f416-109">Ce didacticiel montre comment créer des services principaux à l’aide de ASP.NET Core pour prendre en charge les applications mobiles natives.</span><span class="sxs-lookup"><span data-stu-id="1f416-109">This tutorial demonstrates how to create backend services using ASP.NET Core to support native mobile apps.</span></span> <span data-ttu-id="1f416-110">Elle utilise l' [application Xamarin. Forms TodoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) en tant que client natif, qui comprend des clients natifs distincts pour Android, iOS et Windows.</span><span class="sxs-lookup"><span data-stu-id="1f416-110">It uses the [Xamarin.Forms TodoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, and Windows.</span></span> <span data-ttu-id="1f416-111">Vous pouvez suivre le didacticiel lié pour créer l’application native (et installer les outils Xamarin gratuits nécessaires), ainsi que télécharger l’exemple de solution Xamarin.</span><span class="sxs-lookup"><span data-stu-id="1f416-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="1f416-112">L’exemple Xamarin comprend un projet de services d’API Web ASP.NET Core, que cet article ASP.NET Core application remplace (sans aucune modification requise par le client).</span><span class="sxs-lookup"><span data-stu-id="1f416-112">The Xamarin sample includes an ASP.NET Core Web API services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Application TodoREST s’exécutant sur un smartphone Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="1f416-114">Fonctionnalités</span><span class="sxs-lookup"><span data-stu-id="1f416-114">Features</span></span>

<span data-ttu-id="1f416-115">L' [application TodoREST](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) prend en charge l’affichage, l’ajout, la suppression et la mise à jour des éléments de To-Do.</span><span class="sxs-lookup"><span data-stu-id="1f416-115">The [TodoREST app](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="1f416-116">Chaque élément a un ID, un nom, des notes et une propriété qui indique si elle est déjà effectuée.</span><span class="sxs-lookup"><span data-stu-id="1f416-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="1f416-117">La vue principale des éléments, reproduite ci-dessus, montre le nom de chaque élément et indique si la tâche est effectuée avec une marque.</span><span class="sxs-lookup"><span data-stu-id="1f416-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="1f416-118">Le fait d’appuyer sur l'icône`+` ouvre une boîte de dialogue permettant l’ajout d’un élément :</span><span class="sxs-lookup"><span data-stu-id="1f416-118">Tapping the `+` icon opens an add item dialog:</span></span>

![Boîte de dialogue pour ajouter un élément](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="1f416-120">Le fait de cliquer sur un élément de l’écran de la liste principale ouvre une boîte de dialogue où les valeurs pour Name, Notes et Done peuvent être modifiées, et où vous pouvez supprimer l’élément :</span><span class="sxs-lookup"><span data-stu-id="1f416-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Boîte de dialogue pour modifier un élément](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="1f416-122">Pour tester vous-même l’application ASP.NET Core créée dans la section suivante en cours d’exécution sur votre ordinateur, mettez à jour la constante de l’application [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) .</span><span class="sxs-lookup"><span data-stu-id="1f416-122">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, update the app's [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) constant.</span></span>

<span data-ttu-id="1f416-123">Les émulateurs Android ne s’exécutent pas sur l’ordinateur local et utilisent une adresse IP de bouclage (10.0.2.2) pour communiquer avec l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="1f416-123">Android emulators do not run on the local machine and use a loopback IP (10.0.2.2) to communicate with the local machine.</span></span> <span data-ttu-id="1f416-124">Tirez parti de [Xamarin. Essentials DeviceInfo](/xamarin/essentials/device-information/) pour détecter les opérations exécutées par le système afin d’utiliser l’URL correcte.</span><span class="sxs-lookup"><span data-stu-id="1f416-124">Leverage [Xamarin.Essentials DeviceInfo](/xamarin/essentials/device-information/) to detect what operating the system is running to use the correct URL.</span></span>

<span data-ttu-id="1f416-125">Accédez au [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) projet et ouvrez le [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) fichier.</span><span class="sxs-lookup"><span data-stu-id="1f416-125">Navigate to the [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) project and open the [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) file.</span></span> <span data-ttu-id="1f416-126">Le fichier *constants.cs* contient la configuration suivante.</span><span class="sxs-lookup"><span data-stu-id="1f416-126">The *Constants.cs* file contains the following configuration.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

<span data-ttu-id="1f416-127">Vous pouvez éventuellement déployer le service Web sur un service Cloud tel qu’Azure et mettre à jour le `RestUrl` .</span><span class="sxs-lookup"><span data-stu-id="1f416-127">You can optionally deploy the web service to a cloud service such as Azure and update the `RestUrl`.</span></span>

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="1f416-128">Création du projet ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f416-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="1f416-129">réez une application web ASP.NET Core dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1f416-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="1f416-130">Choisissez le modèle API Web.</span><span class="sxs-lookup"><span data-stu-id="1f416-130">Choose the Web API template.</span></span> <span data-ttu-id="1f416-131">Nommez le projet *TodoAPI*.</span><span class="sxs-lookup"><span data-stu-id="1f416-131">Name the project *TodoAPI*.</span></span>

![Boîte de dialogue Nouvelle application web ASP.NET avec le modèle de projet API web sélectionné](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="1f416-133">L’application doit répondre à toutes les demandes adressées au port 5000, y compris le trafic HTTP en texte clair pour notre client mobile.</span><span class="sxs-lookup"><span data-stu-id="1f416-133">The app should respond to all requests made to port 5000 including clear-text http traffic for our mobile client.</span></span> <span data-ttu-id="1f416-134">Mettez à jour *Startup.cs* de sorte qu’il <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> ne s’exécute pas dans le développement :</span><span class="sxs-lookup"><span data-stu-id="1f416-134">Update *Startup.cs* so <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> doesn't run in development:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> <span data-ttu-id="1f416-135">Exécutez l’application directement, plutôt qu’en arrière-plan IIS Express.</span><span class="sxs-lookup"><span data-stu-id="1f416-135">Run the app directly, rather than behind IIS Express.</span></span> <span data-ttu-id="1f416-136">IIS Express ignore par défaut les demandes non locales.</span><span class="sxs-lookup"><span data-stu-id="1f416-136">IIS Express ignores non-local requests by default.</span></span> <span data-ttu-id="1f416-137">Exécutez [dotnet exécuter](/dotnet/core/tools/dotnet-run) à partir d’une invite de commandes, ou choisissez le profil de nom d’application dans la liste déroulante cible de débogage de la barre d’outils de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1f416-137">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the app name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="1f416-138">Ajoutez une classe de modèle pour représenter des éléments de tâche à effectuer.</span><span class="sxs-lookup"><span data-stu-id="1f416-138">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="1f416-139">Marquez les champs obligatoires avec l' `[Required]` attribut :</span><span class="sxs-lookup"><span data-stu-id="1f416-139">Mark required fields with the `[Required]` attribute:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

<span data-ttu-id="1f416-140">Les méthodes d’API requièrent un moyen d’utiliser des données.</span><span class="sxs-lookup"><span data-stu-id="1f416-140">The API methods require some way to work with data.</span></span> <span data-ttu-id="1f416-141">Utilisez la même interface `ITodoRepository` que celle utilisée par l’exemple Xamarin d’origine :</span><span class="sxs-lookup"><span data-stu-id="1f416-141">Use the same `ITodoRepository` interface the original Xamarin sample uses:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

<span data-ttu-id="1f416-142">Pour cet exemple, l’implémentation utilise simplement une collection privée d’éléments :</span><span class="sxs-lookup"><span data-stu-id="1f416-142">For this sample, the implementation just uses a private collection of items:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

<span data-ttu-id="1f416-143">Configurez l’implémentation dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="1f416-143">Configure the implementation in *Startup.cs*:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a><span data-ttu-id="1f416-144">Création du contrôleur</span><span class="sxs-lookup"><span data-stu-id="1f416-144">Creating the Controller</span></span>

<span data-ttu-id="1f416-145">Ajoutez un nouveau contrôleur au projet, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span><span class="sxs-lookup"><span data-stu-id="1f416-145">Add a new controller to the project, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span></span> <span data-ttu-id="1f416-146">Il doit hériter de <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="1f416-146">It should inherit from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="1f416-147">Ajoutez un attribut `Route` pour indiquer que le contrôleur gère les demandes effectuées via des chemins commençant par `api/todoitems`.</span><span class="sxs-lookup"><span data-stu-id="1f416-147">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="1f416-148">Le jeton `[controller]` de la route est remplacé par le nom du contrôleur (en omettant le suffixe `Controller`) et est particulièrement pratique pour les routes globales.</span><span class="sxs-lookup"><span data-stu-id="1f416-148">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="1f416-149">Découvrez plus d’informations sur le [routage](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="1f416-149">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="1f416-150">Le contrôleur nécessite un `ITodoRepository` pour fonctionner ; demandez une instance de ce type via le constructeur du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1f416-150">The controller requires an `ITodoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="1f416-151">À l’exécution, cette instance est fournie via la prise en charge par l’infrastructure de [l’injection de dépendances](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="1f416-151">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

<span data-ttu-id="1f416-152">Cette API prend en charge quatre verbes HTTP différents pour effectuer des opérations CRUD (création, lecture, mise à jour, suppression) sur la source de données.</span><span class="sxs-lookup"><span data-stu-id="1f416-152">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="1f416-153">La plus simple d’entre elles est l’opération de lecture, qui correspond à une requête HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="1f416-153">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="1f416-154">Lecture d’éléments</span><span class="sxs-lookup"><span data-stu-id="1f416-154">Reading Items</span></span>

<span data-ttu-id="1f416-155">Demander une liste d’éléments se fait via une requête GET à la méthode `List`.</span><span class="sxs-lookup"><span data-stu-id="1f416-155">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="1f416-156">L’attribut `[HttpGet]` sur la méthode `List` indique que cette action doit gérer seulement les requêtes GET.</span><span class="sxs-lookup"><span data-stu-id="1f416-156">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="1f416-157">La route pour cette action est la route spécifiée sur le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1f416-157">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="1f416-158">Le nom de l’action ne doit pas nécessairement constituer une partie de la route.</span><span class="sxs-lookup"><span data-stu-id="1f416-158">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="1f416-159">Il vous suffit de faire en sorte que chaque action ait une route unique et non ambiguë.</span><span class="sxs-lookup"><span data-stu-id="1f416-159">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="1f416-160">Des attributs de routage peuvent être appliqués aux niveaux du contrôleur et des méthodes pour créer des routes spécifiques.</span><span class="sxs-lookup"><span data-stu-id="1f416-160">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

<span data-ttu-id="1f416-161">La `List` méthode retourne un code de réponse 200 OK et tous les éléments todo sérialisés au format JSON.</span><span class="sxs-lookup"><span data-stu-id="1f416-161">The `List` method returns a 200 OK response code and all of the Todo items, serialized as JSON.</span></span>

<span data-ttu-id="1f416-162">Vous pouvez tester votre nouvelle méthode d’API via différents outils, comme [Postman](https://www.getpostman.com/docs/), qui est montré ici :</span><span class="sxs-lookup"><span data-stu-id="1f416-162">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Console de Postman montrant une requête GET pour les éléments de tâche à effectuer (todoitems) et le corps de la réponse montrant le JSON pour les trois éléments retournés](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="1f416-164">Création d’éléments</span><span class="sxs-lookup"><span data-stu-id="1f416-164">Creating Items</span></span>

<span data-ttu-id="1f416-165">Par convention, la création d’éléments de données est mappée au verbe HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="1f416-165">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="1f416-166">`Create`Un `[HttpPost]` attribut est appliqué à la méthode et accepte une `TodoItem` instance.</span><span class="sxs-lookup"><span data-stu-id="1f416-166">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `TodoItem` instance.</span></span> <span data-ttu-id="1f416-167">Étant donné que l' `item` argument est passé dans le corps de la publication, ce paramètre spécifie l' `[FromBody]` attribut.</span><span class="sxs-lookup"><span data-stu-id="1f416-167">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="1f416-168">À l’intérieur de la méthode, la validité et l’existence préalable de l’élément dans le magasin de données sont vérifiées et, si aucun problème ne se produit, il est ajouté via le référentiel.</span><span class="sxs-lookup"><span data-stu-id="1f416-168">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="1f416-169">La vérification `ModelState.IsValid` effectue la [validation du modèle](../mvc/models/validation.md) et doit être effectuée dans chaque méthode d’API qui accepte une entrée utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1f416-169">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

<span data-ttu-id="1f416-170">L’exemple utilise un `enum` code d’erreur contenant qui est transmis au client mobile :</span><span class="sxs-lookup"><span data-stu-id="1f416-170">The sample uses an `enum` containing error codes that are passed to the mobile client:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

<span data-ttu-id="1f416-171">Testez en ajoutant de nouveaux éléments avec Postman, en choisissant le verbe POST qui fournit le nouvel objet au format JSON dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="1f416-171">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="1f416-172">Vous devez également ajouter un en-tête de requête spécifiant un `Content-Type` de `application/json`.</span><span class="sxs-lookup"><span data-stu-id="1f416-172">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Console de Postman montrant un POST et la réponse](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="1f416-174">La méthode retourne l’élément qui vient d’être créé dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="1f416-174">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="1f416-175">Mise à jour d’éléments</span><span class="sxs-lookup"><span data-stu-id="1f416-175">Updating Items</span></span>

<span data-ttu-id="1f416-176">La modification des enregistrements est effectuée via des requêtes HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="1f416-176">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="1f416-177">Outre cette modification, la méthode `Edit` est presque identique à `Create`.</span><span class="sxs-lookup"><span data-stu-id="1f416-177">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="1f416-178">Notez que si l’enregistrement n’est pas trouvé, l’action `Edit` retourne une réponse `NotFound` (404).</span><span class="sxs-lookup"><span data-stu-id="1f416-178">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

<span data-ttu-id="1f416-179">Pour tester avec Postman, changez le verbe en PUT.</span><span class="sxs-lookup"><span data-stu-id="1f416-179">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="1f416-180">Spécifiez les données de l’objet mis à jour dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="1f416-180">Specify the updated object data in the Body of the request.</span></span>

![Console de Postman montrant un PUT et la réponse](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="1f416-182">Cette méthode retourne une réponse `NoContent` (204) en cas de réussite, pour des raisons de cohérence avec l’API préexistante.</span><span class="sxs-lookup"><span data-stu-id="1f416-182">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="1f416-183">Suppression d’éléments</span><span class="sxs-lookup"><span data-stu-id="1f416-183">Deleting Items</span></span>

<span data-ttu-id="1f416-184">La suppression d’enregistrements est effectuée via des requêtes DELETE adressées au service, en passant l’ID de l’élément à supprimer.</span><span class="sxs-lookup"><span data-stu-id="1f416-184">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="1f416-185">Comme pour les mises à jour, les requêtes pour des éléments qui n’existent pas reçoivent des réponses `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="1f416-185">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="1f416-186">Sinon, une requête qui réussit obtient une réponse `NoContent` (204).</span><span class="sxs-lookup"><span data-stu-id="1f416-186">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

<span data-ttu-id="1f416-187">Notez que quand vous testez les fonctionnalités de suppression, rien n’est obligatoire dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="1f416-187">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Console de Postman montrant un DELETE et la réponse](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a><span data-ttu-id="1f416-189">Empêcher la post-validation</span><span class="sxs-lookup"><span data-stu-id="1f416-189">Prevent over-posting</span></span>

<span data-ttu-id="1f416-190">Actuellement, l’exemple d’application expose l' `TodoItem` objet entier.</span><span class="sxs-lookup"><span data-stu-id="1f416-190">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="1f416-191">En général, les applications de production limitent les données entrées et retournées à l’aide d’un sous-ensemble du modèle.</span><span class="sxs-lookup"><span data-stu-id="1f416-191">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="1f416-192">Il existe plusieurs raisons à cela, et la sécurité est essentielle.</span><span class="sxs-lookup"><span data-stu-id="1f416-192">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="1f416-193">Le sous-ensemble d’un modèle est généralement appelé un objet Transfert de données (DTO), un modèle d’entrée ou un modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="1f416-193">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="1f416-194">Le **DTO** est utilisé dans cet article.</span><span class="sxs-lookup"><span data-stu-id="1f416-194">**DTO** is used in this article.</span></span>

<span data-ttu-id="1f416-195">Un DTO peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="1f416-195">A DTO may be used to:</span></span>

* <span data-ttu-id="1f416-196">Empêcher la survalidation.</span><span class="sxs-lookup"><span data-stu-id="1f416-196">Prevent over-posting.</span></span>
* <span data-ttu-id="1f416-197">Masquer les propriétés que les clients ne sont pas censés afficher.</span><span class="sxs-lookup"><span data-stu-id="1f416-197">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="1f416-198">Omettez certaines propriétés afin de réduire la taille de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="1f416-198">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="1f416-199">Aplatir les graphiques d’objets qui contiennent des objets imbriqués.</span><span class="sxs-lookup"><span data-stu-id="1f416-199">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="1f416-200">Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.</span><span class="sxs-lookup"><span data-stu-id="1f416-200">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="1f416-201">Pour illustrer l’approche DTO, consultez [empêcher la survalidation](xref:tutorials/first-web-api#prevent-over-posting)</span><span class="sxs-lookup"><span data-stu-id="1f416-201">To demonstrate the DTO approach, see [Prevent over-posting](xref:tutorials/first-web-api#prevent-over-posting)</span></span>

## <a name="common-web-api-conventions"></a><span data-ttu-id="1f416-202">Conventions des API web courantes</span><span class="sxs-lookup"><span data-stu-id="1f416-202">Common Web API Conventions</span></span>

<span data-ttu-id="1f416-203">Quand vous développez des services backend pour votre application, vous souhaitez obtenir un ensemble cohérent de conventions ou de stratégies pour gérer les problèmes transversaux.</span><span class="sxs-lookup"><span data-stu-id="1f416-203">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="1f416-204">Par exemple, dans le service montré ci-dessus, les requêtes pour des enregistrements spécifiques qui n’ont pas été trouvés ont reçu une réponse `NotFound` et non pas une réponse `BadRequest`.</span><span class="sxs-lookup"><span data-stu-id="1f416-204">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="1f416-205">De même, les commandes envoyées à ce service qui ont passé des types liés au modèle ont toujours vérifié `ModelState.IsValid` et retourné un `BadRequest` pour les types de modèle non valide.</span><span class="sxs-lookup"><span data-stu-id="1f416-205">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="1f416-206">Une fois que vous avez identifié une stratégie commune pour vos API, vous pouvez en général l’encapsuler dans un [filtre](../mvc/controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="1f416-206">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="1f416-207">Découvrez plus d’informations sur [la façon d’encapsuler des stratégies d’API courantes dans les applications ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span><span class="sxs-lookup"><span data-stu-id="1f416-207">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f416-208">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1f416-208">Additional resources</span></span>

- [<span data-ttu-id="1f416-209">Xamarin. Forms : authentification du service Web</span><span class="sxs-lookup"><span data-stu-id="1f416-209">Xamarin.Forms: Web Service Authentication</span></span>](/xamarin/xamarin-forms/data-cloud/authentication/)
- [<span data-ttu-id="1f416-210">Xamarin. Forms : utiliser un service Web RESTful</span><span class="sxs-lookup"><span data-stu-id="1f416-210">Xamarin.Forms: Consume a RESTful Web Service</span></span>](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [<span data-ttu-id="1f416-211">Microsoft Learn : utilisation des services Web REST dans les applications Xamarin</span><span class="sxs-lookup"><span data-stu-id="1f416-211">Microsoft Learn: Consume REST web services in Xamarin Apps</span></span>](/learn/modules/consume-rest-services/)
- [<span data-ttu-id="1f416-212">Microsoft Learn : créer une API Web avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f416-212">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
