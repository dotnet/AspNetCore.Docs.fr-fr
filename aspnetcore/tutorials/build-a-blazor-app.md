---
title: Créer une Blazor application de liste de tâches
author: guardrex
description: Générez une Blazor application pas à pas.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2021
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
ms.openlocfilehash: d984023a1c46c5383d47a1634c54e61747b83d60
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101208"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="dafae-103">Créer une Blazor application de liste de tâches</span><span class="sxs-lookup"><span data-stu-id="dafae-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="dafae-104">Ce didacticiel vous montre comment créer et modifier une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="dafae-104">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="dafae-105">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="dafae-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dafae-106">Créer un projet d’application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="dafae-106">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="dafae-107">Modifier les Razor composants</span><span class="sxs-lookup"><span data-stu-id="dafae-107">Modify Razor components</span></span>
> * <span data-ttu-id="dafae-108">Utiliser la gestion des événements et la liaison de données dans les composants</span><span class="sxs-lookup"><span data-stu-id="dafae-108">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="dafae-109">Utiliser le routage dans une Blazor application</span><span class="sxs-lookup"><span data-stu-id="dafae-109">Use routing in a Blazor app</span></span>

<span data-ttu-id="dafae-110">À la fin de ce didacticiel, vous disposerez d’une application de liste de tâches en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="dafae-110">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dafae-111">Prérequis</span><span class="sxs-lookup"><span data-stu-id="dafae-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a><span data-ttu-id="dafae-112">Créer une application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="dafae-112">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="dafae-113">Créez une Blazor application nommée `TodoList` dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="dafae-113">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="dafae-114">La commande précédente crée un dossier nommé `TodoList` avec l' `-o|--output` option de stockage de l’application.</span><span class="sxs-lookup"><span data-stu-id="dafae-114">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="dafae-115">Le `TodoList` dossier est le *dossier racine* du projet.</span><span class="sxs-lookup"><span data-stu-id="dafae-115">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="dafae-116">Accédez au dossier à l' `TodoList` aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="dafae-116">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="dafae-117">Ajoutez un nouveau `Todo` Razor composant à l’application à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="dafae-117">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="dafae-118">L' `-n|--name` option de la commande précédente spécifie le nom du nouveau Razor composant.</span><span class="sxs-lookup"><span data-stu-id="dafae-118">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="dafae-119">Le nouveau composant est créé dans le dossier du projet `Pages` avec l' `-o|--output` option.</span><span class="sxs-lookup"><span data-stu-id="dafae-119">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="dafae-120">Razor les noms de fichiers de composant requièrent une première lettre capitalisée.</span><span class="sxs-lookup"><span data-stu-id="dafae-120">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="dafae-121">Ouvrez le `Pages` dossier et vérifiez que le `Todo` nom de fichier du composant commence par une lettre majuscule `T` .</span><span class="sxs-lookup"><span data-stu-id="dafae-121">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="dafae-122">Le nom de fichier doit être `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="dafae-122">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="dafae-123">Ouvrez le `Todo` composant dans n’importe quel éditeur de fichier et ajoutez une `@page` Razor directive en haut du fichier avec une URL relative de `/todo` .</span><span class="sxs-lookup"><span data-stu-id="dafae-123">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="dafae-124">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="dafae-124">`Pages/Todo.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   <span data-ttu-id="dafae-125">Enregistrez le fichier `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="dafae-125">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="dafae-126">Ajoutez le composant `Todo` à la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="dafae-126">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="dafae-127">Le `NavMenu` composant est utilisé dans la disposition de l’application.</span><span class="sxs-lookup"><span data-stu-id="dafae-127">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="dafae-128">Les dispositions sont des composants qui vous permettent d’éviter la duplication de contenu dans une application.</span><span class="sxs-lookup"><span data-stu-id="dafae-128">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="dafae-129">Le `NavLink` composant fournit une pile dans l’interface utilisateur de l’application lorsque l’URL du composant est chargée par l’application.</span><span class="sxs-lookup"><span data-stu-id="dafae-129">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="dafae-130">Dans la liste non triée ( `<ul>...</ul>` ) du `NavMenu` composant, ajoutez l’élément de liste ( `<li>...</li>` ) et le `NavLink` composant suivants pour le composant `Todo` .</span><span class="sxs-lookup"><span data-stu-id="dafae-130">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="dafae-131">Dans `Shared/NavMenu.razor` :</span><span class="sxs-lookup"><span data-stu-id="dafae-131">In `Shared/NavMenu.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   <span data-ttu-id="dafae-132">Enregistrez le fichier `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="dafae-132">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="dafae-133">Générez et exécutez l’application en exécutant la [`dotnet watch run`](xref:tutorials/dotnet-watch) commande dans l’interface de commande à partir du `TodoList` dossier.</span><span class="sxs-lookup"><span data-stu-id="dafae-133">Build and run the app by executing the [`dotnet watch run`](xref:tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="dafae-134">Une fois l’application en cours d’exécution, accédez à la nouvelle page todo en sélectionnant le **`Todo`** lien dans la barre de navigation de l’application, qui charge la page à l’adresse `/todo` .</span><span class="sxs-lookup"><span data-stu-id="dafae-134">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="dafae-135">Laissez l’application s’exécuter dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="dafae-135">Leave the app running the command shell.</span></span> <span data-ttu-id="dafae-136">Chaque fois qu’un fichier est enregistré, l’application est automatiquement reconstruite.</span><span class="sxs-lookup"><span data-stu-id="dafae-136">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="dafae-137">Le navigateur perd temporairement sa connexion à l’application pendant la compilation et le redémarrage.</span><span class="sxs-lookup"><span data-stu-id="dafae-137">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="dafae-138">La page dans le navigateur est automatiquement rechargée lorsque la connexion est rétablie.</span><span class="sxs-lookup"><span data-stu-id="dafae-138">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="dafae-139">Ajoutez un `TodoItem.cs` fichier à la racine du projet (le `TodoList` dossier) pour contenir une classe qui représente un élément TODO.</span><span class="sxs-lookup"><span data-stu-id="dafae-139">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="dafae-140">Utilisez le code C# suivant pour la classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="dafae-140">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="dafae-141">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="dafae-141">`TodoItem.cs`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   > [!NOTE]
   > <span data-ttu-id="dafae-142">Si vous utilisez Visual Studio pour créer le `ToDoItem.cs` fichier et la `ToDoItem` classe, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="dafae-142">If using Visual Studio to create the `ToDoItem.cs` file and `ToDoItem` class, use either of the following approaches:</span></span>
   >
   > * <span data-ttu-id="dafae-143">Supprimez l’espace de noms généré par Visual Studio pour la classe.</span><span class="sxs-lookup"><span data-stu-id="dafae-143">Remove the namespace that Visual Studio generates for the class.</span></span>
   > * <span data-ttu-id="dafae-144">Utilisez le bouton **copier** dans le bloc de code précédent et remplacez l’intégralité du contenu du fichier généré par Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dafae-144">Use the **Copy** button in the preceding code block and replace the entire contents of the file that Visual Studio generates.</span></span>

1. <span data-ttu-id="dafae-145">Revenez au `Todo` composant et effectuez les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="dafae-145">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="dafae-146">Ajoutez un champ pour les éléments todo dans le `@code` bloc.</span><span class="sxs-lookup"><span data-stu-id="dafae-146">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="dafae-147">Le composant `Todo` utilise ce champ pour maintenir l’état de la liste de tâches.</span><span class="sxs-lookup"><span data-stu-id="dafae-147">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="dafae-148">Ajoutez un balisage de liste non triée et une boucle `foreach` pour effectuer le rendu de chaque élément todo en tant qu’élément de liste (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="dafae-148">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="dafae-149">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="dafae-149">`Pages/Todo.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

1. <span data-ttu-id="dafae-150">L’application nécessite des éléments d’interface utilisateur pour ajouter des éléments todo à la liste.</span><span class="sxs-lookup"><span data-stu-id="dafae-150">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="dafae-151">Ajoutez une entrée de texte (`<input>`) et un bouton (`<button>`) sous la liste non ordonnée (`<ul>...</ul>`) :</span><span class="sxs-lookup"><span data-stu-id="dafae-151">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

1. <span data-ttu-id="dafae-152">Enregistrez le `TodoItem.cs` fichier et le fichier mis à jour `Pages/Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="dafae-152">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="dafae-153">Dans l’interface de commande, l’application est automatiquement reconstruite lors de l’enregistrement des fichiers.</span><span class="sxs-lookup"><span data-stu-id="dafae-153">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="dafae-154">Le navigateur perd temporairement sa connexion à l’application, puis recharge la page lorsque la connexion est rétablie.</span><span class="sxs-lookup"><span data-stu-id="dafae-154">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="dafae-155">Lorsque le **`Add todo`** bouton est sélectionné, rien ne se produit, car un gestionnaire d’événements n’est pas attaché au bouton.</span><span class="sxs-lookup"><span data-stu-id="dafae-155">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="dafae-156">Ajoutez une `AddTodo` méthode au `Todo` composant et enregistrez la méthode pour le bouton à l’aide de l' `@onclick` attribut.</span><span class="sxs-lookup"><span data-stu-id="dafae-156">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="dafae-157">La méthode C# `AddTodo` est appelée lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="dafae-157">The `AddTodo` C# method is called when the button is selected:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

1. <span data-ttu-id="dafae-158">Pour obtenir le titre du nouvel élément TODO, ajoutez un `newTodo` champ de chaîne en haut du `@code` bloc :</span><span class="sxs-lookup"><span data-stu-id="dafae-158">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   <span data-ttu-id="dafae-159">Modifiez l' `<input>` élément de texte pour établir une liaison `newTodo` avec l' `@bind` attribut :</span><span class="sxs-lookup"><span data-stu-id="dafae-159">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="dafae-160">Mettez à jour la méthode `AddTodo` pour ajouter `TodoItem` avec le titre spécifié à la liste.</span><span class="sxs-lookup"><span data-stu-id="dafae-160">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="dafae-161">Supprimez la valeur du texte d’entrée en définissant `newTodo` sur une chaîne vide :</span><span class="sxs-lookup"><span data-stu-id="dafae-161">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

1. <span data-ttu-id="dafae-162">Enregistrez le fichier `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="dafae-162">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="dafae-163">L’application est automatiquement reconstruite dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="dafae-163">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="dafae-164">La page se recharge dans le navigateur une fois que le navigateur s’est reconnecté à l’application.</span><span class="sxs-lookup"><span data-stu-id="dafae-164">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="dafae-165">Le texte du titre pour chaque élément todo peut être rendu modifiable et une case à cocher peut aider l’utilisateur à effectuer le suivi des éléments terminés.</span><span class="sxs-lookup"><span data-stu-id="dafae-165">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="dafae-166">Ajoutez une entrée de case à cocher pour chaque élément todo et liez sa valeur à la propriété `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="dafae-166">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="dafae-167">Basculer `@todo.Title` vers un `<input>` élément lié à `todo.Title` avec `@bind` :</span><span class="sxs-lookup"><span data-stu-id="dafae-167">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

1. <span data-ttu-id="dafae-168">Mettez à jour l' `<h3>` en-tête pour afficher le nombre d’éléments todo qui ne sont pas complets (a la valeur `IsDone` `false` ).</span><span class="sxs-lookup"><span data-stu-id="dafae-168">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="dafae-169">Le `Todo` composant terminé ( `Pages/Todo.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="dafae-169">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

1. <span data-ttu-id="dafae-170">Enregistrez le fichier `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="dafae-170">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="dafae-171">L’application est automatiquement reconstruite dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="dafae-171">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="dafae-172">La page se recharge dans le navigateur une fois que le navigateur s’est reconnecté à l’application.</span><span class="sxs-lookup"><span data-stu-id="dafae-172">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="dafae-173">Ajoutez des éléments, modifiez des éléments et marquez les éléments todo effectués pour tester le composant.</span><span class="sxs-lookup"><span data-stu-id="dafae-173">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="dafae-174">Lorsque vous avez terminé, arrêtez l’application dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="dafae-174">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="dafae-175">De nombreux interpréteurs de commandes acceptent la commande de clavier <kbd>CTRL</kbd> + <kbd>c</kbd> pour arrêter une application.</span><span class="sxs-lookup"><span data-stu-id="dafae-175">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="dafae-176">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="dafae-176">Next steps</span></span>

<span data-ttu-id="dafae-177">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="dafae-177">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dafae-178">Créer un projet d’application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="dafae-178">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="dafae-179">Modifier les Razor composants</span><span class="sxs-lookup"><span data-stu-id="dafae-179">Modify Razor components</span></span>
> * <span data-ttu-id="dafae-180">Utiliser la gestion des événements et la liaison de données dans les composants</span><span class="sxs-lookup"><span data-stu-id="dafae-180">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="dafae-181">Utiliser le routage dans une Blazor application</span><span class="sxs-lookup"><span data-stu-id="dafae-181">Use routing in a Blazor app</span></span>

<span data-ttu-id="dafae-182">En savoir plus sur les outils pour ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="dafae-182">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
