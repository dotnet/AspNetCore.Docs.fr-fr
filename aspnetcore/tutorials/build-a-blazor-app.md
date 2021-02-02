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
ms.openlocfilehash: 106e1119db777074b5eae24f5d7e216e6127ca13
ms.sourcegitcommit: 75db2f684a9302b0be7925eab586aa091c6bd19f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99238306"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="e6c5a-103">Créer une Blazor application de liste de tâches</span><span class="sxs-lookup"><span data-stu-id="e6c5a-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="e6c5a-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e6c5a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e6c5a-105">Ce didacticiel vous montre comment créer et modifier une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="e6c5a-106">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e6c5a-107">Créer un projet d’application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="e6c5a-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="e6c5a-108">Modifier les Razor composants</span><span class="sxs-lookup"><span data-stu-id="e6c5a-108">Modify Razor components</span></span>
> * <span data-ttu-id="e6c5a-109">Utiliser la gestion des événements et la liaison de données dans les composants</span><span class="sxs-lookup"><span data-stu-id="e6c5a-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="e6c5a-110">Utiliser le routage dans une Blazor application</span><span class="sxs-lookup"><span data-stu-id="e6c5a-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="e6c5a-111">À la fin de ce didacticiel, vous disposerez d’une application de liste de tâches en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e6c5a-112">Prérequis</span><span class="sxs-lookup"><span data-stu-id="e6c5a-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="e6c5a-113">Créer une application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="e6c5a-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="e6c5a-114">Créez une Blazor application nommée `TodoList` dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="e6c5a-115">La commande précédente crée un dossier nommé `TodoList` avec l' `-o|--output` option de stockage de l’application.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-115">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="e6c5a-116">Le `TodoList` dossier est le *dossier racine* du projet.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="e6c5a-117">Accédez au dossier à l' `TodoList` aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="e6c5a-118">Ajoutez un nouveau `Todo` Razor composant à l’application à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-118">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="e6c5a-119">L' `-n|--name` option de la commande précédente spécifie le nom du nouveau Razor composant.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-119">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="e6c5a-120">Le nouveau composant est créé dans le dossier du projet `Pages` avec l' `-o|--output` option.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-120">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="e6c5a-121">Razor les noms de fichiers de composant requièrent une première lettre capitalisée.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-121">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="e6c5a-122">Ouvrez le `Pages` dossier et vérifiez que le `Todo` nom de fichier du composant commence par une lettre majuscule `T` .</span><span class="sxs-lookup"><span data-stu-id="e6c5a-122">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="e6c5a-123">Le nom de fichier doit être `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="e6c5a-123">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="e6c5a-124">Ouvrez le `Todo` composant dans n’importe quel éditeur de fichier et ajoutez une `@page` Razor directive en haut du fichier avec une URL relative de `/todo` .</span><span class="sxs-lookup"><span data-stu-id="e6c5a-124">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="e6c5a-125">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="e6c5a-125">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo0.razor?highlight=1)]

   <span data-ttu-id="e6c5a-126">Enregistrez le fichier `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-126">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="e6c5a-127">Ajoutez le composant `Todo` à la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-127">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="e6c5a-128">Le `NavMenu` composant est utilisé dans la disposition de l’application.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-128">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="e6c5a-129">Les dispositions sont des composants qui vous permettent d’éviter la duplication de contenu dans une application.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-129">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="e6c5a-130">Le `NavLink` composant fournit une pile dans l’interface utilisateur de l’application lorsque l’URL du composant est chargée par l’application.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-130">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="e6c5a-131">Dans la liste non triée ( `<ul>...</ul>` ) du `NavMenu` composant, ajoutez l’élément de liste ( `<li>...</li>` ) et le `NavLink` composant suivants pour le composant `Todo` .</span><span class="sxs-lookup"><span data-stu-id="e6c5a-131">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="e6c5a-132">Dans `Shared/NavMenu.razor` :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-132">In `Shared/NavMenu.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   <span data-ttu-id="e6c5a-133">Enregistrez le fichier `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-133">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="e6c5a-134">Générez et exécutez l’application en exécutant la [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) commande dans l’interface de commande à partir du `TodoList` dossier.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-134">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="e6c5a-135">Une fois l’application en cours d’exécution, accédez à la nouvelle page todo en sélectionnant le **`Todo`** lien dans la barre de navigation de l’application, qui charge la page à l’adresse `/todo` .</span><span class="sxs-lookup"><span data-stu-id="e6c5a-135">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="e6c5a-136">Laissez l’application s’exécuter dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-136">Leave the app running the command shell.</span></span> <span data-ttu-id="e6c5a-137">Chaque fois qu’un fichier est enregistré, l’application est automatiquement reconstruite.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-137">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="e6c5a-138">Le navigateur perd temporairement sa connexion à l’application pendant la compilation et le redémarrage.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-138">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="e6c5a-139">La page dans le navigateur est automatiquement rechargée lorsque la connexion est rétablie.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-139">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="e6c5a-140">Ajoutez un `TodoItem.cs` fichier à la racine du projet (le `TodoList` dossier) pour contenir une classe qui représente un élément TODO.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-140">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="e6c5a-141">Utilisez le code C# suivant pour la classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-141">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="e6c5a-142">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="e6c5a-142">`TodoItem.cs`:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]
   
   > [!NOTE]
   > <span data-ttu-id="e6c5a-143">Si vous utilisez Visual Studio pour créer le `ToDoItem.cs` fichier et la `ToDoItem` classe, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-143">If using Visual Studio to create the `ToDoItem.cs` file and `ToDoItem` class, use either of the following approaches:</span></span>
   >
   > * <span data-ttu-id="e6c5a-144">Supprimez l’espace de noms généré par Visual Studio pour la classe.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-144">Remove the namespace that Visual Studio generates for the class.</span></span>
   > * <span data-ttu-id="e6c5a-145">Utilisez le bouton **copier** dans le bloc de code précédent et remplacez l’intégralité du contenu du fichier généré par Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-145">Use the **Copy** button in the preceding code block and replace the entire contents of the file that Visual Studio generates.</span></span>

1. <span data-ttu-id="e6c5a-146">Revenez au `Todo` composant et effectuez les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-146">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="e6c5a-147">Ajoutez un champ pour les éléments todo dans le `@code` bloc.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-147">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="e6c5a-148">Le composant `Todo` utilise ce champ pour maintenir l’état de la liste de tâches.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-148">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="e6c5a-149">Ajoutez un balisage de liste non triée et une boucle `foreach` pour effectuer le rendu de chaque élément todo en tant qu’élément de liste (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="e6c5a-149">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="e6c5a-150">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="e6c5a-150">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo2.razor?highlight=5-10,13)]

1. <span data-ttu-id="e6c5a-151">L’application nécessite des éléments d’interface utilisateur pour ajouter des éléments todo à la liste.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-151">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="e6c5a-152">Ajoutez une entrée de texte (`<input>`) et un bouton (`<button>`) sous la liste non ordonnée (`<ul>...</ul>`) :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-152">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo3.razor?highlight=12-13)]

1. <span data-ttu-id="e6c5a-153">Enregistrez le `TodoItem.cs` fichier et le fichier mis à jour `Pages/Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="e6c5a-153">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="e6c5a-154">Dans l’interface de commande, l’application est automatiquement reconstruite lors de l’enregistrement des fichiers.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-154">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="e6c5a-155">Le navigateur perd temporairement sa connexion à l’application, puis recharge la page lorsque la connexion est rétablie.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-155">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="e6c5a-156">Lorsque le **`Add todo`** bouton est sélectionné, rien ne se produit, car un gestionnaire d’événements n’est pas attaché au bouton.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-156">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="e6c5a-157">Ajoutez une `AddTodo` méthode au `Todo` composant et enregistrez la méthode pour le bouton à l’aide de l' `@onclick` attribut.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-157">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="e6c5a-158">La méthode C# `AddTodo` est appelée lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-158">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="e6c5a-159">Pour obtenir le titre du nouvel élément TODO, ajoutez un `newTodo` champ de chaîne en haut du `@code` bloc :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-159">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo5.razor?highlight=3)]

   <span data-ttu-id="e6c5a-160">Modifiez l' `<input>` élément de texte pour établir une liaison `newTodo` avec l' `@bind` attribut :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-160">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="e6c5a-161">Mettez à jour la méthode `AddTodo` pour ajouter `TodoItem` avec le titre spécifié à la liste.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-161">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="e6c5a-162">Supprimez la valeur du texte d’entrée en définissant `newTodo` sur une chaîne vide :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-162">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo6.razor?highlight=19-26)]

1. <span data-ttu-id="e6c5a-163">Enregistrez le fichier `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-163">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="e6c5a-164">L’application est automatiquement reconstruite dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-164">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="e6c5a-165">La page se recharge dans le navigateur une fois que le navigateur s’est reconnecté à l’application.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-165">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="e6c5a-166">Le texte du titre pour chaque élément todo peut être rendu modifiable et une case à cocher peut aider l’utilisateur à effectuer le suivi des éléments terminés.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-166">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="e6c5a-167">Ajoutez une entrée de case à cocher pour chaque élément todo et liez sa valeur à la propriété `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-167">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="e6c5a-168">Basculer `@todo.Title` vers un `<input>` élément lié à `todo.Title` avec `@bind` :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-168">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo7.razor?highlight=4-7)]

1. <span data-ttu-id="e6c5a-169">Mettez à jour l' `<h3>` en-tête pour afficher le nombre d’éléments todo qui ne sont pas complets (a la valeur `IsDone` `false` ).</span><span class="sxs-lookup"><span data-stu-id="e6c5a-169">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="e6c5a-170">Le `Todo` composant terminé ( `Pages/Todo.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-170">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="e6c5a-171">Enregistrez le fichier `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-171">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="e6c5a-172">L’application est automatiquement reconstruite dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-172">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="e6c5a-173">La page se recharge dans le navigateur une fois que le navigateur s’est reconnecté à l’application.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-173">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="e6c5a-174">Ajoutez des éléments, modifiez des éléments et marquez les éléments todo effectués pour tester le composant.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-174">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="e6c5a-175">Lorsque vous avez terminé, arrêtez l’application dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-175">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="e6c5a-176">De nombreux interpréteurs de commandes acceptent la commande de clavier <kbd>CTRL</kbd> + <kbd>c</kbd> pour arrêter une application.</span><span class="sxs-lookup"><span data-stu-id="e6c5a-176">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e6c5a-177">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="e6c5a-177">Next steps</span></span>

<span data-ttu-id="e6c5a-178">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-178">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e6c5a-179">Créer un projet d’application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="e6c5a-179">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="e6c5a-180">Modifier les Razor composants</span><span class="sxs-lookup"><span data-stu-id="e6c5a-180">Modify Razor components</span></span>
> * <span data-ttu-id="e6c5a-181">Utiliser la gestion des événements et la liaison de données dans les composants</span><span class="sxs-lookup"><span data-stu-id="e6c5a-181">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="e6c5a-182">Utiliser le routage dans une Blazor application</span><span class="sxs-lookup"><span data-stu-id="e6c5a-182">Use routing in a Blazor app</span></span>

<span data-ttu-id="e6c5a-183">En savoir plus sur les outils pour ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="e6c5a-183">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
