---
title: Créer une Blazor application de liste de tâches
author: guardrex
description: Générez une Blazor application pas à pas.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2020
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
ms.openlocfilehash: 68a38b82f5a89365e4f345a60f1f34b697c027ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060090"
---
# <a name="build-a-no-locblazor-todo-list-app"></a>Créer une Blazor application de liste de tâches

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

Ce didacticiel vous montre comment créer et modifier une Blazor application. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet d’application de liste de tâches Blazor
> * Modifier les Razor composants
> * Utiliser la gestion des événements et la liaison de données dans les composants
> * Utiliser le routage dans une Blazor application

À la fin de ce didacticiel, vous disposerez d’une application de liste de tâches en cours d’utilisation.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-todo-list-no-locblazor-app"></a>Créer une application de liste de tâches Blazor

1. Créez une Blazor application nommée `TodoList` dans une interface de commande :

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   La commande précédente crée un dossier nommé `TodoList` pour contenir l’application. Le `TodoList` dossier est le *dossier racine* du projet. Accédez au dossier à l' `TodoList` aide de la commande suivante :

   ```dotnetcli
   cd TodoList
   ```

1. Ajoutez un nouveau `Todo` Razor composant à l’application dans le `Pages` dossier à l’aide de la commande suivante :

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor les noms de fichiers de composant requièrent une première lettre capitalisée. Ouvrez le `Pages` dossier et vérifiez que le `Todo` nom de fichier du composant commence par une lettre majuscule `T` . Le nom de fichier doit être `Todo.razor` .

1. Dans `Pages/Todo.razor` fournir le balisage initial pour le composant :

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Ajoutez le composant `Todo` à la barre de navigation.

   Le `NavMenu` composant ( `Shared/NavMenu.razor` ) est utilisé dans la disposition de l’application. Les dispositions sont des composants qui vous permettent d’éviter la duplication de contenu dans l’application.

   Ajoutez un `<NavLink>` élément pour le `Todo` composant en ajoutant la balise d’élément de liste suivante sous les éléments de liste existants dans le `Shared/NavMenu.razor` fichier :

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Générez et exécutez l’application en exécutant la `dotnet run` commande dans l’interface de commande à partir du `TodoList` dossier. Consultez la nouvelle page Todo pour vérifier que le lien vers le composant `Todo` fonctionne.

1. Ajoutez un `TodoItem.cs` fichier à la racine du projet (le `TodoList` dossier) pour contenir une classe qui représente un élément TODO. Utilisez le code C# suivant pour la classe `TodoItem` :

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Revenez au `Todo` composant ( `Pages/Todo.razor` ) :

   * Ajoutez un champ pour les éléments Todo dans un bloc `@code`. Le composant `Todo` utilise ce champ pour maintenir l’état de la liste de tâches.
   * Ajoutez un balisage de liste non triée et une boucle `foreach` pour effectuer le rendu de chaque élément todo en tant qu’élément de liste (`<li>`).

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. L’application nécessite des éléments d’interface utilisateur pour ajouter des éléments todo à la liste. Ajoutez une entrée de texte (`<input>`) et un bouton (`<button>`) sous la liste non ordonnée (`<ul>...</ul>`) :

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Arrêtez l’application en cours d’exécution dans l’interface de commande. De nombreux interpréteurs de commandes acceptent la commande de clavier <kbd>CTRL</kbd> + <kbd>c</kbd> pour arrêter une application. Régénérez et exécutez l’application avec la `dotnet run` commande. Lorsque le **`Add todo`** bouton est sélectionné, rien ne se produit, car un gestionnaire d’événements n’est pas relié au bouton.

1. Ajoutez une méthode `AddTodo` au composant `Todo` et enregistrez-la pour les sélections du bouton à l’aide de l’attribut `@onclick`. La méthode C# `AddTodo` est appelée lorsque le bouton est sélectionné :

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Pour obtenir le titre du nouvel élément todo, ajoutez un champ de chaîne `newTodo` en haut du bloc `@code` et liez-le à la valeur du texte d’entrée à l’aide de l’attribut `bind` dans l’élément `<input>` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Mettez à jour la méthode `AddTodo` pour ajouter `TodoItem` avec le titre spécifié à la liste. Supprimez la valeur du texte d’entrée en définissant `newTodo` sur une chaîne vide :

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Arrêtez l’application en cours d’exécution dans l’interface de commande. Régénérez et exécutez l’application avec la `dotnet run` commande. Ajoutez quelques éléments todo à la liste Todo pour tester le nouveau code.

1. Le texte du titre pour chaque élément todo peut être rendu modifiable et une case à cocher peut aider l’utilisateur à effectuer le suivi des éléments terminés. Ajoutez une entrée de case à cocher pour chaque élément todo et liez sa valeur à la propriété `IsDone`. Remplacez `@todo.Title` par un élément `<input>` lié à `@todo.Title` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Pour vérifier que ces valeurs sont liées, mettez à jour l’en-tête `<h3>` pour afficher le nombre d’éléments todo qui ne sont pas terminés (`IsDone` est `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Le `Todo` composant terminé ( `Pages/Todo.razor` ) :

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Arrêtez l’application en cours d’exécution dans l’interface de commande. Régénérez et exécutez l’application avec la `dotnet run` commande. Ajoutez des éléments todo pour tester le nouveau code.

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
