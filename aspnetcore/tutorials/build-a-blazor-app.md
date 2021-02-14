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
ms.openlocfilehash: 939841ca7214e212a2f197ea1e00b0f6152c471e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280510"
---
# <a name="build-a-blazor-todo-list-app"></a>Créer une Blazor application de liste de tâches

Ce didacticiel vous montre comment créer et modifier une Blazor application. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet d’application de liste de tâches Blazor
> * Modifier les Razor composants
> * Utiliser la gestion des événements et la liaison de données dans les composants
> * Utiliser le routage dans une Blazor application

À la fin de ce didacticiel, vous disposerez d’une application de liste de tâches en cours d’utilisation.

## <a name="prerequisites"></a>Prérequis

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a>Créer une application de liste de tâches Blazor

1. Créez une Blazor application nommée `TodoList` dans une interface de commande :

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   La commande précédente crée un dossier nommé `TodoList` avec l' `-o|--output` option de stockage de l’application. Le `TodoList` dossier est le *dossier racine* du projet. Accédez au dossier à l' `TodoList` aide de la commande suivante :

   ```dotnetcli
   cd TodoList
   ```

1. Ajoutez un nouveau `Todo` Razor composant à l’application à l’aide de la commande suivante :

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   L' `-n|--name` option de la commande précédente spécifie le nom du nouveau Razor composant. Le nouveau composant est créé dans le dossier du projet `Pages` avec l' `-o|--output` option.

   > [!IMPORTANT]
   > Razor les noms de fichiers de composant requièrent une première lettre capitalisée. Ouvrez le `Pages` dossier et vérifiez que le `Todo` nom de fichier du composant commence par une lettre majuscule `T` . Le nom de fichier doit être `Todo.razor` .

1. Ouvrez le `Todo` composant dans n’importe quel éditeur de fichier et ajoutez une `@page` Razor directive en haut du fichier avec une URL relative de `/todo` .

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   Enregistrez le fichier `Pages/Todo.razor`.

1. Ajoutez le composant `Todo` à la barre de navigation.

   Le `NavMenu` composant est utilisé dans la disposition de l’application. Les dispositions sont des composants qui vous permettent d’éviter la duplication de contenu dans une application. Le `NavLink` composant fournit une pile dans l’interface utilisateur de l’application lorsque l’URL du composant est chargée par l’application.

   Dans la liste non triée ( `<ul>...</ul>` ) du `NavMenu` composant, ajoutez l’élément de liste ( `<li>...</li>` ) et le `NavLink` composant suivants pour le composant `Todo` .

   Dans `Shared/NavMenu.razor` :

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   Enregistrez le fichier `Shared/NavMenu.razor`.

1. Générez et exécutez l’application en exécutant la [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) commande dans l’interface de commande à partir du `TodoList` dossier. Une fois l’application en cours d’exécution, accédez à la nouvelle page todo en sélectionnant le **`Todo`** lien dans la barre de navigation de l’application, qui charge la page à l’adresse `/todo` .

   Laissez l’application s’exécuter dans l’interface de commande. Chaque fois qu’un fichier est enregistré, l’application est automatiquement reconstruite. Le navigateur perd temporairement sa connexion à l’application pendant la compilation et le redémarrage. La page dans le navigateur est automatiquement rechargée lorsque la connexion est rétablie.

1. Ajoutez un `TodoItem.cs` fichier à la racine du projet (le `TodoList` dossier) pour contenir une classe qui représente un élément TODO. Utilisez le code C# suivant pour la classe `TodoItem`.

   `TodoItem.cs`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   > [!NOTE]
   > Si vous utilisez Visual Studio pour créer le `ToDoItem.cs` fichier et la `ToDoItem` classe, utilisez l’une des approches suivantes :
   >
   > * Supprimez l’espace de noms généré par Visual Studio pour la classe.
   > * Utilisez le bouton **copier** dans le bloc de code précédent et remplacez l’intégralité du contenu du fichier généré par Visual Studio.

1. Revenez au `Todo` composant et effectuez les tâches suivantes :

   * Ajoutez un champ pour les éléments todo dans le `@code` bloc. Le composant `Todo` utilise ce champ pour maintenir l’état de la liste de tâches.
   * Ajoutez un balisage de liste non triée et une boucle `foreach` pour effectuer le rendu de chaque élément todo en tant qu’élément de liste (`<li>`).

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

1. L’application nécessite des éléments d’interface utilisateur pour ajouter des éléments todo à la liste. Ajoutez une entrée de texte (`<input>`) et un bouton (`<button>`) sous la liste non ordonnée (`<ul>...</ul>`) :

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

1. Enregistrez le `TodoItem.cs` fichier et le fichier mis à jour `Pages/Todo.razor` . Dans l’interface de commande, l’application est automatiquement reconstruite lors de l’enregistrement des fichiers. Le navigateur perd temporairement sa connexion à l’application, puis recharge la page lorsque la connexion est rétablie.

1. Lorsque le **`Add todo`** bouton est sélectionné, rien ne se produit, car un gestionnaire d’événements n’est pas attaché au bouton.

1. Ajoutez une `AddTodo` méthode au `Todo` composant et enregistrez la méthode pour le bouton à l’aide de l' `@onclick` attribut. La méthode C# `AddTodo` est appelée lorsque le bouton est sélectionné :

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

1. Pour obtenir le titre du nouvel élément TODO, ajoutez un `newTodo` champ de chaîne en haut du `@code` bloc :

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   Modifiez l' `<input>` élément de texte pour établir une liaison `newTodo` avec l' `@bind` attribut :

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Mettez à jour la méthode `AddTodo` pour ajouter `TodoItem` avec le titre spécifié à la liste. Supprimez la valeur du texte d’entrée en définissant `newTodo` sur une chaîne vide :

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

1. Enregistrez le fichier `Pages/Todo.razor`. L’application est automatiquement reconstruite dans l’interface de commande. La page se recharge dans le navigateur une fois que le navigateur s’est reconnecté à l’application.

1. Le texte du titre pour chaque élément todo peut être rendu modifiable et une case à cocher peut aider l’utilisateur à effectuer le suivi des éléments terminés. Ajoutez une entrée de case à cocher pour chaque élément todo et liez sa valeur à la propriété `IsDone`. Basculer `@todo.Title` vers un `<input>` élément lié à `todo.Title` avec `@bind` :

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

1. Mettez à jour l' `<h3>` en-tête pour afficher le nombre d’éléments todo qui ne sont pas complets (a la valeur `IsDone` `false` ).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Le `Todo` composant terminé ( `Pages/Todo.razor` ) :

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

1. Enregistrez le fichier `Pages/Todo.razor`. L’application est automatiquement reconstruite dans l’interface de commande. La page se recharge dans le navigateur une fois que le navigateur s’est reconnecté à l’application.

1. Ajoutez des éléments, modifiez des éléments et marquez les éléments todo effectués pour tester le composant.

1. Lorsque vous avez terminé, arrêtez l’application dans l’interface de commande. De nombreux interpréteurs de commandes acceptent la commande de clavier <kbd>CTRL</kbd> + <kbd>c</kbd> pour arrêter une application.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un projet d’application de liste de tâches Blazor
> * Modifier les Razor composants
> * Utiliser la gestion des événements et la liaison de données dans les composants
> * Utiliser le routage dans une Blazor application

En savoir plus sur les outils pour ASP.NET Core Blazor :

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
