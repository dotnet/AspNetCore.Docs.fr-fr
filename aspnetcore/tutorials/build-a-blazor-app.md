---
title: Créer une Blazor application de liste de tâches
author: guardrex
description: Générez une Blazor application pas à pas.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/14/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 87626ff30589de82a04c95634fc0dcbcf2eeac18
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507005"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="a42ea-103">Créer une Blazor application de liste de tâches</span><span class="sxs-lookup"><span data-stu-id="a42ea-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="a42ea-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a42ea-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a42ea-105">Ce didacticiel vous montre comment créer et modifier une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="a42ea-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="a42ea-106">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="a42ea-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a42ea-107">Créer un projet d’application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="a42ea-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="a42ea-108">Modifier les Razor composants</span><span class="sxs-lookup"><span data-stu-id="a42ea-108">Modify Razor components</span></span>
> * <span data-ttu-id="a42ea-109">Utiliser la gestion des événements et la liaison de données dans les composants</span><span class="sxs-lookup"><span data-stu-id="a42ea-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="a42ea-110">Utiliser le routage dans une Blazor application</span><span class="sxs-lookup"><span data-stu-id="a42ea-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="a42ea-111">À la fin de ce didacticiel, vous disposerez d’une application de liste de tâches en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="a42ea-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a42ea-112">Prérequis</span><span class="sxs-lookup"><span data-stu-id="a42ea-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="a42ea-113">Créer une application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="a42ea-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="a42ea-114">Créez une Blazor application nommée `TodoList` dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="a42ea-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="a42ea-115">La commande précédente crée un dossier nommé `TodoList` avec l' `-o|--output` option de stockage de l’application.</span><span class="sxs-lookup"><span data-stu-id="a42ea-115">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="a42ea-116">Le `TodoList` dossier est le *dossier racine* du projet.</span><span class="sxs-lookup"><span data-stu-id="a42ea-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="a42ea-117">Accédez au dossier à l' `TodoList` aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a42ea-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="a42ea-118">Ajoutez un nouveau `Todo` Razor composant à l’application à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a42ea-118">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="a42ea-119">L' `-n|--name` option de la commande précédente spécifie le nom du nouveau Razor composant.</span><span class="sxs-lookup"><span data-stu-id="a42ea-119">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="a42ea-120">Le nouveau composant est créé dans le dossier du projet `Pages` avec l' `-o|--output` option.</span><span class="sxs-lookup"><span data-stu-id="a42ea-120">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="a42ea-121">Razor les noms de fichiers de composant requièrent une première lettre capitalisée.</span><span class="sxs-lookup"><span data-stu-id="a42ea-121">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="a42ea-122">Ouvrez le `Pages` dossier et vérifiez que le `Todo` nom de fichier du composant commence par une lettre majuscule `T` .</span><span class="sxs-lookup"><span data-stu-id="a42ea-122">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="a42ea-123">Le nom de fichier doit être `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="a42ea-123">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="a42ea-124">Ouvrez le `Todo` composant dans n’importe quel éditeur de fichier et ajoutez une `@page` Razor directive en haut du fichier avec une URL relative de `/todo` .</span><span class="sxs-lookup"><span data-stu-id="a42ea-124">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="a42ea-125">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="a42ea-125">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo0.razor?highlight=1)]

   <span data-ttu-id="a42ea-126">Enregistrez le fichier `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="a42ea-126">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="a42ea-127">Ajoutez le composant `Todo` à la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="a42ea-127">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="a42ea-128">Le `NavMenu` composant est utilisé dans la disposition de l’application.</span><span class="sxs-lookup"><span data-stu-id="a42ea-128">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="a42ea-129">Les dispositions sont des composants qui vous permettent d’éviter la duplication de contenu dans une application.</span><span class="sxs-lookup"><span data-stu-id="a42ea-129">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="a42ea-130">Le `NavLink` composant fournit une pile dans l’interface utilisateur de l’application lorsque l’URL du composant est chargée par l’application.</span><span class="sxs-lookup"><span data-stu-id="a42ea-130">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="a42ea-131">Dans la liste non triée ( `<ul>...</ul>` ) du `NavMenu` composant, ajoutez l’élément de liste ( `<li>...</li>` ) et le `NavLink` composant suivants pour le composant `Todo` .</span><span class="sxs-lookup"><span data-stu-id="a42ea-131">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="a42ea-132">Dans `Shared/NavMenu.razor` :</span><span class="sxs-lookup"><span data-stu-id="a42ea-132">In `Shared/NavMenu.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   <span data-ttu-id="a42ea-133">Enregistrez le fichier `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="a42ea-133">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="a42ea-134">Générez et exécutez l’application en exécutant la [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) commande dans l’interface de commande à partir du `TodoList` dossier.</span><span class="sxs-lookup"><span data-stu-id="a42ea-134">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="a42ea-135">Une fois l’application en cours d’exécution, accédez à la nouvelle page todo en sélectionnant le **`Todo`** lien dans la barre de navigation de l’application, qui charge la page à l’adresse `/todo` .</span><span class="sxs-lookup"><span data-stu-id="a42ea-135">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="a42ea-136">Laissez l’application s’exécuter dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="a42ea-136">Leave the app running the command shell.</span></span> <span data-ttu-id="a42ea-137">Chaque fois qu’un fichier est enregistré, l’application est automatiquement reconstruite.</span><span class="sxs-lookup"><span data-stu-id="a42ea-137">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="a42ea-138">Le navigateur perd temporairement sa connexion à l’application pendant la compilation et le redémarrage.</span><span class="sxs-lookup"><span data-stu-id="a42ea-138">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="a42ea-139">La page dans le navigateur est automatiquement rechargée lorsque la connexion est rétablie.</span><span class="sxs-lookup"><span data-stu-id="a42ea-139">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="a42ea-140">Ajoutez un `TodoItem.cs` fichier à la racine du projet (le `TodoList` dossier) pour contenir une classe qui représente un élément TODO.</span><span class="sxs-lookup"><span data-stu-id="a42ea-140">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="a42ea-141">Utilisez le code C# suivant pour la classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="a42ea-141">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="a42ea-142">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="a42ea-142">`TodoItem.cs`:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="a42ea-143">Revenez au `Todo` composant et effectuez les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="a42ea-143">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="a42ea-144">Ajoutez un champ pour les éléments todo dans le `@code` bloc.</span><span class="sxs-lookup"><span data-stu-id="a42ea-144">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="a42ea-145">Le composant `Todo` utilise ce champ pour maintenir l’état de la liste de tâches.</span><span class="sxs-lookup"><span data-stu-id="a42ea-145">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="a42ea-146">Ajoutez un balisage de liste non triée et une boucle `foreach` pour effectuer le rendu de chaque élément todo en tant qu’élément de liste (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="a42ea-146">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="a42ea-147">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="a42ea-147">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,13)]

1. <span data-ttu-id="a42ea-148">L’application nécessite des éléments d’interface utilisateur pour ajouter des éléments todo à la liste.</span><span class="sxs-lookup"><span data-stu-id="a42ea-148">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="a42ea-149">Ajoutez une entrée de texte (`<input>`) et un bouton (`<button>`) sous la liste non ordonnée (`<ul>...</ul>`) :</span><span class="sxs-lookup"><span data-stu-id="a42ea-149">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="a42ea-150">Enregistrez le `TodoItem.cs` fichier et le fichier mis à jour `Pages/Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="a42ea-150">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="a42ea-151">Dans l’interface de commande, l’application est automatiquement reconstruite lors de l’enregistrement des fichiers.</span><span class="sxs-lookup"><span data-stu-id="a42ea-151">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="a42ea-152">Le navigateur perd temporairement sa connexion à l’application, puis recharge la page lorsque la connexion est rétablie.</span><span class="sxs-lookup"><span data-stu-id="a42ea-152">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="a42ea-153">Lorsque le **`Add todo`** bouton est sélectionné, rien ne se produit, car un gestionnaire d’événements n’est pas attaché au bouton.</span><span class="sxs-lookup"><span data-stu-id="a42ea-153">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="a42ea-154">Ajoutez une `AddTodo` méthode au `Todo` composant et enregistrez la méthode pour le bouton à l’aide de l' `@onclick` attribut.</span><span class="sxs-lookup"><span data-stu-id="a42ea-154">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="a42ea-155">La méthode C# `AddTodo` est appelée lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="a42ea-155">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="a42ea-156">Pour obtenir le titre du nouvel élément TODO, ajoutez un `newTodo` champ de chaîne en haut du `@code` bloc :</span><span class="sxs-lookup"><span data-stu-id="a42ea-156">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=3)]

   <span data-ttu-id="a42ea-157">Modifiez l' `<input>` élément de texte pour établir une liaison `newTodo` avec l' `@bind` attribut :</span><span class="sxs-lookup"><span data-stu-id="a42ea-157">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="a42ea-158">Mettez à jour la méthode `AddTodo` pour ajouter `TodoItem` avec le titre spécifié à la liste.</span><span class="sxs-lookup"><span data-stu-id="a42ea-158">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="a42ea-159">Supprimez la valeur du texte d’entrée en définissant `newTodo` sur une chaîne vide :</span><span class="sxs-lookup"><span data-stu-id="a42ea-159">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="a42ea-160">Enregistrez le fichier `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="a42ea-160">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="a42ea-161">L’application est automatiquement reconstruite dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="a42ea-161">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="a42ea-162">La page se recharge dans le navigateur une fois que le navigateur s’est reconnecté à l’application.</span><span class="sxs-lookup"><span data-stu-id="a42ea-162">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="a42ea-163">Le texte du titre pour chaque élément todo peut être rendu modifiable et une case à cocher peut aider l’utilisateur à effectuer le suivi des éléments terminés.</span><span class="sxs-lookup"><span data-stu-id="a42ea-163">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="a42ea-164">Ajoutez une entrée de case à cocher pour chaque élément todo et liez sa valeur à la propriété `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="a42ea-164">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="a42ea-165">Basculer `@todo.Title` vers un `<input>` élément lié à `todo.Title` avec `@bind` :</span><span class="sxs-lookup"><span data-stu-id="a42ea-165">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=4-7)]

1. <span data-ttu-id="a42ea-166">Mettez à jour l' `<h3>` en-tête pour afficher le nombre d’éléments todo qui ne sont pas complets (a la valeur `IsDone` `false` ).</span><span class="sxs-lookup"><span data-stu-id="a42ea-166">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="a42ea-167">Le `Todo` composant terminé ( `Pages/Todo.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="a42ea-167">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="a42ea-168">Enregistrez le fichier `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="a42ea-168">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="a42ea-169">L’application est automatiquement reconstruite dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="a42ea-169">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="a42ea-170">La page se recharge dans le navigateur une fois que le navigateur s’est reconnecté à l’application.</span><span class="sxs-lookup"><span data-stu-id="a42ea-170">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="a42ea-171">Ajoutez des éléments, modifiez des éléments et marquez les éléments todo effectués pour tester le composant.</span><span class="sxs-lookup"><span data-stu-id="a42ea-171">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="a42ea-172">Lorsque vous avez terminé, arrêtez l’application dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="a42ea-172">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="a42ea-173">De nombreux interpréteurs de commandes acceptent la commande de clavier <kbd>CTRL</kbd> + <kbd>c</kbd> pour arrêter une application.</span><span class="sxs-lookup"><span data-stu-id="a42ea-173">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a42ea-174">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="a42ea-174">Next steps</span></span>

<span data-ttu-id="a42ea-175">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="a42ea-175">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a42ea-176">Créer un projet d’application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="a42ea-176">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="a42ea-177">Modifier les Razor composants</span><span class="sxs-lookup"><span data-stu-id="a42ea-177">Modify Razor components</span></span>
> * <span data-ttu-id="a42ea-178">Utiliser la gestion des événements et la liaison de données dans les composants</span><span class="sxs-lookup"><span data-stu-id="a42ea-178">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="a42ea-179">Utiliser le routage dans une Blazor application</span><span class="sxs-lookup"><span data-stu-id="a42ea-179">Use routing in a Blazor app</span></span>

<span data-ttu-id="a42ea-180">En savoir plus sur les outils pour ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="a42ea-180">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
